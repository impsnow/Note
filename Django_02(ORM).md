https://www.liujiangblog.com/course/django/95

## Django ORM - 单表实例
增：实例化模型或object操作
```
    book = models.Book(title="菜鸟教程",price=300,publish="菜鸟出版社",pub_date="2008-8-8") 
    book.save()
    
    #books = models.Book.objects.create(title="如来神掌",price=200,publish="功夫出版社",pub_date="2010-10-10") 
```
查：
```
books = models.Book.objects.all() 
books = models.Book.objects.filter(pk=5)
books = models.Book.objects.exclude(publish='菜鸟出版社', price=300)
books = models.Book.objects.get(pk=5)
books = models.Book.objects.order_by("-price").reverse()
books = models.Book.objects.filter(price=200).count() # 查询符合条件数据的数量
books = models.Book.objects.first() # 返回所有数据的第一条数据 :模型类的对象: Book Object (5)
books = models.Book.objects.last() # 返回所有数据的最后一条数据
books = models.Book.objects.values("pk","price") # 查询所有的id字段和price字段的数据
books = models.Book.objects.values_list("publish").distinct() # 对模型类的对象去重没有意义，因为每个对象都是一个不一样的存在。
books = models.Book.objects.filter(price__in=[200,300])  # 查询价格为200或者300的数据
books = models.Book.objects.filter(price__gt=200)
books=models.Book.objects.filter(price__range=[200,300])
books=models.Book.objects.filter(title__contains="菜")
books=models.Book.objects.filter(title__icontains="python") # 不区分大小写
books=models.Book.objects.filter(title__startswith="菜")
books=models.Book.objects.filter(title__endswith="教程")
books=models.Book.objects.filter(pub_date__year=2008) 
```
get() 方法用于查询符合条件的返回模型类的对象符合条件的对象只能为一个，如果符合筛选条件的对象超过了一个或者没有一个都会抛出错误。

last() 方法返回最后一条数据返回的数据是模型类的对象不能用索引下标 [-1]，ORM 没有逆序索引。

exists() 方法用于判断查询的结果 QuerySet 列表里是否有数据。

注意：filter 中运算符号只能使用等于号 = ，不能使用大于号 > ，小于号 < ，等等其他符号。

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
        
##  关系类型
### 多对一（ForeignKey）
外键要定义在‘多’的一方！

多对一字段的变量名一般设置为关联的模型的小写单数，而多对多则一般设置为小写复数。

如果你要关联的模型位于当前模型之后，则需要通过字符串的方式进行引用

manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE, **options)

on_delete:
CASCADE：模拟SQL语言中的ON DELETE CASCADE约束，将定义有外键的模型对象同时删除！
PROTECT:阻止上面的删除操作，但是弹出ProtectedError异常
SET_NULL：将外键字段设为null，只有当字段设置了null=True时，方可使用该值。
SET_DEFAULT:将外键字段设为默认值。只有当字段设置了default参数时，方可使用。

### 多对多（ManyToManyField）
ManyToManyField(to, **options)

多对多的字段可以定义在任何的一方，请尽量定义在符合人们思维习惯的一方

建议为多对多字段名使用复数形式。



### 一对一（OneToOneField）
OneToOneField(to, on_delete, **options)


