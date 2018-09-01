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

## 创建Django项目：
	命令行：django-admin startproject [NAME]

## 运行Django项目：
- 在项目文件夹下运行python manage.py runserver
- 指定运行端口：python manage.py runserver [端口号]

- 停止正在运行的项目：
	ctrl+C
