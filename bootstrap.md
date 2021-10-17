
#

## 布局容器 和 网格
.container 固定宽度并支持响应式

.container-fluid 暂用全部视口viewport的容器

row行 column列

超过12会换行 ， 电脑基本是md大小 >=970px

```
    <div class="container">
      <div classs="row">
        <div class="col-md-4 col-lg-4">4 </div>
        <div class="col-md-8 col-lg-8">8 </div>
      </div>
    </div>

```
列偏移

class="col-md-1 col-md-offse-2" 往右偏移多少列

列排序

class="col-md-1 col-md-pull-1" 往前偏移 有的话会覆盖前列

class="col-md-1 col-md-push-1" 往后偏移 有的话会被后列覆盖

列嵌套

列里面加上row又可分为12列

## 常用样式
### 标题

提供了对应的类名，可为非标题元素设置样式，h1~h6

<div class="h1">xx </div>

副标题，small标签或small类

### 段落

.lead 强调

small 标签 ： 小号字

b stronger 标签： 加粗

i em标签： 倾斜

### 颜色强调
```
.text-muted ： 提示，浅灰色
.text-primary： 主要，蓝色
.text-success： 成功，浅绿色
.text-info： 通知信息，浅蓝色
.text-warning： 警告，黄色
.text-danger： 危险，褐色
```
### 对齐效果
```
.text-left ：左对齐
.text-justify ：左右对齐
```
### 列表
```
.list-unstyle： 无点列表
.list-inline： 内联列表 水平
.dl-horizontal： 定义列表 ，水平

```
### 代码
```
code标签：单行代码
kbd标签：显示快捷键操作
pre标签：多行代码，保留空格和换行，显示html代码需要字符实体 （< &lt） .pre-scrollable 加滚动条
```
### 表格










