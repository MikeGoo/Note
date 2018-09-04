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