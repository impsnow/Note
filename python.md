*=		+=


## 获取帮助

help(xx)

dir()

xx.__doc__ 文档字符串

## 数据类型
```

float:浮点数间存在不确定位数，运算比较时用round函数
科学计数：4.3e-3=0.0043  9.6E5=960000.0


string，list，tulpe，dict
方法 函数
string： 同时含有单双引号时可使用'''或转义\
		下标 切片[起始：结束：步长] .find() .count() .replace() .split() 
list: append() extend() insert(下标,xx) del  pop() remove(xx) sort() reserve() 嵌套
tulpe：
dict：dict01.get(XX,defualt)    del dict['Name']   .keys() .values() .items()
公共方法： + * in not in len() max() min() del
```
## 函数
```
def 函数名()：
	"""
	DOC
	"""
	global a
    xxx
	return a，b，c #默认是元组

参数 		return 		局部变量
拆包：  a，b=1，2            a，b=fun()
参数：位置参数，缺省参数(a=18)，不定长参数(*args 存放在元组args中)（**kwargs，放在字典中）
交换变量值： a,b=b,a

递归函数： 函数内部调用自己本身
eval(): eval('a+20') 字符串可以的话被执行
lambda 参数：语句  ：  sum = lambda 4,5:a+b
range： range(开始，结束，步长) 返回列表 整数
列表推导式： list01 = [ x for x in range(10) ]
```
## 对象 class

类： 类名，属性，方法

对象（实例）

__init__(self，tom，18)  传入参数 给属性
self
self.__xx： 私有属性，不能直接调用，可再写个方法
私有方法：def __XX:同上，可在类内部访问
__str__（self） ：直接打印对象时返回此方法的返回内容，且只能是字符串


__del__（self）： 对象没有被引用时（删除时） 会调用del方法

继承：子类可重写父类的方法，想调用父类方法时用 super().父类的方法名 调用
class A（B，C）同名的属性方法模式使用第一个
多态
封装

类属性：写到类里面。方法外面。通过类名.属性名调用
实例属性：__init__里面，通过实例名.属性名调用

实例方法
类方法：调用时用类名调用， cls同self
	@classmethod
	def foo（cls）：
静态方法：不需要传参数，类 实例都可调用
	@staticmethod
	def foo（）：
	
	
属性引用使用和 Python 中所有的属性引用一样的标准语法：obj.name。
类的方法与普通的函数只有一个特别的区别——它们必须有一个额外的第一个参数名称, 按照惯例它的名称是 self。




### 函数装饰器

```
import functools


def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print('call %s():' % func.__name__)
        print('args = {}'.format(*args))
        return func(*args, **kwargs)

    return wrapper
```
值得注意的是@functools.wraps(func)，这是python提供的装饰器。
它能把原函数的元信息拷贝到装饰器里面的 func 函数中。
函数的元信息包括docstring、name、参数列表等等。
可以尝试去除@functools.wraps(func)，你会发现test.__name__的输出变成了wrapper

装饰器可以带参数

### 迭代器 生成器

### 静态代码分析工具

## 虚拟环境
```
virtualenv --no-site-packages myvenv
source venv/bin/activate
deactivate

# python3.3之后自带venv ，进入退出方法与virtualenv一致
python -m venv myvenv
```

### 下划线使用

_var   ：命名约定，内部使用，私有 import* 不会导入
 
var_    ：命名约定，避免与关键字冲突

__var  ：在类中触发名称修饰，防止子类重写， 变成 __classname_methodname

__var__  ： 特殊方法

_ 临时的变量



