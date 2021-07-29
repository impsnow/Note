## Django ORM - 单表实例
增：
```
    book = models.Book(title="菜鸟教程",price=300,publish="菜鸟出版社",pub_date="2008-8-8") 
    book.save()
    
    #books = models.Book.objects.create(title="如来神掌",price=200,publish="功夫出版社",pub_date="2010-10-10") 
```

python manage.py makemigrations让修改动作保存到记录文件中

python manage.py migrate，让操作实际应用到数据库上

## 模型的属性
模型还有一个不为人知的隐藏属性_state。

_state自己又有2个属性：adding和db

adding：一个标识符，如果当前的模型实例还没有保存到数据库内，则为True，否则为False
db：一个字符串指向某个数据库，当前模型实例是从该数据库中读取出来的。

## 模型方法:






