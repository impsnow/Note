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

form = NameForm(request.POST) 这叫“绑定数据到表单”
 is_valid() 为 True ，我们就能在其 cleaned_data 属性中找到所有通过验证的表单数据。
表单的 is_bound 属性将告诉您一张表单是否具有绑定的数据。

HTML5输入类型和浏览器验证
如果您的表单包含 URLField ， EmailField 或者其他整数字段类型，Django将使用 url ， email 和 number HTML5输入类型。默认情况下，浏览器可能会在这些字段上应用他们自己的验证，这也许比Django的验证更加严格。如果您想禁用这个行为，请在 form 标签上设置 novalidate 属性，或者在字段上指定一个不同的控件，比如 TextInput 。

每个表单字段都有一个相对应的 控件类 widget,字段都有合适的默认控件,也可自定义：
message = forms.CharField(widget=forms.Textarea)


###  django.forms.ModelForm
会Model 类构建一张对应字段及其属性的表单。










