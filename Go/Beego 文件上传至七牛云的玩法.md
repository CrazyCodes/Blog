![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/10/2978289583-5bd423e85207c_articlex.png)


# 前言

![](https://resources.blog.fastrun.cn/wp-content/uploads/2018/11/beego_purple.png)

Beego是一款GO语言开发的传统MVC的框架，beego对上传这块的代码封装的也非常简单易用。


# beego的上传

贴出官方的一段代码

https://beego.me/docs/mvc/controller/params.md#%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0

```
func (c *FormController) Post() {
    f, h, err := c.GetFile("uploadname")
    if err != nil {
        log.Fatal("getfile err ", err)
    }
    defer f.Close()
    c.SaveToFile("uploadname", "static/upload/" + h.Filename) // 保存位置在 static/upload, 没有文件夹要先创建
    
}
```

这里beego通过 GetFile方法获取文件的name，既设置的 ```input name="uploadname"``` 可以通过其第二个返回值获得文件的详细详细
```
h.Filename
h.Header
h.size
```
上面的例子是一个传统上传，既将文件拷贝到本地项目目录中。

# 七牛云
关于注册、登录、获取key什么的本文就不废话了。官方提供了Go的Sdk

文档地址：https://developer.qiniu.com/kodo/sdk/1238/go

## 安装
通过go get 安装包
```
go get -u github.com/qiniu/api.v7
```

## 使用
这里演示两种上传方式
七牛官方的SDK提供了几个上传方法，如下所示
```
// 本地文件上传至七牛云
func (p *FormUploader) PutFile
func (p *FormUploader) PutFileWithoutKey
// 数据流方式上传至七牛云
func (p *FormUploader) Put
func (p *FormUploader) PutWithoutKey
```

这个SDK也允许你自定义返回结果,通过重写结构体的方式
```
type PutRet struct {
	Hash         string `json:"hash"`
	PersistentID string `json:"persistentId"`
	Key          string `json:"key"`
}
```

### 本地上传
这是官方的一个demo,本地上传这里就不多阐述了，大概都能看懂
```
// 设置上传文件
localFile = "/Users/jemy/Documents/github.png"
// 设置上传空间名
bucket = "if-pbl"
// 上传的文件名称
key = "github-x.png"

putPolicy := storage.PutPolicy{
    Scope:               bucket,
}
mac := qbox.NewMac(accessKey, secretKey)
upToken := putPolicy.UploadToken(mac)

cfg := storage.Config{}
// 空间对应的机房
cfg.Zone = &storage.ZoneHuadong
// 是否使用https域名
cfg.UseHTTPS = false
// 上传是否使用CDN上传加速
cfg.UseCdnDomains = false

// 构建表单上传的对象
formUploader := storage.NewFormUploader(&cfg)
ret := storage.PutRet{}

// 可选配置
putExtra := storage.PutExtra{
    Params: map[string]string{
        "x:name": "github logo",
    },
}
err := formUploader.PutFile(context.Background(), &ret, upToken, key, localFile, &putExtra)
if err != nil {
    fmt.Println(err)
    return
}
fmt.Println(ret.Key,ret.Hash)

```

### 流信息上传
这是我自行封装的流上传代码
```
package qiniu

import (
	"github.com/qiniu/api.v7/storage"
	"github.com/qiniu/api.v7/auth/qbox"
	"context"
	"io"
)

const (
	bucket    = "avatars" // 
	accessKey = ""
	secretKey = ""
)

func config() storage.Config {
	cfg := storage.Config{}

	cfg.Zone = &storage.ZoneHuadong
	// 是否使用https域名
	cfg.UseHTTPS = false
	// 上传是否使用CDN上传加速
	cfg.UseCdnDomains = false

	return cfg
}

func Upload(localFile io.Reader, size int64, filename string) (string, error) {

	putPolicy := storage.PutPolicy{
		Scope: bucket,
	}

	mac := qbox.NewMac(accessKey, secretKey)
	upToken := putPolicy.UploadToken(mac)

	cig := config()

	formUploader := storage.NewFormUploader(&cig)

	ret := storage.PutRet{}

	putExtra := storage.PutExtra{}

	err := formUploader.Put(context.Background(), &ret, upToken, filename, localFile, size, &putExtra)

	if err != nil {
		return "", err
	}

	return ret.Key, nil
}

```
注意```localFile io.Reader```这个参数，调用upload方法需要传一个byte。下面演示调用方法的代码

```
func (u *UserController) UploadImage() {
	f, h, err := u.GetFile("image")

	if err != nil {
		u.Data["json"] = common.Response{
			Data:    [0]int{},
			Message: "上传失败",
			Code:    0,
		}

		u.ServeJSON()

		return
	}


	defer f.Close()

	file, _ := h.Open() // 这里获得的实际就是一个io,通过源码看到这个open方法最终返回的是一个结构体,其内部包含了 io.Reader的接口
	
	// type File interface {
		// io.Reader
		// io.ReaderAt
		// io.Seeker
		// io.Closer
	// }


	unix := time.Now().Unix()

	timeByte := []byte(strconv.Itoa(int(unix)))

	filename := function.Md5(timeByte) // 这里是计算了一个md5(time())的字符串作为文件名

	if filename, err = qiniu.Upload(file, h.Size, filename); err != nil { // 通过h.size 即可获得文件大小
		u.Data["json"] = common.Response{
			Data:    [0]int{},
			Message: "上传失败",
			Code:    0,
		}
	} else {
		u.Data["json"] = common.Response{
			Data: map[string]string{
				"filename": filename,
			},
			Message: "上传成功",
			Code:    200,
		}
	}

	u.ServeJSON()
}


```

# 致谢
go 自身的特点不应是做web，web使用php，java就够了。一般用go都是写接口。所以上传文件时不应先传到本地再传到七牛，我想很多初学者都在这里被坑。特此写一篇来解释其使用方法。

希望本篇文章可以帮到你。谢谢



