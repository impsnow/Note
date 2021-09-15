# html

## 表单
```HTML
<form>
.
input 元素
.
</form>
```

文本域： <input type="text">
密码字段：<input type="password">
单选按钮： <input type="radio" name="sex" value="female">Female
复选框：<input type="checkbox" name="vehicle" value="Car">I have a car

 required 属性会自动表单验证，不为空即可

### 
href  
在页面很长的时候会使用 # 来定位页面的具体位置，格式为：# + id。

# javascript
```
<script>
document.write("Hello World!");
</script>
```
可在head，body，外部
<script src="myScript.js"></script>

### 输出
```
JavaScript 没有任何打印或者输出的函数。

JavaScript 显示数据
JavaScript 可以通过不同的方式来输出数据：

使用 window.alert() 弹出警告框。
使用 document.write() 方法将内容写到 HTML 文档中。
使用 innerHTML 写入到 HTML 元素。
使用 console.log() 写入到浏览器的控制台。
```
### 语法
字面量：固定的值 ，数字，字符串，表达式，数组，对象（object），函数

字符串（String）字面量 可以使用单引号或双引号

数组，对象:array ， object

var cars=["Saab","Volvo","BMW"];

var arr= new Array()； 


var person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"};

对象的方法定义了一个函数，并作为对象的属性存储。

对象方法通过添加 () 调用 (作为一个函数)。

不加（）返回函数表达式

函数（Function）字面量 定义一个函数：

function myFunction(a, b) { return a * b;}


## 变量
使用**关键字** var 来定义变量， 使用等号来为变量赋值

可以在声明变量时对其赋值：

var carname="Volvo";

可以通过将变量的值设置为 null 来清空变量。

局部变量会在函数运行以后被删除。

全局变量会在页面关闭后被删除。


## 注释：
// 我不会执行
多行注释以 /* 开始，以 */ 结尾。

JavaScript 对大小写是敏感的。

通常我们在每条可执行的语句结尾添加分号。

可以在文本字符串中使用反斜杠对代码行进行换行，提高代码可读性

 反斜杠是一个转义字符。 转义字符将特殊字符转换为字符串字符：

可以使用 typeof 操作符来检测变量的数据类型
constructor 属性返回所有 JavaScript 变量的构造函数。

全局方法 String() 可以将数字转换为字符串。
全局方法 Number() 可以将字符串转换为数字。

viod操作符指定要计算一个表达式但是不返回值。
<a href="javascript:void(alert('Warning!!!'))">点我!</a>



### 字符串方法

### 运算符
算术运算符
赋值运算符
比较运算符
逻辑运算符
条件运算符：
variablename=(condition)?value1:value2 

### 条件语句
if else
switch
for循环：
```
JavaScript 支持不同类型的循环：

for - 循环代码块一定的次数
for/in - 循环遍历对象的属性
while - 当指定的条件为 true 时循环指定的代码块
do/while - 同样当指定的条件为 true 时循环指定的代码块
```
continue语句：跳过该循环并执行下一个循环

如需标记 JavaScript 语句，请在语句之前加上冒号：
```
label:
statements
```

### 事件
HTML 事件是发生在 HTML 元素上的事情。
HTML 事件可以是浏览器行为，也可以是用户行为。

将修改自身元素的内容 (使用 this.innerHTML):
<button onclick="this.innerHTML=Date()">现在的时间是?</button>

### 异步编程
使用setTimeout函数
```
<script>
setTimeout(function () {
    document.getElementById("demo1").innerHTML="RUNOOB-1!";
}, 3000);
document.getElementById("demo2").innerHTML="RUNOOB-2!";
</script>
```

### DOM
通过 HTML DOM，可访问 JavaScript HTML 文档的所有元素。
当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。

通过 id 找到 HTML 元素：
var x=document.getElementById("intro");
通过标签名找到 HTML 元素：
var x=document.getElementsByTagName("p");
通过类名找到 HTML 元素：
var x=document.getElementsByClassName("intro");





# css
CSS 规则由两个主要的部分构成：选择器，以及一条或多条声明:
<style>
	XXX
</style>

```
p
{
    color:red;
    text-align:center;
	/*这是个注释*/
}
```
id选择器：
```
#para1
{
    text-align:center;
    color:red;
}

```
class选择器：
```
.center {text-align:center;}
p.center {text-align:center;}
```
插入样式表：
（内联样式）Inline style > （内部样式）Internal style sheet >（外部样式）External style sheet > 浏览器默认样式

### div 浮动
浮动可以理解为让某个div元素脱离标准流，漂浮在标准流之上，和标准流不是一个层次。


float， clear：
https://www.cnblogs.com/acorn/p/5249089.html

清除浮动方式：

另加元素 带属性clear：both  clear 属性指定元素两侧不能出现浮动元素。

父级可添加overflow:hidden （内容会被修剪，并且其余内容是不可见的。）或hight 来显示父级div 

CSS overflow 属性用于控制内容溢出元素框时显示的方式。

margin、border、padding、context。

### 定位
position：relative 相对定位，以原有位置移动，保留占用原有定位

position：absolute 绝对定位，不会占用原有位置 ，外层有定位时（非static）以父类为参考点。 外层无定位时以00点参考。如父层没有设置position，，寻找设定过定位的外层元素作为参考点

position：fixed 固定定位，相对于窗口进行定位

position：static 默认值

Z-index  设置z轴的距离，设置元素显示顺序，只支持定位元素

### display属性
block 指定对象为块元素，如div

inline 内联元素，如span

inline-block


#
AJAX 是与服务器交换数据并更新部分网页的技术，在不重新加载整个页面的情况下
JavaScript 框架库 - jQuery
