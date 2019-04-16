![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/04/3050921220-5c98c7232c6c4_articlex.png)

# 前言
> 我一生的文章都会放在这里，我的博客，我希望每一行代码，每一段文字都能帮助你。https://github.com/CrazyCodes/Blog

大家好，我是CrazyCodes，今天我们来聊聊50%（不完全统计，不必纠结比例 😆）的程序员都感觉没有啥用的数据库测试。

实际测试是重中之重，正常下来一个需求应当先写测试用例后实现功能代码，如果没有在开发前做测试，那你可以选择写一个错误的断言，使用错误断言来验证代码是否符合预期，而不是根据功能去写测试，这是写测试的一种逆向思维。

# 啥是数据库测试？
很多人可能玩过单元测试，设定呀，断言呀，等等条件。但单元测试具有局限性，现如今大部分代码与数据库耦合度较高，无法独立进行单元测试，例如要做了登录模块，大概逻辑如下

![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/04/1160511531-5c98bdc3cc045_articlex.png)

那可以用单元测试的地方有哪些呢？
1. 对用户名添加正则表达式（或者是规则）进行单元测试
2. 对用户密码添加正则表达式（或者是规则）进行单元测试
但是否发现验证用户是否存在就无法使用单元测试进行预期的判断了？这时候就需要做数据库测试了，数据库测试实际很简单，大概的流程如下


![](https://resources.blog.fastrun.cn/wp-content/uploads/2019/04/3178289017-5c98bf0e25c1e_articlex.png)


我们不看官方文档的例子，因为那对新人来说很多名词难于理解，如果你准备好了，那接下来，让我们通过实操来初试数据库测试吧！

# 准备测试数据
在准备数据前，来看看PHPUnit为我们准备的几种测试数据文件的格式。

## Flat XML DataSet （平直 XML 数据集）
```
<?xml version="1.0" ?>
<dataset>
    <user id="1" username="zhangsan" password="12345" created="2019-03-25 17:15:23" />
    <user id="2" username="lisi" password="12345" created="2019-03-25 12:14:20" />
</dataset>
```
就是如上这样，上述的结构大概意思如下
- user 表名
- username user表内的username字段
- password user表内的pssword字段
- created user表内的created字段
每一个以 ``` /> ``` 结束为一条测试数据

## XML DataSet （XML 数据集）
```
<?xml version="1.0" ?>
<dataset>
    <table name="user">
        <column>id</column>
        <column>username</column>
        <column>password</column>
        <column>created</column>
        <row>
            <value>1</value>
            <value>zhangsan</value>
            <value>123456</value>
            <value>2019-03-25 17:15:23</value>
        </row>
        <row>
            <value>2</value>
            <value>lisi</value>
            <value>123456</value>
            <value>2019-03-25 17:15:23</value>
        </row>
    </table>
</dataset>
```
上述XML与第一个XML表达形式不同，但大概是一个意思
- 首先声明了使用哪张表 ``` <table name="user"> ```
- 每行数据的包裹标签为 ``` <row> ```
- ``` <value> ``` 标签一一对应标签 ``` <column> ```
就跟SQL语句
```
UPDATE table_name SET field1=new-value1, field2=new-value2
```
一样。什么？你需要创造的测试数据太多？一个一个填会不会累死？那下面就是你的福音了

## MySQL XML DataSet （MySQL XML 数据集）
Unit 可直接使用MySQL导出的数据集,你可以在MySQL控制台使用命令
```
mysqldump --xml -t -u [username] --password=[password] [database] > /path/to/file.xml
```
这是直接导出指定库的所有表数据，如果想指定库你可以这样做
```
mysqldump --xml -t -u [username] --password=[password] [database] [table1] [table2] > /path/to/file.xml
```
导出的数据大概如下
```
<?xml version="1.0"?>
<mysqldump xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <database name="testdatabase">
        <table_data name="user">
               <field name="id">1</field>
               <field name="username">zhangsan</field>
               <field name="password">123456</field>
               <field name="created">2019-03-25 17:15:23</field>
        </table_data>
    </database>
</mysqldump>
```

## YAML DataSet （YAML 数据集）
```
user:
  -
    id: 1
    username: "zhangsan"
    password: "123456"
    created: 2019-03-25 17:15:23
  -
    id: 2
    username: "lisi"
    password:"123456"
    created: 2019-03-25 17:15:23
```
相信看到这里，我不用解释你也能看懂了。

## 其他
更多的文件格式请参照 https://phpunit.readthedocs.io/zh_CN/latest/database.html#dataset-datatable
并不是你喜好哪个格式就用哪个，要根据业务来，通过上面的几种方式，我们可以看出，类似于动态的数据，例如字段 created 我们不需要他是一个固定的值，而是根据时间变化，这种情况你只能让 

> 世界上最好的语言 PHP

来帮你了。

```
<?php
use PHPUnit\Framework\TestCase;
use PHPUnit\DbUnit\TestCaseTrait;

class IsUserTest extends TestCase
{
    use TestCaseTrait;

    protected function getDataSet()
    {
        return new UserTest(
            [
                'user' => [
                    [
                        'id' => 1,
                        'username' => 'zhangsan',
                        'password' => '123456',
                        'created' => '2019-03-25 17:15:23'
                    ],
                    [
                        'id' => 2,
                        'username' => 'lisi',
                        'password' => '123456',
                        'created' => '2019-03-25 17:15:23'
                    ],
                ],
            ]
        );
    }
}
?>
```
当然你需要实现一个自定义的数据库测试类,官方提供的这个已经够用了，你也可以随意更改以达到你的测试目的
```
<?php
class UserTest extends PHPUnit_Extensions_Database_DataSet_AbstractDataSet
{
    /**
     * @var array
     */
    protected $tables = [];

    /**
     * @param array $data
     */
    public function __construct(array $data)
    {
        foreach ($data AS $tableName => $rows) {
            $columns = [];
            if (isset($rows[0])) {
                $columns = array_keys($rows[0]);
            }

            $metaData = new PHPUnit_Extensions_Database_DataSet_DefaultTableMetaData($tableName, $columns);
            $table = new PHPUnit_Extensions_Database_DataSet_DefaultTable($metaData);

            foreach ($rows AS $row) {
                $table->addRow($row);
            }
            $this->tables[$tableName] = $table;
        }
    }

    protected function createIterator($reverse = false)
    {
        return new PHPUnit_Extensions_Database_DataSet_DefaultTableIterator($this->tables, $reverse);
    }

    public function getTable($tableName)
    {
        if (!isset($this->tables[$tableName])) {
            throw new InvalidArgumentException("$tableName is not a table in the current database.");
        }

        return $this->tables[$tableName];
    }
}
?>
```

# 准备验证数据
验证数据与测试数据格式一样。都是官方文档的那几种。例如你希望插入数据库后的结果是
```
<?xml version="1.0" ?>
<dataset>
    <user id="1" username="zhangsan" password="12345" created="2019-03-25 17:15:23" />
    <user id="2" username="lisi" password="12345" created="2019-03-25 12:14:20" />
</dataset>
```
那在执行测试时，unit则会将该xml文件对比数据库中的数据。一样则通过测试。就是这么简单。

# 致谢
充分掌握上述的格式以及官方文档内的demo，概念等，才能将数据库掌握在自己手中。下一章我会根据上述准备的数据准备一次“实战演习”，我已经帮你开了头，剩下的就看你自己的了。

感谢你看到这里，希望本篇文章可以帮到你。 
