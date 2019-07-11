# 1. 接口测试
## 1.1 测试的主要类型及阶段   



|测试类型|测试阶段|测试规模|自动化难易度|自动化收益|负责人员|
|----|----|----|----|----|----|
|单元测试|早期(提测前)|小|低|高|开发|
|接口测试|中期(联调或刚提测)|中小|中|中|开发或测试|
|集成测试|中后期(提测后)|中|中|中|开发或测试|
|UI测试|中后期(提测后)|大|高|低|测试|





- 单元测试，每种编程语言都有对应的单元测试框架来帮助开发人员来完成单元测试。Java有JUnit和TestNG，Python有unittest等。

- UI测试，是测试人员最为熟悉的。有大量的工具可以帮助测试人员实现自动化。如QTP，Selenium等。

- 接口测试，是指测试系统单个模块的接口。验证单个模块的接口功能是否正常。对于测试目标模块所依赖的其他模块，我们多采用打桩（Stub）的方式来处理。

- 集成测试，是指把系统多个模块集成到一起再进行测试，测试的目的是测试多个模块之间交互的较复杂的逻辑。对于测试目标所依赖的其他模块，我们选择部分打桩（Stub）或者不进行打桩。


` 在实际工作中大家常把集成测试中，只测试接口部分的测试，也成为接口测试，只是一种构建部署和目标逻辑较为复杂的一种接口测试。` 



## 1.2 互联网服务的接口类型

在互联网的应用场景中，绝大部分采用的是http（https）协议类型的接口。

` 在一些游戏场景会看到socket等其他的协议类型接口 `

WebSerivce的两种主要实现方式 SOAP & REST

` RESTful Web 服务使用标准的 HTTP 方法 (GET/PUT/POST/DELETE) 来抽象所有 Web 系统的服务能力，而不同的是，SOAP 应用都通过定义自己个性化的接口方法来抽象 Web 服务，这是一种RPC协议风格。Rest具有无状态性和幂指相等特性，流行程度越来越高，渐渐的在取代SOAP` 

所以本文主要阐述http接口的测试，并会搭建RESTful风格的服务来进行测试演示。


## 1.3 为什么选择Python
#### 脚本语言 和 编译型语言

脚本语言的开发环境比较轻量级，学习相较编译型语言要容易轻松。

#### 3种脚本语言的特点

Perl：正则非常出色，扩展的管理略逊色。流行度相对较低。
Puby：优秀的web框架。
Python：非常特色的语法贴近自然语言，优秀的扩展管理，丰富的插件。Web框架也很优秀。3种脚本语言中流行度最高。

#### 3种脚本语言的哈希（字典）实现方式

Perl
```
#!/usr/bin/perl
 
%data = ('google'=>'google.com', 'runoob'=>'runoob.com', 'taobao'=>'taobao.com');
 
print "\$data{'google'} = $data{'google'}\n";
print "\$data{'runoob'} = $data{'runoob'}\n";
print "\$data{'taobao'} = $data{'taobao'}\n";
```
Ruby
```
#!/usr/bin/ruby
 
$, = ", "
months = Hash.new( "month" )
 
months = {"1" => "January", "2" => "February"}
 
keys = months.keys
 
puts "#{keys}"
```

Python
```
#!/usr/bin/python
 
dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'};
 
print "dict['Name']: ", dict['Name'];
print "dict['Age']: ", dict['Age'];
```

可以看到Python的字典在语法上和原始json格式非常接近，易于上手。


## 1.4 Jennkins自动化

暂时略

# 2. 接口测试实战
## 2.1 测试准备
### 2.1.1 框架准备 之 Requests
首选选择了Python这个脚本语言，但是仅仅依靠python自带的urllib这个库，操作起来不够便捷。在这里选择使用requests库。requests基于urllib，但是urllib的功能过于基础，实现一个常用功能就需要写大量代码。requests在urllib之上再进行了一些优化整合，实现了会话保持、文件上传、自动编码等等新特性。


##### 一次requests的GET请求
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests
 
r = requests.get(url='https://www.bing.com')    # 最基本的GET请求
print r.status_code    # 获取返回状态


r = requests.get(url='https://cn.bing.com/dict/search', params={'q':'python'})   #带参数的GET请求
print r.url.encode("utf-8")   #带参数的请求url地址
print r.content   #打印解码后的返回数据
```


##### requests的请求，风格统一，使用简单
```
requests.get(‘http://httpbin.org’) #GET请求
requests.post(“http://httpbin.org/post”) #POST请求
requests.put(“http://httpbin.org/put”) #PUT请求
requests.delete(“http://httpbin.org/delete”) #DELETE请求
requests.head(“http://httpbin.org/get”) #HEAD请求
requests.options(“http://httpbin.org/get”) #OPTIONS请求
```


##### 一次json数据的POST请求
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests
import json
 
url = 'http://httpbin.org/post'
jsonData = json.dumps({'key1': 'value1', 'key2': 'value2'})    # Json数据
r = requests.post(url, data=jsonData)
print r.content
```

##### 一次from表单的POST请求
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests
import json
 
url = 'http://httpbin.org/post'
jsonData = {'key1': 'value1', 'key2': 'value2'}    # 这里的python字典被当做from表单
r = requests.post(url, data=jsonData)
print r.content
```

##### 一次上传文件的POST请求
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests
import json
 
url = 'http://httpbin.org/post'
files = {'file': open('report.txt', 'rb')}
r = requests.post(url, files=files)
print r.content
```

##### Response 对象
```
r.status_code #响应状态码
r.raw #返回原始响应体，也就是 urllib 的 response 对象，使用 r.raw.read() 读取
r.content #字节方式的响应体，会自动为你解码 gzip 和 deflate 压缩
r.text #字符串方式的响应体，会自动根据响应头部的字符编码进行解码
r.headers #以字典对象存储服务器响应头，但是这个字典比较特殊，字典键不区分大小写，若键不存在则返回None
#*特殊方法*#
r.json() # Requests中内置的JSON解码器
r.raise_for_status() #失败请求(非200响应)抛出异常
```

### 2.1.2 框架准备 之 Unittest

另外，采用python自己的单元测试框架来帮助我们完成我们接口测试框架的搭建。

- 一个完整的测试流程，包括测试前准备环境的搭建(setUp)，执行测试代码(run)，以及测试后环境的还原(tearDown)。元测试(unit test)的本质也就在这里，一个测试用例是一个完整的测试单元，通过运行这个测试单元，可以对某一个问题进行验证。

- 多个测试用例集合在一起，就是TestSuite，而且TestSuite也可以嵌套TestSuite。

##### 一个Unittest实例
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import random  
import unittest  
  
class TestSequenceFunctions(unittest.TestCase):  
  
    def setUp(self):  
        self.seq = range(10)  
  
    def test_shuffle(self):  
        # make sure the shuffled sequence does not lose any elements  
        random.shuffle(self.seq)  
        self.seq.sort()  
        self.assertEqual(self.seq, range(10))  
  
        # should raise an exception for an immutable sequence  
        self.assertRaises(TypeError, random.shuffle, (1,2,3))  
  
    def test_choice(self):  
        element = random.choice(self.seq)  
        self.assertTrue(element in self.seq)  
  
    def test_sample(self):  
        with self.assertRaises(ValueError):  
            random.sample(self.seq, 20)  
        for element in random.sample(self.seq, 5):  
            self.assertTrue(element in self.seq)  
  
if __name__ == '__main__':  
    unittest.main()  
```
### 2.1.3 配置项 (configparser插件)

在软件项目开发过程中会有不少配置项，用来对软件工程进行配置管理。服务的发现管理，分发的策略，数据库的连接，功能的开关等等。

在测试框架中，也会产生不少配置项。比如，数据库连接，测试目标的选择，依赖的配置。一般来说常见的配置文件为ini文件。


##### sample.ini
```
[WebTest]
host = yikedemo.mysql.rds.aliyuncs.com
port = 3406
user = admin
passwd = ****************
db = danlu_cd_database
charset = utf8

[AppTest]
host = yikedemoapp.mysql.rds.aliyuncs.com
port = 3306
user = admin
passwd = ****************
db = danlu_cd_database
charset = utf8
```

##### configparser示例

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import ConfigParser  
  
config=ConfigParser.ConfigParser()  
config.read(u'sample.ini')

for section in config.sections():  
    print section  
    for option in config.options(section):  
        print "    ",option,"=",config.get(section,option)  


```

### 2.1.4 数据准备（xlutils插件）

遵循测试数据和测试用例分离的开发规范，是有助于提高用例开发效率，以及提高用例及数据的可维护性。

数据可以用excel保存起来，也可以直接保存xml和json数据到文本文件。

处理excel文件需要用到一个插件xlutils。

##### xlutils xlrd 示例
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import xlrd

exceldata = xlrd.open_workbook('testdata.xls')

sh1 = exceldata.sheet_by_index(0)  #得到第一张表单，索引方式
sh2 = exceldata.sheet_by_name(u'Sheet1')  #得到第一张表单，名字方式

for rownum in range(sh1.nrows):
    print sh1.row_values(rownum) #递归打印出每行的信息

for rownum in range(sh2.nrows):
    print sh2.row_values(rownum) #递归打印出每行的信息
```
测试执行的时候，绝大多数时间是对excel数据的读操作，几乎不用会用写操作。

如果xml或者json数据，直接用python自带的文件读写操作即可完成。


## 2.2 用例编写
### 2.2.1 接口封装（关键字封装）

把测试目标的接口分组，并进行封装。
对于结构复杂的表单或者json数据，进行分解，把固定数据录入文件，把变化的数据作为封装的入参。
```
代码暂定
待整理


class target_sc:
    def __init__(self):
        # 从配置文件中读取接口服务器IP、域名，端口
        from www.common.config import config
        httpConfig = config().confighttp
        self.dlschost = httpConfig['dlschost']

    def getOrdersFullBackInfo(self,sellerId=None,goodsIds=None,goodsCoupon=None,goodsPromotion=None,goodsAmount=None):
        # post请求,根据传入商品信息判断此订单是否满足满返活动的条件，并返回相应提示
        # /sc/V1/fullBack/getOrdersFullBackInfo
        url = 'http://' + self.dlschost + '/sc/V1/fullBack/getOrdersFullBackInfo'
        data = [
            {
                'sellerId': sellerId,
                'goodsIds': goodsIds ,
                'goodsCoupon': goodsCoupon ,
                'goodsPromotion': goodsPromotion,
                'goodsAmount': goodsAmount
            }
        ]
        response = requests.post(url, json=data)
        response.connection.close()
        res = json.loads(response.content)
        return res
```


### 2.2.2 用例编写

继承unittest.TestCase类
开始编写测试用例
测试用例分为3个部分
1. 测试数据准备，可以去数据文件读取数据出来，也可以针对本次用例在用例中录入特定数据。
2. 通过封装好的函数，访问目标接口。
3. 根据回应状态及内容进行断言，判断此次用例结果是否正确。（遇到特殊的情况，需要对数据库的内容进行断言）


```
代码暂定
待整理
class getOrdersFullBackInfo(unittest.TestCase):
    dlservice = dlsc()
    UserShop = wsData('FullBack')

    #校验订单参加满返活动--成功
    def getOrdersFullBackInfo_sucess(self):
        update('UPDATE shoppingcart.shoppingcart SET goods_quantity=? WHERE goods_id=?','5','1111111111111111111')
        getInfo=self.dlservice.getOrdersFullBackInfo(sellerId=self.UserShop.sellerId,goodsIds=[self.UserShop.goodsIds],goodsCoupon={self.UserShop.goodsIds:self.UserShop.goodsCoupon},goodsPromotion={self.UserShop.goodsIds:self.UserShop.goodsPromotion},goodsAmount={self.UserShop.goodsIds:self.UserShop.goodsAmount})

        print getInfo
        for dataItem in getInfo['data']:
                 print dataItem['fullBackStatus']
        self.assertEqual(getInfo['status'],0)
        self.assertEqual(dataItem['activityId'],'074346554360')
        #满返状态（0-达到门槛，1-未到门槛）
        self.assertEqual(dataItem['fullBackStatus'],0)
         #本次能获得的满返活动红包个数
        self.assertEqual(dataItem['couponNum'],1)
         # #满返活动关联单个红包金额
        self.assertEqual(dataItem['couponAmt'],2000)

```
### 2.2.3 断言

unittest模块自带了很多断言
下面是一些常用的
|断言方法|断言描述|
|:-----|:----|
|assertEqual(arg1, arg2, msg=None)|验证arg1=arg2，不等则fail|
|assertNotEqual(arg1, arg2, msg=None)|验证arg1 != arg2, 相等则fail|
|assertTrue(expr, msg=None)|验证expr是true，如果为false，则fail|
|assertFalse(expr,msg=None)|验证expr是false，如果为true，则fail|
|assertIs(arg1, arg2, msg=None)|验证arg1、arg2是同一个对象，不是则fail|
|assertIsNot(arg1, arg2, msg=None)|验证arg1、arg2不是同一个对象，是则fail|
|assertIsNone(expr, msg=None)|验证expr是None，不是则fail|
|assertIsNotNone(expr, msg=None)|验证expr不是None，是则fail|
|assertIn(arg1, arg2, msg=None)|验证arg1是arg2的子串，不是则fail|
|assertNotIn(arg1, arg2, msg=None)|验证arg1不是arg2的子串，是则fail|
|assertIsInstance(obj, cls, msg=None)|验证obj是cls的实例，不是则fail|
|assertNotIsInstance(obj, cls, msg=None)|验证obj不是cls的实例，是则fail|

### 2.2.4 数据场景的准备和还原
数据场景的准备和还原，大多数时候需要去操作数据库。、

比如一个用例是添加用户，那么在用例执行完毕之后，需要去删除数据。不然下次执行这条用例的时候，如果测试数据不变，添加用户的行为会失败。

如果用例是删除用户，那么在执行用例之前，要准备一个用户让用例去删除。不然无数据可删除，也会导致用例失败。

也有较为省事的做法。人工维护好一个测试数据库，每次测试执行之前都做好数据库备份，执行完毕再还原回去。这样可以降低用例的复杂度。但是如果数据库结构变化较为频繁，这样维护数据库的人员，工作量会增大。

### 2.2.5 测试报告生成
可以用HTMLTestRunner配合unittest的结果，生成一个详尽的html测试报告。
另外可以编写mail发送模块，可以往指定的目标发送email（当然这个功能可以在自动化的时候靠jenkins的插件来做）


## 2.3 自动化


暂略

随便增加一点






***end*** 

