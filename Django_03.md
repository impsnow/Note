https://www.liujiangblog.com/course/django/2

## Form组件 
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
