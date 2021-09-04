### sqlite3
```
 sqlite3  db.sqlite3
.databases
.tables
.schema tablename

```
# django

### admin command
```
django-admin startproject HelloWorld


python3 manage.py makemigrations app
python3 manage.py migrate

nohup python3 manage.py runserver 0.0.0.0:8000 > output.log 2>&1 &
```
### 模板 template

语法：

变量：
```
view：｛"HTML变量名" : "views变量名"｝
HTML：｛｛变量名｝｝
```
列表：
templates 中的 html中，可以用 . 索引下标取出对应的元素。
字典：
templates 中的 html中，可以用 .键 取出对应的值。

**过滤器**：
{{ 变量名 | 过滤器：可选参数 }}
过滤器有参数。 过滤器的参数跟随冒号之后并且总是以双引号包含。 
default 为变量提供一个默认值。
{{ num|filesizeformat}}
{{ time|date:"Y-m-d" }}

safe：将字符串标记为安全，不需要转义。

**标签**：
if/else标签 ：
```
{% if condition %}
     ... display
{% endif %}
```
for标签：
```
{% for i in views_list %}
{{ i }}
{% endfor %}
```
遍历字典: 可以直接用字典 .items 方法
```
{% for i,j in views_dict.items %}
{{ i }}---{{ j }}
{% endfor %}
```
可选的 {% empty %} 从句：在循环为空的时候执行（即 in 后面的参数布尔值为 False ）。
```
{% for i in listvar %}
    {{ forloop.counter0 }}
{% empty %}
    空空如也～
{% endfor %}
```
ifequal/ifnotequal 标签:
```
{% ifequal section 'sitenews' %}
    <h1>Site News</h1>
{% else %}
    <h1>No News Here</h1>
{% endifequal %}

{# 这是一个注释 #}

include 标签: {% include %} 标签允许在模板中包含其它的模板的内容。
{% include "nav.html" %}

csrf_token 用于form表单中，作用是跨站请求伪造保护。

可自定义标签
```


静态文件：
```
STATIC_URL = '/static/' # 别名 
STATICFILES_DIRS = [ 
    os.path.join(BASE_DIR, "statics"), 
]
```
html中引入要用别名 STATIC_URL
在模板中使用需要加入 {% load static %} 代码：
```
{% load static %}
{{name}}<img src="{% static "images/runoob-logo.png" %}" alt="runoob-logo">
```
**模板继承**：
父模板
标签 block...endblock: 父模板中的预留区域，该区域留给子模板填充差异性的内容，不同预留区域名字不能相同。
```
{% block 名称 %} 
预留给子模板的区域，可以设置设置默认内容
{% endblock 名称 %}
```
子模板:
```
{% extends "父模板路径"%} 

{ % block 名称 % }
内容 
{% endblock 名称 %}

```
### 模型
models类-》数据表
对象实例-》一条记录
属性-》字段
```
# 在与 settings.py 同级目录下的 __init__.py 中引入模块和进行配置
import pymysql
pymysql.install_as_MySQLdb()
```
Django 规定，如果要使用模型，必须要创建一个 app。
django-admin.py startapp TestModel

数据库操作：
修改数据可以使用 save() 或 update():
```
   Test.objects.filter(id=1).update(name='Google')
或
    test1 = Test.objects.get(id=1)
    test1.name = 'Google'
    test1.save()
	
	
	Test.objects.filter(id=1).delete()	
   
```
### 表单
request.post["HTML中ID"]
每个视图函数的第一个参数是一个 HttpRequest 对象,如request

META 中这些头加上前缀 HTTP_ 为 Key, 冒号(:)后面的为 Value

if request.method == "POST"来判断是否使用HTTP POST方法

POST不包括file-upload信息。参见FILES属性。

GET和POST属性是django.http.QueryDict类的实例。
```
def search(request):  
    request.encoding='utf-8'
    if 'q' in request.GET and request.GET['q']:
        message = '你搜索的内容为: ' + request.GET['q']
    else:
        message = '你提交了空表单'
    return HttpResponse(message)
```

### 视图
请求对象：
request.get()：返回键值对应的字符串，如果该键对应有多个值，取出该键的最后一个值。

request.body

request.path

request.method


响应对象：

主要有三种形式：HttpResponse()、render()、redirect()。

HttpResponse(): 返回文本，可渲染html标签

render(): 返回文本，第一个参数为 request，

	第二个参数为字符串（页面名称），
	
	第三个参数为字典（可选参数，向页面传递的参数：	键为页面参数名，值为views参数名）。
```
return render(request,"runoob.html",{"name":name})
```
redirect()：重定向
	return redirect("/index/")

问题：post 请求返回 403
1. 解决：
导入模块：
from django.views.decorators.csrf import csrf_exempt
在函数前面添加修饰器：
@csrf_exempt

### 路由
path：用于普通路径
re_path：用于正则路径
```
from django.urls import re_path # 用re_path 需要引入
urlpatterns = [
    path('admin/', admin.site.urls),
    path('index/', views.index), # 普通路径
    re_path(r'^articles/([0-9]{4})/$', views.articles), # 正则路径
]
```
正则路径中的分组

正则路径中的无名分组：
```
urlpatterns = [
    path('admin/', admin.site.urls),
    re_path("^index/([0-9]{4})/$", views.index),
]

def index(request，year):
    print(year) # 一个形参代表路径中一个分组的内容，按顺序匹配
    return HttpResponse('菜鸟教程')
```
正则路径中的有名分组:
(?P<组名>正则表达式)

路由分发(include)

### 反向解析(路由别名)

反向解析一般用在模板中的超链接及视图中的重定向。

```
普通路径：
在 urls.py 中给路由起别名，name="路由别名"。
path("login1/", views.login, name="login")
在 views.py 中，从 django.urls 中引入 reverse，利用 reverse("路由别名") 反向解析:
return redirect(reverse("login"))
在模板 templates 中的 HTML 文件中，利用 {% url "路由别名" %} 反向解析。
<form action="{% url 'login' %}" method="post"> 

正则路径（无名分组）：

re_path(r"^login/([0-9]{2})/$", views.login, name="login")
在 views.py 中，从 django.urls 中引入 reverse，利用 reverse("路由别名"，args=(符合正则匹配的参数,)) 反向解析。
return redirect(reverse("login",args=(10,)))
在模板 templates 中的 HTML 文件中利用 {% url "路由别名" 符合正则匹配的参数 %} 反向解析。
<form action="{% url 'login' 10 %}" method="post"> 

正则路径（有名分组）：

re_path(r"^login/(?P<year>[0-9]{4})/$", views.login, name="login")
在 views.py 中，从 django.urls 中引入 reverse，利用 reverse("路由别名"，kwargs={"分组名":符合正则匹配的参数}) 反向解析。
return redirect(reverse("login",kwargs={"year":3333}))
在模板 templates 中的 HTML 文件中，利用 {% url "路由别名" 分组名=符合正则匹配的参数 %} 反向解析。
<form action="{% url 'login' year=3333 %}" method="post">

### 命名空间(路由分发)

include(("app名称：urls"，"app名称"))
path("app01/", include(("app01.urls","app01"))) 

在 app01/urls.py 中起相同的路由别名。
path("login/", views.login, name="login")

return redirect(reverse("app01:login")
{% url "app名称：路由别名" %}
```
访问时app01/login/


### Django Admin 管理工具
	
通常我们在生成项目时会在 urls.py 中自动设置好，我们只需去掉注释即可。
python manage.py createsuperuser

```
# Register your models here.
admin.site.register(Test)
```

admin.ModelAdmin的field定义显示字段
```
#ContactAdmin 类，用以说明管理页面的显示格式。
# Register your models here.
class ContactAdmin(admin.ModelAdmin):
    fields = ('name', 'email')
 
admin.site.register(Contact, ContactAdmin)

```

将输入栏分块：
```
# Register your models here.
class ContactAdmin(admin.ModelAdmin):
    fieldsets = (
        ['Main',{
            'fields':('name','email'),
        }],
        ['Advance',{
            'classes': ('collapse',), # CSS格式：隐藏
            'fields': ('age',),
        }]
    )
 
admin.site.register(Contact, ContactAdmin)
```

内联(Inline)显示
显示出外键关系
```
from django.contrib import admin
from TestModel.models import Test,Contact,Tag
 
# Register your models here.
class TagInline(admin.TabularInline):
    model = Tag
 
class ContactAdmin(admin.ModelAdmin):
    inlines = [TagInline]  # Inline
    fieldsets = (
        ['Main',{
            'fields':('name','email'),
        }],
        ['Advance',{
            'classes': ('collapse',),
            'fields': ('age',),
        }]
 
    )
 
admin.site.register(Contact, ContactAdmin)
admin.site.register([Test])
```

列表页的显示
list_display = ('name','age', 'email') # list

search_fields 为该列表页增加搜索栏：
search_fields = ('name',)
