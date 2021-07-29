https://www.liujiangblog.com/course/django/95

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
```
class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    birth_date = models.DateField()

    def baby_boomer_status(self):
        "Returns the person's baby-boomer status."
        import datetime
        if self.birth_date < datetime.date(1945, 8, 1):
            return "Pre-boomer"
        elif self.birth_date < datetime.date(1965, 1, 1):
            return "Baby boomer"
        else:
            return "Post-boomer"

    @property
    def full_name(self):
        "Returns the person's full name."
        return '%s %s' % (self.first_name, self.last_name)
        
    def __str__(self):
        return self.first_name + self.last_name
   ```     
        



