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
