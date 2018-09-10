# 虚拟环境
## virtualenv:
- 安装：pip install virtualenv
- 创建：virtualenv [VIRTUALENVNAME]
### 进入虚拟环境：
- 进入到虚拟环境的scripts文件夹，执行activate。
- 创建环境时指定python版本：
	virtualenv -p /path(python)/ [VIRTUALENVNAME]
### 插件：virtualenvwrapper
- 安装：pip install virtualenvwrapper-win
- 使用命令：
	- mkvirtualnev [VITRUALENVNAME]：
		命令创建的虚拟环境会默认在用户命令下创建一个Envs的目录，所有的虚拟环境文件夹都会放在这个目录下。
	- workon VIRTUALENVNAME:
		进入指定的虚拟环境
	- rmvirtualenv VIRTUALENVNAME:
		删除指定的虚拟环境
	- lsvirtualnev:
		列出所有的虚拟环境
	- cdvirtualenv:
		进入到虚拟环境所在目录
	- 修改mkvirtualenv的默认路径：
		在环境变量中添加一个WORKON_HOME变量，在变量值中添加想要修改的路径
	- 创建虚拟环境时指定python版本：
				mkvirtualenv --python==/path(python) VIRTUAENVNAME
# Django:
- 官网：https://wwwdjangoproject.com/
- Django Book2.0版本的中文文档：http://djangobook.py3k.cn/2.0/chapter01
- Django2.0版本的中文文档：http://python.usyiyi.cntranslate/django2/index.html

## 创建Django项目：命令行下执行：
	django-admin startproject [NAME]

## 运行Django项目：
- 在项目文件夹下运行:
```
python manage.py runserver
```
- 指定运行端口：
```
python manage.py runserver [端口号]
```
- 指定IP地址：
```
python manage.py runserver [0.0.0.0:0000]
```
- 停止正在运行的项目：
	ctrl+C

## 项目结构
- `manage.py`:以后和项目的交互基本上都是基于这个文件。一般都是在终端输入`python manage.py [子命令]`。可以输入`python manage.py help`看下能做什么事情。
- `settings.py`:本项目的设置项，以后所有和项目相关的配置都放在这个里面。
- `urls.py`:这个文件是用来配置URL路由的。比如访问http://127.0.0.1/news/是访问新闻列表页，这些东西就需要在这个文件中完成。
- `wsgi.py`:项目与WSGI协议兼容的web服务器入口，部署的时候需要用到的，一般情况下不需要修改。

# URL分发器

## 视图函数
- 视图函数的第一个参数必须是request，这个参数绝对不能少
- 视图函数的返回值必须是`django.http.response.HttpResponseBase`的子类的对象。

## url传递参数

### url映射
- 为什么回去urls.py文件中寻找映射呢？  是因为在`settings.py`文件中配置了`ROOT_URLCONF`为`urls.py`。
- 在`urls.py`中我的所有的映射，都应该放在`urlpatterns`这个变量中。
- 所有的映射不是随便写的，而是使用`path`函数或者是`re_path`函数进行包装的

### url传参数
- 采用在url中使用变量的方式：在path的第一个参数中，使用`<参数名>`的方式可以传递参数。然后在视图函数中也要写一个参数，视图函数的参数必须与url中的参数保持一致。另外，url中可以传递多个参数。
- 采用查询字符串的方式：在url中不需要单独的匹配查询字符串的部分。只需要在视图函数中使用`request.GET.get('参数名称')`的方式来获取。示例代码如下：
```python
def auther_detail(request):
	auther_id = request.GET.get('id')
	text = '作者的id是：%s'%auther_id
	return HttpResponse(text)
```
	因为查询字符串使用的是`GET`请求，所以我们通过`request.GET`来获取参数。并且因为`GET`是一个类似于字典的数据类型，所有获取值跟字典的方式都是一样的。

 
# URL命名

## 为什么需要url命名
因为url是经常变化的，如果在代码中写死，需要经常改代码。给url取个名字，以后使用url的时候就是用他的名字进行反转就可以了，就不需要一个一个去改url。
反转需要用到reverse函数：`from django.shortcuts import reverse`
重定向用法：
```python
def index(request):
# 判断是否有username，有就显示前台首页，没有就重定向去登录页面
    username = request.GET.get('username')
    if username:
        return HttpResponse('前台首页')
    else:
        #通过login名称找到对应的url，通过reverse反转成地址
        return redirect(reverse('lonin'))
```

## 如何给一个url命名
在`path`中，传递一个`name`参数就可以指定。示例代码如下：
```python
urlpatterns = [
    path('',views.index,name = 'index'),
    path('login/',views.login,name = 'login')
]
```
## 应用命名空间
在多个app之间，有可能会产生同名的url，这时候为了避免反转url的时候产生混淆，可以使用应用命名空间，来做区分。定义命名空间非常简单，只要在`app`的`urls.py`中定义一个叫做`app_name`的变量，来指定这个应用的命名空间。实例代码如下：
```python
# 应用命名空间的变量叫做app_name
app_name = 'front'
urlpatterns = [
    path('',views.index,name = 'index'),
    path('signin/',views.longin,name = 'login')
]
```
以后在做反转的时候就可以使用`应用命名空间:url名称`的方式进行反转。示例代码如下：
```python
return redirect(reverse('cms:login'))
```
## 应用命名空间和实例命名空间

# 模板介绍

模板可以让视图函数返回复杂的html代码，从而使浏览器可以渲染出非常漂亮的页面。
目前市面上有非常多的模板系统，最知名最好用的就是`DTL(Django Templage Language)`和`jinja2(flask)`。DTL为Django内置模板语言，可以与django做到无缝衔接。

- DTL与普通html文件的区别
DTL模板是一种带有特殊语法的HTML文件，这个HTML文件可以被Django变异，可以传递参数进去，实现数据动态化。在编译完成后生成一个普通的HTML文件，然后发送给客户端。

## 渲染模板
渲染模板有多重方式，这里讲下两种常用的方式。

- `render_to_string`：找到模板，然后将模板编译后渲染成python字符串格式，最后再通过`HttpResponse`类包装成一个HttpResponse对象返回回去。示例代码如下：
```python
from django.templage.loader import render_to_string
from django.http import HttpResponse
def book_detail(request,book_id):
    html = render_to_string('detail.html')
    return HttpResponse(html)
```
- `render`:django还提供了一个更加简便的方式，直接将模板渲染成字符串和包装成`HTTPResponse`对象一步到位完成。示例代码如下：
```python
from django.shortcuts import render
def book_id(request):
    return render(request,'detail.html')
```

## 模板查找路径

在项目的`settings.py`文件中，有一个`TEMPLATES`配置，这个配置包含了模板引擎的配置，模板查找路径的配置，模板上下文的配置等。模板路径可以在两个地方配置。

- `DIRS`:这个一个列表，存放模板文件夹`templates`的地址
- `APP_DIRS`:默认为True，这个开启后，会在INSTALL_APP的安装了的APP的目录下的templates目录查找模板文件。
## 模板查找顺序
`DIRS` --> 自己的APP下的`templats` --> 其他APP下的`templates` 

# DTL模板语法

## 变量

- 在模板中还用变量，需要将变量放到`{{}}`中。
- 如果想要访问对象的属性，那么可以通过`对象.属性名`来进行访问。
```python
class Person(object):
    def __init__(self,username):
        self.username = username
p = Person()
context = {
    'person':p
}

```
    以后想要访问`person`的`username`，那么就是通过`person.username`来访问。
- 如果想要访问一个字典的key对应的value，那么只能通过`字典名.key`的方式进行访问，不能通过`中括号[]`的形式进行访问。
```python
context = {
    'person':{
        'username':'zhangsan'
    }
}
```
    那么以后在模板中访问`username`，就是用`person.username`
- 因为在访问字典的`key`时候也是使用`.`来访问，因此不能在字典中定义字典本身就有的属性名单做`key`，否则字典的那个属性将变成字典中的key了。
```python
context = {
    'person':{
        'username':'zhangsan',
        'keys':'abc'
    }
}
```
    以上因为将`keys`作为`person`这个字典的key了，所有字典`personde`的`keys`属性不能调用
- 如果想要访问列表或者元组，也要通过`.`的方式进行访问，不能通过`中括号[]`来进行访问。示例代码如下：
```python
{{person.1}}
```

# if语句
- 所有的标签都是在`{% %}`之间
- if标签有闭合标签，就是`{%endif%}`
- if标签的判断运算符，跟python中是一样的。

# for...in...标签

## `for...in...`标签：
`for...in...`类似有python中的`for...in...`。可以遍历列表，元组，字符串，字典等一切可以遍历的对象。示例代码如下：
```html
{% for person in persons%}
<p>{{ person.name }}</p>
{% endfor %}
```
如果想要反向遍历，那么在遍历的时候加上一个`reversed`，示例代码如下：
```html
{% for person in persons reversed%}
<p>{{ person.name }}</p>
{% endfor %}
```

遍历字典的时候，需要使用`items`、`keys`和`values`等方法。在`DTL`中，执行一个方法不能使用圆括号的形式。遍历字典示例代码如下：
```html
{%for key,value in person.items%}
    <li>
        {{key}}:{{value}}
    </li>
{%endfor%}
```

在`for`循环中，`DTL`提供了一些变量可供使用。这些变量如下：
- `forloop.counter`:当前循环的下标，以1作为起始值。
- `forloop.counter0`:当前循环的下标，以0作为起始值。
- `forloop.revcounter`:当前循环的反下标值，到1为止。
- `forloop.revcounter0`:当前循环的反下标值，到0为止。
- `forloop.first`:是否是第一次遍历。
- `forloop.last`:是否是最后一次遍历。

## `for...in...empty`标签
这个标签的使用跟`for...in...`是一样的，不过在遍历的对象没有元素的情况下，会执行`empty`中的内容，示例代码如下：
```html
{% for person in persons %}
    <li>{{person}}</li>
{% empty %}
    <p>暂时没有任何人</p>
{% endfor %}
```
# `url`标签

## `url`标签
在模板中，我们经常要写一些`url`，比如某个`a`标签中需要定义`href`属性。建议使用饭庄的方式来实现，类似于`reverse`一样，示例代码如下：
```html
<a href="{%  url 'book:list' %}">图书列表页面</a>
```

如果`url`反转的时候需要传递参数，那么可以在后面传递，但是参数分位置参数和关键字参数。位置参数和关键字参数不能同时使用，示例代码如下：
```python
# path 部分
path('detail/<book_id>',views.book_detail,name='detail')

# url反转，使用位置参数
<a href="{% url 'book:detail' 1 %}">图书详情页面</a>

# url反转，使用关键字参数
<a href="{% url 'book:detail' book_id=1 %}">图书详情页面</a>

```

如果想要在使用`url`标签的时候传递查询字符串参数，那么必须手动在后面添加，示例代码如下：
```html
<a href="{% url 'book:detail' book_id=1%}?page=1">图书详情页面</a>
```

# Django模板过滤器

## 为什么要用过滤器？
因为在DTL中，不支持给函数传递参数，这将有很大的局限，过滤器其实就是一个函数，可以对需要处理的函数进行处理，并且可以额外接受一个参数（也就是说，最多只能有两个参数）

## add过滤器
将传进来的参数添加到原来的值上面。这个过滤器会尝试将`值`和`参数`转换成整型然后进行相加。如果转换成整型过程中失败了，那么会将`值`和`参数`进行拼接。如果是字符串，那么会拼接成字符串，如果是列表，那么会拼接成一个列表。示例代码如下：
```
{{ value|add:'2'}}
```

如果`value`等于4，那么结果是`6`，如果`value`是一个普通的字符串，比如`abc`,那么结果将是`abc2`，`add`过滤器的源代码如下：
```python
def add(value,arg):
'''Add the arg to the value.'''
try:
    return int(value) + int(arg)
except(ValueError,TypeError):
try:
    return value + arg
except Exception:
    return ''
```

## cut过滤器
移除值中所有指定的字符串。类似于`python`中的`replace(args,"")`。示例代码如下：
```
{{ value|cut:" "}}
```

以上示例将会移除`value`中所有的空格字符。`cut`过滤器的源代码如下：
```python
def cut(value,arg):
    safe = isinstance(value,SafeData)
    value = value.replace(arg,'')
    if safe and arg != ';':
        return mark_safe(value)
    return value
```

## `date`过滤器

### 数据
    context = {
        "today":datetime.now
    }

### 模板
    {{ today|date:"Y/m/d" }}

上面将会输出当前日期，格式为：`年/月/日`，更多时间格式化方式如下：
- Y：年份，四位数
- m：月份，两位数
- n：月份，1-9前面没有0
- d：天，两位数
- j：天，1-9前面没有0
- g：小时，12小时制，1-9前面没有0
- h：小时，12小时制，两位数
- G：小时，24小时制，1-9前面没有0
- H：小时，24小时制，两位数
- i：分钟，两位数
- s：秒，两位数

# 模板继承

在前端页面开发中，有些代码是需要重复使用的，这种情况可以使用`incloud`标签来实现，也可以使用另外一个比较强大的方式来实现，那就是继承模板。模板继承类似于`Python`中的类，在父类中可以先定义好一些子模板需要用到的代码，然后子模板直接继承就可以了。并且因为子模板肯定有自己的不同代码，因此可以在父母版中定义一个`block`接口，然后子模板再去实现。以下是父模板的代码：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Title</title>
</head>
<body>
<header>
    <ul>
        <li><a href="/">首页</a></li>
        <li><a href="{% url 'company' %}">公司</a></li>
        <li><a href="{% url 'school' %}">校园</a></li>
        <li>{{ username }}</li>
    </ul>
</header>
<div class="content">
    {% block content %}
        我是父模板中content的代码
    {% endblock%}
</div>
<footer>
    这是footer部分
</footer>
</body>
</html>
```

这个模板，我们取名叫`base.html`，定义好一个简单的`html`骨架，然后定义好两个`block`接口，让子模板来根据具体需求来实现。子模板然后通过`extends`标签来实现，示例代码如下：
```html
{% extends 'base.html' %}
{% block content%}
<!-- 以下代码可以使用到父模板中的content内容 -->
    <p>{{ block.super}}</p>
    这是school的index
{% endblock%}
```

**需要注意的是：extends标签必须放在最开始的位置**
**子模板中的代码必须放在block中，否则将不会被渲染**
如果在某个`block`中需要使用父模板中的内容，那么可以使用`{{ block.super}}`来继承。

# 加载静态文件
在一个网页中，不仅仅只有一个`html`骨架，还需要`css`样式文件，`js`执行文件及一些图片等。因此在`DTL`中，使用`static`标签来加载静态文件。要使用`static`标签，首先需要`{% load static %}`。加载静态文件的步骤如下：

1. 首先确保`django.contrib.staticfiles`已经添加到`settings.INSTALLED_APPS`中。

2. 确保在`settings.py`中设置了`STATIC_URL`。

3. 在已经安装了的`app`下创建一个文件夹叫做`static`，然后再在这个文件夹下创建一个和当前`app`同名的文件夹，再把静态文件放到这个文件夹下。

4. 如果有一些静态文件是不合任何`app`挂钩的，那么可以在`settings.py`中添加`STATICFILES_DIRS`，以后`DTL`就会在这个列表的路径中查找静态文件。可以这样设置：
```python 
STATICFILES_DIRS = [
    os.path.join(BASE_DIR,'static')    
]
```

5. 在模板中使用`load`标签加载`static`标签，比如要加载在项目的`static`文件夹下的`style.css`的文件。那么示例代码如下：
```html
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'style.css'%}">
```

6. 如果不想每次在模板中加载静态文件都使用`load`加载`static`标签，那么可以在`settings.py`中的`TEMPLATES/OPTIONS`中添加`builtins:['django.templatetags.static']`，这样以后模板中就可以直接使用`static`标签，而不需要手动`load`了。

7. 如果没有在`settings.INSTALLED_APPS`中添加`django.contrib.staticfiles`。那么我们就需要手动的将请求静态文件的 `url`与静态文件的路径进行映射了。示例代码如下：
```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    
] + static(settings.STATIC_URL,documents_root = settings.STATIC_ROOT)
```

# ORM模型
`ORM`，全称`Object Relational Mapping`,中文叫做对象关系映射通过`ORM`我们可以通过类的方式去操作数据库，而不用再写原生的SQL语句。通过把表映射成类，把行作实例，把字段作为属性，`ORM`在执行对象操作的时候最终还是会把对应的操作转化为数据库的原生语句。

## 创建ORM模型
`ORM`模型一般都是防在`app`的`modeld.py`文件夹中，每个`app`都可以有自己的模型。并且如果这个模型想要映射到数据库中，那么这个`app`必须要放在`settings.py`的`INSTALLED_APP`中进行安装。示例代码如下：
```python
from django.db import models
class Book(models.Model):
    name = models.CharField(max_length=100,null=False)
    auther = models.CharField(max_length=100,null=False)
    pub_time = models.DateTimeField(default=datetime.now)
    price = models.FloatField(default=0)
```
以上便定义了一个模型，这个模型继承自`django.db.models.Model`类，如果这个模型想要映射到数据库中，就必须继承自这个类。这个模型以后映射到数据库中，表名是模型名称的小写形式，为`book`。

## 映射模型到数据库
将`ORM`模型映射到数据库，总结起来就是以下几步：
1. 在`settings.py`中，配置好`DATABASES`，做好数据库相关的配置。
2. 在`app`中的`models.py`中定义好模型，这个模型必须继承自`django.db.models.Model`。
3. 将这个`app`添加到`settings.py`的`INSTALLED_APP`中。
4. 在命令行终端，进入到项目所在的路径然后执行命令`python manage.py makemigrations`来生成迁移脚本文件。
5. 在命令行执行命令：`python manage.py migrate`来将迁移脚本文件映射到数据库中。

# ORM对数据库的基本操作

## 添加数据
只需要使用ORM模型创建一个对象，然后在调用这个对象的`save()`方法进行保存就可以了。示例代码如下：
```python
book = Book(name='西游记',author='吴承恩',price=100)
book.save()
```

## 查找数据
所有的查找工作都是使用模型上的`objects`属性来完成，也可自定义方法。
1. 根据主键进行查找：
```python
book = Book.objects.get(pk=1)
```
2. 根据其他字串进行查找:
```python
books = Book.objects.filter(name='三国演义')
```
使用`filter`方法返回回来的是一个`QuerySet`对象，这个对象类似于列表，可以使用这个对象的`first`方法来获取第一个值。

## 删除数据
首先查找到对应的数据模型，然后在调用这个模型的`delete`方法即可删除，示例代码如下：
```python
book = Book.objects.get(pk=1)
book.delete()
```

## 修改数据
首先查找到想要修改的数据，然后修改这个模型上对应属性的值，再执行`save()`方法：
```python
book = Book.objects.get(pk=1)
book.price = 200
book.save()
```

# ORM模型常用属性

## 常用字段
### AutoField
映射到数据库中是`int`类型，可以有自动增长的特性。一般不需要使用这个类型，如果不指定主键，那么模型会自动生成一个叫做`id`的自动增长的主键。

### BigAutoField
64位的整型，类似于`AutoField`，只不过产生的数据范围是从1~(2^64-1)。

### BooleanField
在模型层面接受的是True/False。在数据层面是`tinyint`类型。如果没有指定默认值，默认是`none`

### CharField
在数据库层面是`varchar`类型。在`python`层面就是普通的字符串。这个类型在使用的时候必须要指定最大的长度，也即必须要传递`max_length`这个关键字参数进去。

### DateField
日期类型。在`python`中是`datetime.date`类型，可以记录年月日。在映射到数据库中也是`date`类型。使用这个`field`可以传递以下几个参数：
1. `auto_now`：在每次这个数据保存的时候，都是用当前的时间。比如作为一个记录修改日期的字段，可以将这个属性设置为`True`。
2. `auto_now_add`：在每次数据第一次被添加进去的时候，都是用当前的时间。比如作为一个记录第一次入库的字段，可以将这个顺序性设置为`True`。

### EmailField
类似于`CharField`。在数据库层面也是一个`varchar`类型，最大长度是254个字符。

### FileField
用来存储文件的。

### ImageField
用来存储图片文件的。

### FloatField
浮点类型。映射到数据中是`float`类型。

### IntegerField
整型。

### BigInterField
大整型。

### SmallIntegerField
小整型

### PositiveSmallIntegerField
正小整型

### TextField
大量的文本类型。映射到数据库中是`longtext`类型。

### UUIDField
只能存储`uuid`格式的字符串。`uuid`是一个32位的全球唯一的字符串，一般用来做主键。

### URLField
类似于`CharField`，只不过只能用来存储`url`格式的字符串。并且默认的`max_length`是200.

# 外键
外键用来关联其他表或者本表内的其他数据，示例代码如下：
```python
class Article(model.Model):
    title = models.CharField(max_lengt=100)
    content = models.TextField()

    author = models.ForeignKey('user.User',on_delete=models.CASCADE)
```
`ForeignKey`第一个参数接收引用数据所在的表的名字，如果是本表，可以用`self`,第二个参数`on_delete`参数指示当要引用的那一条数据被删除了之后这条数据该如何处理。

# 表关系
表之间的关系都是通过外键来进行关联的。而表之间的关系，无非就是三种关系：一对一，一对多(多对一),多对多。

## 一对多
- 应用场景：比如文章和作者之间的关系。一个文章只能由一个作者编写，但是一个作者可以写多篇文章。文章和作者之间的关系就是典型的多对一关系。
- 实现方式：一对多或者多对一，都是通过`ForeighKey`来实现的。世事示例代码如下：
```python
class User(models.Model):
    username = models.CharField(max_length=100)
    password = models.CharField(max_length=100)

class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey('User',on_delete=models.CASCADE)
```

那么以后在给`Article`对象指定`author`，就可以使用以下代码：
```python
article = Article(title='abc',content='123')
author = User(username='mike',password='111111')
author.save()
article.author = author
article.save()
```
并且以后如果想要获取某个用户下面的所有文章，可以通过`article_set`来实现。示例代码如下：
```python
user = User.objects.first()
articles = user.article_set.all()
for article in articles:
    print(article)
```
并且如果想要将文章添加到某个分类中。可以使用以下的方式：
```python
user = User.objects.first()
article = Article(title='111',content='bbb')
article.author = User.objects.first()

user.article_set.add(article,bulk=False)
```
- 使用`bulk=False`，那么`django`会自动保存`article`，而不需要再添加到`user`之前，先将`article`保存到数据库中。 

# 查找条件
- `exact`:在底层会被翻译成`=`。`id__exact=1`等同于`id=1`。
- `iexact`：在底层会被翻译成`like`。可以用`title__iexact=%[content]%`进行模糊查找。

`like`和`=`：大部分情况下是等价的，只有少数情况下是不等价的。
`exact`和`iexact`:同上
**大部分情况下直接使用`=`就行，不用`exact`与`iexact`。
