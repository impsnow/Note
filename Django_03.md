https://www.liujiangblog.com/course/django/2

##  Form组件 django.forms.Form
Django Form 组件用于对页面进行初始化，生成 HTML 标签，此外还可以对用户提交对数据进行校验（显示错误信息）。
form.py

label：输入框前面的文本信息。
error_message：自定义显示的错误信息，属性值是字典， 
其中 required 为设置不能为空时显示的错误信息的 key。
```
from django import forms
from django.core.exceptions import ValidationError
from app01 import models

class EmpForm(forms.Form):
    name = forms.CharField(min_length=4, label="姓名", error_messages={"min_length": "你太短了", "required": "该字段不能为空!"})
    age = forms.IntegerField(label="年龄")
    salary = forms.DecimalField(label="工资")
	
	
```
表单类的字段会映射到HTML表单的 <input> 元素。 ModelForm 通过 Form 映射模型类的字段到HTML表单的 <input> 元素

form = NameForm(request.POST) 这叫“绑定数据到表单” (未验证)

 is_valid() 为 True ，我们就能在其 cleaned_data 属性中找到所有通过验证的表单数据。
 
表单的 is_bound 属性将告诉您一张表单是否具有绑定的数据。

HTML5输入类型和浏览器验证

如果您的表单包含 URLField ， EmailField 或者其他整数字段类型，Django将使用 url ， email 和 number HTML5输入类型。默认情况下，浏览器可能会在这些字段上应用他们自己的验证，这也许比Django的验证更加严格。如果您想禁用这个行为，请在 form 标签上设置 novalidate 属性，或者在字段上指定一个不同的控件，比如 TextInput 。

每个表单字段都有一个相对应的 控件类 widget,字段都有合适的默认控件,也可自定义：
message = forms.CharField(widget=forms.Textarea)

```
from django.http import HttpResponseRedirect
from django.shortcuts import render

from .forms import NameForm

def get_name(request):
    # if this is a POST request we need to process the form data
    if request.method == 'POST':
        # create a form instance and populate it with data from the request:
        form = NameForm(request.POST)
        # check whether it's valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required
            # ...
	    subject = form.cleaned_data['subject']
            # redirect to a new URL:
            return HttpResponseRedirect('/thanks/')

    # if a GET (or any other method) we'll create a blank form
    else:
        form = NameForm()

    return render(request, 'name.html', {'form': form})
```
表单渲染成html时 不包含外层 <form> 标签以及 submit 控件。这些必须由你自己提供。
{{ form.as_table }}
{{ form.as_p }}
{{ form.as_ul }}
必须自己提供外层的 <table> 或 <ul> 元素。
	
<p><label for="id_subject">Subject:</label>
    <input id="id_subject" type="text" name="subject" maxlength="100" required></p>

手动渲染字段:	
```
{{ form.non_field_errors }}   //非字段验证错误信息
<div class="fieldWrapper">
    {{ form.subject.errors }}   //显示该字段的错误信息列表，它被渲染成无序列表
    <label for="{{ form.subject.id_for_label }}">Email subject:</label>
    {{ form.subject }}
</div>	
```
完整的 <label> 元素还可以使用 label_tag() 
	
	
###  django.forms.ModelForm
会Model 类构建一张对应字段及其属性的表单。


## FormSet

>>> from django.forms import formset_factory
>>> ArticleFormSet = formset_factory(ArticleForm)

```
>>> import datetime
>>> from django.forms import formset_factory
>>> from myapp.forms import ArticleForm
>>> ArticleFormSet = formset_factory(ArticleForm, extra=2)
>>> formset = ArticleFormSet(initial=[
...     {'title': 'Django is now open source',
...      'pub_date': datetime.date.today(),}
... ])

>>> for form in formset:
...     print(form.as_table())
<tr><th><label for="id_form-0-title">Title:</label></th><td><input type="text" name="form-0-title" value="Django is now open source" id="id_form-0-title"></td></tr>
<tr><th><label for="id_form-0-pub_date">Pub date:</label></th><td><input type="text" name="form-0-pub_date" value="2008-05-12" id="id_form-0-pub_date"></td></tr>
<tr><th><label for="id_form-1-title">Title:</label></th><td><input type="text" name="form-1-title" id="id_form-1-title"></td></tr>
<tr><th><label for="id_form-1-pub_date">Pub date:</label></th><td><input type="text" name="form-1-pub_date" id="id_form-1-pub_date"></td></tr>
<tr><th><label for="id_form-2-title">Title:</label></th><td><input type="text" name="form-2-title" id="id_form-2-title"></td></tr>
<tr><th><label for="id_form-2-pub_date">Pub date:</label></th><td><input type="text" name="form-2-pub_date" id="id_form-2-pub_date"></td></tr>	
```



## 表单资源（ Media 类）
静态定义:
```
from django import forms

class CalendarWidget(forms.TextInput):
class Media:
    css = {
        'screen': ('pretty.css',),
        'tv,projector': ('lo_res.css',),
        'print': ('newspaper.css',)
    }
    js = ('animations.js', 'actions.js')
```
	
把 Media 作为动态属性:
```
class CalendarWidget(forms.TextInput):
    @property
    def media(self):
        return forms.Media(css={'all': ('pretty.css',)},
                           js=('animations.js', 'actions.js'))
```

