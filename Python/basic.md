## 简介
**编译型语言与解释型语言**

-  编译型语言(java):先编译成可执行文件然后交给操作系统执行 
-  解释型语言(python):逐行解释每一句源代码，效率低 

官方文档：[https://docs.python.org/zh-cn/3.6/](https://docs.python.org/zh-cn/3.6/)


## 计算
### 除法


```python
print(9/3) 		# 3.0 即使能整除结果也为浮点数
print(10//3)	# 3 只取整数部分 
print(10%3)     # 1 取余
```


## 编码：

-  ASCII编码 (一个字节) 
-  Unicode编码 (两个字节) 
-  UTF-8编码 (英文一个字节,汉字通常3个字节，也会有4-6个字节)  
```python
# 字符串前加 b 表示bytes类型
a = 'abc'.encode('utf-8')
b = a.decode('utf-8')
print(a, b)	# b'abc' abc
```


## 字符串
### 格式化字符串


```python
# 使用%
print('%s拿了%f元钱买了%d双鞋' % ('zy',2101.5,2))
# zy拿了2101.500000元钱买了2双鞋  %s字符 %f浮点数 %d整数
# 使用f
s = '张三'
m = '20.5元'
print(f'{s}今天花费了了{m}元')
```


## 容器


分类

| 名称 | 标识 | 特点 |
| --- | --- | --- |
| 列表(list) | [] | 有序,可重复，可变 |
| 元组(tuple) | () | 与list唯一区别不可变 |
| 字典（dict） | {} | 存储key,value与 |
| set | {} | 与字典类似，只存key |



### 切片


```python
#list可为容器或者字符串
print(list[:])  # :左闭又开 -1表最后一位 不写默认左：0 右：-1
```


### 遍历


#### 迭代器


可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：`Iterator`。


-  可作用于`for`循环的对象都是`Iterable`类型； 
-  可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列； 



集合数据类型如`list`、`dict`、`str`等是`Iterable`但不是`Iterator`，不过可以通过`iter()`函数获得一个`Iterator`对象


Python的`for`循环本质上就是通过不断调用`next()`函数实现的


#### 案例


```python
# for可遍历6种类型：list、tuple、dict、set、str,generator
#list可为容器或者字符串
for item in list:
    print(item)
# 获取索引和值
for index,item in enumerate(list):
# 同时引用多变量
for x,y,z in [(1,2,4),(7,3,4),(0,5,6)]:
    print(x,y,z)
# 字典
user = {'name':'tyc','sex':1,'age':23}
for k,v in user.items():
    print(f'{k}:{v}')
```


### 列表生成式


```python
# 生成列表
list = [x*x for x in range(1,11)]   
# 多个循环
list = [m + n for m in 'ABC' for n in 'XYZ']
# 加入条件
list  = [x for x in range(1,11) if x%2==0] # 挑选偶数
```


### 生成器


这种一边循环一边计算的机制，称为生成器：generator，可以节省空间


普通函数和generator函数，


generator保存的是算法，每次调用`next(g)`，就计算出`g`的下一个元素的值，循环的过程中不断推算出后续的元素，不必创建完整的list，从而节省大量的空间


```python
# generator与list区别
l  = [x for x in range(1,11)]  #<class 'list'> 
g  = (x for x in range(1,11))  #<class 'generator'>
# 普通函数调用直接返回结果,generator函数的“调用”实际返回一个generator对象(yield)
def test1(n):			# 普通函数
    for i in range(0,n):
        print(i)


def test2(n):			# generator函数
    i = 0
    while i < n:
        yield i
        i+=1


test1(3)
g = test2(3)			#generator对象
for i in g:
    print(i)
```


## 高阶函数


该函数允许传入其他函数作为参数


```python
# 计算两个数的绝对值之和,注意传入函数不要加括号
def abs_add(var1,var2,fun):
    return fun(var1)+fun(var2)

print(abs_add(-3,19,abs))
```


### map/reduce


`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回


`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算


map和reduce都是接收：两个参数，一个是函数，一个是`Iterable`


```python
# 利用map()函数，首字母大写，其他小写
def change(name):
        return name.capitalize()
print(list(map(change,['aaa','bbb,ccCC'])))
# 可以接受一个list并利用reduce()求积
from functools import reduce

def prod(x,y):
    return x*y
print(reduce(prod,[1,3,5]))
#
```


### fillter/sorted


```python
# 与map类似 将函数作用于每个元素，根据函数返回的布尔值决定是否保留
list  = list(filter(lambda x: x % 2 == 0, [x for x in range(1, 11)]))	# 获取偶数
# sorted实现按照绝对值从大到小排列
list = [1,3,-10,-7,9]
print(sorted(list,key=abs,reverse=True))
```


### 返回函数


```python
# 函数返回一个函数，实现懒加载
def lazy_sum(*args):
    def sum():
        total = 0
        for item in args:
            total+=item
        return total
    return sum

s = lazy_sum(1, 3, 5)		# 获取sum函数
print(s())					# 执行sum函数
```


### 匿名函数(lamda)


```python
# lamda 参数:表达式 两树相加案例
f = lambda x,y:x+y		
print(f(11,2))
```


### 装饰器


为已经存在的函数或对象添加额外的功能


```python
# 编写统计方法计算时长的装饰器 使用案例
# 编写装饰器
def time_calc(func):
    def wrapper(*args,**kargs):
        start = time.time()
        f = func(*args,**kargs)
        methord_name = func.__name__
        used_time = time.time() -start
        print(f'执行{methord_name},花费了{used_time}毫秒')
        return f
    return  wrapper

# 使用
@time_calc
def test():
    time.sleep(1)

if __name__ == '__main__':
    test()
```


## 面向对象


### 创建


```python
# 定义类 ()里边填写父类 默认object
class Student():

    # 构造函数
    def __init__(self,name,score):
        # name为私有变量
        self.__name = name
        self.score = score

    def print_score(self):
        print(f'{self.__name}:{self.score}')
s = Student('tyc',100)
s.print_score()
```


### 常用函数


```python
isinstance(对象，类型)			#判断是否属于某类，优先使用isinstance()判断类型，可以将指定类型及其子类“一网打尽”
type()							# 获取对象
dir								# 获得一个对象的所有属性和方法
print(dir(list))	# 获取一个对象所有属性和方法
hasattr(obj, '属性名') # 判断对象是否有某属性
setattr(obj, 'y', 19) # 设置一个属性'y'
```


### __slots__使用


动态语言允许我们在创建实例后，给该实例绑定属性和方法，__slots__是用来声明可以为实例赋予哪些属性


```python
class Student():
    __slots__ = ('name','age')

s = Student()
s.name = 'zs'
s.age  = '33'
s.sex = 1		#报错 因为sex不在__slots__里
```


### @property使用


利用@property实现对属性赋值时进行参数校验


```python
class Student():

    @property		# 将getter方法变成属性
    def score(self):
        return self.__score

    @score.setter			# 相当于setter方法
    def score(self,value):
        if not isinstance(value,int):
            raise ValueError('score must be an integer')
        if value < 0 or value >100:
            raise ValueError('score must between 0 ~ 100!')
        self.__score = value

s = Student()
s.score = 'ab'
```


```python
# 重写__str__ 	打印对象信息
# 实现__iter__	可被用于for ... in循环
# 实现__getitem__ 可像list那样按照下标取出元素
# __getattr__	  在没有找到属性的情况下，调用__getattr__
# __call__()	  直接对实例进行调用
```


### main使用
当该python脚本被作为模块(module)引入(import)时，其中的main()函数将不会被执行。如果我们是直接执行某个.py文件的时候，该文件中那么”__name__ == '__main__'“是True,但是我们如果从另外一个.py文件通过import导入该文件的时候，这时__name__的值就是我们这个py文件的名字而不是__main__。
这个功能还有一个用处：调试代码的时候，在”if __name__ == '__main__'“中加入一些我们的调试代码，我们可以让外部模块调用的时候不执行我们的调试代码，但是如果我们想排查问题的时候，直接执行该模块文件，调试代码能够正常运行！


## 异常


```python
# 捕获格式
try:
    pass
except BaseException as e:	# 所有的错误类型都继承自BaseException
    pass
finally:
    pass
# 抛出：raise
```


logging使用


## IO


### 文件读写


```python
# r都 w写 a +  rb/wb 二进制读/写
open('文件地址','打开方式','编码格式')
# with自动调用close方法
with open('/path/to/file', 'r') as f:
```


### StringIO和BytesIO


```python
# StringIO使用
from io import StringIO
s = StringIO()
s.write('hello')
s.write('\tworld')
print(s.getvalue())
# BytesIO使用
from io import BytesIO
b = BytesIO()
b.write("你好".encode('utf-8'))
v = b.getvalue()
print(v,v.decode(encoding='utf-8'))
```


### 操作文件和目录
#### OS
```python
import os
# 获取环境变量
print(os.environ.get('path'))
# 查看当前目录绝对路径
print(os.path.abspath('.'))
# 然后创建一个目录:
os.mkdirs()
# 删掉一个目录:
os.rmdir('')
# 两个路径合成一个
os.path.join('','')
# 得到文件扩展名
os.path.splitext('')
# 拆分路径 后一部分是最后级别的目录或文件名
os.path.split('')
# 对文件重命名:
os.rename('test.txt', 'test.py')
# 删掉文件:
os.remove('test.py')
# 判断是否存在
os.path.exists()
```
#### shutil
```python
 import shutil
 # 返回复制之后的路径
 shutil.copy('来源文件','目标地址')
 # 将一个文件的内容拷贝的另外一个文件当中
 shutil.copyfile('来源文件','目标地址')
 # 移除整个目录，无论是否空
 shutil.rmtree(目录路径)
```
### 序列化


```python
# 存
import pickle
d = {'name':'zs','age':30,'sex':1}
with open('user.txt','wb') as f:
    pickle.dump(d,f)
# 取
import pickle
d = {'name':'zs','age':30,'sex':1}
with open('user.txt','rb') as f:
    print(pickle.load(f))
```


### JSON


```python
import json
class  User():
    def __init__(self,id,name,pwd):
        self.id = id
        self.name = name
        self.pwd = pwd
#用于对象转JSON
def user2dict(u):
    return {
        'id': u.id,
        'name': u.name,
        'pwd': u.pwd
    }
#用于JSON转对象
def dict2user(d):
    return User(d['id'], d['name'], d['pwd'])

u = User(1,'zs','123456')
# indent缩进
# dumps：无文件操作   dump：序列化+写入文件
# json.dump(p1, f, indent=4) f为打开的文件
print(json.dumps(u,default=user2dict)) # 只能解析User实例
print(json.dumps(u,default=lambda obj:obj.__dict__)) #可解析所有实例

json_str = '{"id": 2, "name": "ls", "pwd": "654321"}'
# json.load(f) f为json文件
# loads：无文件操作  load： 读文件+反序列化
print(json.loads(json_str, object_hook=dict2user))
```


### yml


```python
# 配置文件
user:
  name: zs
  pwd: 21676737de
# 测试
import yaml
import os

cong_file_name = 'config.yml'
cur_path = os.path.abspath('.')
print(f'当前文件所在目录：{cur_path}')
config_path = os.path.join(cur_path, cong_file_name)
print(f'配置文件地址：{config_path}')
with open(config_path, 'r', encoding='utf-8') as f:
    content = f.read()
    content_yml = yaml.load(content,Loader=yaml.FullLoader)
    a =content_yml.items()
    print('获取配置用户名:%s' % (content_yml.get('user').get('name')))
    print('获取配置密码:%s' % (content_yml.get('user').get('pwd')))
```


### XML
详情参考：[https://www.cnblogs.com/CheeseZH/p/4026686.html#elementtree-xpath](https://www.cnblogs.com/CheeseZH/p/4026686.html#elementtree-xpath)
```python
try:
    import xml.etree.cElementTree as ET
except:
    import xml.etree.ElementTree as ET

tree = ET.parse('test.xml')
root = tree.getroot()
print(tree)
print(tree.getroot())
print(f'{root.tag}:{root.attrib}')
# 获取根节点下第一个节点的第三个属性值
print(root[0][2].text)


# 便利
for child in root:
    # 遍历第二层节点和属性
    print(f'{child.tag}:{child.attrib}')
    for child_2 in child:
        print(f'{child_2.tag}:{child_2.attrib}')
```
像xml中添加节点
```python
        # xml文件位置
        value_dir = dir.decompiler(self.channel)+"\\res\\values\\strings.xml"
        tree = ET.parse(value_dir)
        root = tree.getroot()
        stringsNodes = root.findall('string')
        # 准备要添加的元素
        e1 = Element('string', {'name': '属性值1'})
        e1.text = privacyUrl
        e1_add = True
        e2 = Element('string', {'name': '属性值2'})
        e2.text = userAgreementUrl
        e2_add = True
        # 判断源文件里有没有此属性，有则修改没则添加
        for node in stringsNodes:
            if node.attrib['name'] == '属性值1':
                node.text = '值1'
                e1_add = False
            elif node.attrib['name'] == '属性值2':
                node.text = '值2'
                e2_add = False
        if e1_add:
            root.append(e1)

        if e2_add:
            root.append(e2)
		# 格式化xml文件
        indent(root)
        tree.write(value_dir, "utf-8", xml_declaration=True,  method='xml')
        
def indent(elem, level=0):
    i = "\n" + level*"    "
    if len(elem):
        if not elem.text or not elem.text.strip():
            elem.text = i + "    "
        for e in elem:
            indent(e, level+1)
        if not e.tail or not e.tail.strip():
            e.tail = i
    if level and (not elem.tail or not elem.tail.strip()):
        elem.tail = i
    return elem       
```
结果如下：
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="属性值1">值1</string>
    <string name="属性值2">值2</string>
</resources>
```


## 进程与线程


## 常用模块


### datetime


```python
from datetime import datetime
# 获取当前时间
print(datetime.now())
# 获取指定时间
dt = datetime(2021, 7, 19, 12, 20, 59)
print(dt)
# 转timestamp
print(dt.timestamp())
ts = 1626668459.0
# 转timestamp->datatime
print(datetime.fromtimestamp(ts))
# 字符串转时间
cday = datetime.strptime('2021-6-1 18:19:59', '%Y-%m-%d %H:%M:%S')
# 时间转字符串
print(datetime.now().strftime('%Y-%m-%d %H:%M:%S'))
```


### 数据可视化
```python
# coding=utf-8
# author：tyc
# creat_time: 2021/7/15 17:22
# description: 数据可视化
# pip install pyecharts   介绍：https://pyecharts.org/#/zh-cn/intro
# pip install jupyter   介绍：https://www.cnblogs.com/xingxia/p/python_jupyter.html
# jupyter notebook 启动
# jupyter notebook --generate-config 生成配置文件
# 添加以下信息配置打开谷歌浏览器
'''
import webbrowser
webbrowser.register("chrome",None,webbrowser.GenericBrowser(r"C:\Program Files\Google\Chrome\Application\chrome.exe"))
c.NotebookApp.browser = 'chrome'
'''
from pyecharts.charts import Bar
from pyecharts import options as opts

bar = (
    Bar()
    .add_xaxis(["衬衫", "毛衣", "领带", "裤子", "风衣", "高跟鞋", "袜子"])
    .add_yaxis("商家A", [114, 55, 27, 101, 125, 27, 105])
    .add_yaxis("商家B", [57, 134, 137, 129, 145, 60, 49])
    .add_yaxis("商家C", [55, 12, 43, 167, 43, 79, 18])
    .set_global_opts(title_opts=opts.TitleOpts(title="某商场销售情况"))
)
# 生成html文件
# bar.render()
# 在jupyter展示
bar.render_notebook()
```
## 爬虫
[https://gitee.com/tyc12345/pc](https://gitee.com/tyc12345/pc)
## 案例
### 配置logger打印控制台添加颜色，输出日志文件
创建log.py
```python
# coding=utf-8
# author：tyc
# creat_time: 2021/6/10 9:19
# description: 初始化日志信息

import logging
import os
from datetime import datetime
from logging.handlers import TimedRotatingFileHandler
import colorlog

# 配置console不同级别打印颜色
log_colors_config = {
    'DEBUG': 'cyan',
    'INFO': 'green',
    'WARNING': 'yellow',
    'ERROR': 'red',
    'CRITICAL': 'red',
}


def _getLogger_():
    # 创建logger,配置打印级别
    logger = logging.getLogger()
    logger.setLevel(logging.DEBUG)
    # todo 从配置文件中读取信息
    console_level = logging.DEBUG       # 控制台打印级别
    logs_level = logging.WARN           # 日志保存级别
    # 定义打印格式
    console_formatter = colorlog.ColoredFormatter('%(log_color)s%(asctime)s-%(filename)s-[line:%(lineno)d]-['
                                                  'thread_id:%(thread)d]-%(levelname)s: %(message)s',
                                                  log_colors=log_colors_config)
    formatter = logging.Formatter(
        '%(asctime)s-%(filename)s-[line:%(lineno)d]-[thread_id:%(thread)d]-%(levelname)s: %(message)s')
    # 创建handler输出到文件handler
    log_name = datetime.now().strftime('%Y%m%d')  # 日志文件名不带后缀
    log_path = os.path.abspath('.') + "\\logs"
    isExists = os.path.exists(log_path)
    if not isExists:
        os.makedirs(log_path)
    full_log_name = log_path + '\\' + log_name + '.log'
    file_handler = logging.FileHandler(full_log_name)
    file_handler.setFormatter(formatter)
    # 创建handler输出到控制台
    console_handler = logging.StreamHandler()
    console_handler.setLevel(console_level)
    console_handler.setFormatter(console_formatter)
    # 创建TimedRotatingFileHandler
    time_rotating_file_handler = TimedRotatingFileHandler(filename=full_log_name, when="midnight", interval=1,
                                                          backupCount=7)  # 每天产生一个日志文件 backupCount: 表示日志文件的保留个数
    time_rotating_file_handler.setFormatter(formatter)
    time_rotating_file_handler.setLevel(logs_level)
    # 添加handler
    logger.addHandler(file_handler)
    logger.addHandler(console_handler)
    logger.addHandler(time_rotating_file_handler)
    # 防止重复打印
    logger.removeHandler(file_handler)
    return logger


log = _getLogger_()

```
测试
```python
from log import log

log.debug('111111')
log.info('222222')
log.warn('33333')
log.error('44444')
```


 
