简介

> Django是一个开放源代码的Web应用框架，它最初是被开发来用于管理劳伦斯出版集团旗下的一些以新闻内容为主的网站的，即是CMS（内容管理系统）软件。并于2005年7月在BSD许可证下发布。这套框架是以比利时的吉普赛爵士吉他手Django Reinhardt来命名的。
> 	重量级，替开发者想了太多的事情，帮开发者做了很多的选择，内置了很多的功能
> 官方网站
> 	http://www.djangoproject.com
> 使用版本1.11.7
> 	LTS：长期支持版本
> 	以后再学2.2 LTS

虚拟环境

```
创建虚拟环境
	virtualenv django1905
	source django1905/bin/activate
```

虚拟化技术

```
（1）虚拟机      
（2）虚拟容器
	Docker  
		持很多种语言
（3）虚拟环境--迷你
	python专用
	将python依赖隔离
```

安装

```
pip install django
	pip install django==1.11.7   一定要使用==
	pip install django==1.11.7 -i https://pypi.douban.com/simple  豆瓣源
查看django是否安装成功
    pip freeze
    pip list
    进入python环境
        import django
        django.get_version()
```

创建django项目

```
django-admin startproject 项目名字
	tree命令观察项目结构 [sudo apt install tree]
```

项目结构

```
项目名字
	manage.py   管理整个项目的文件-以后的命令基本都通过他来调用
	项目名字
		__init__   python包而不是一个文件夹
		settings   项目全局配置文件
			ALLOWED_HOST = ["*"]
			修改settings
				LANGUAGE_CODE = 'zh-hans'
				TIME_ZONE = 'Asia/Shanghai'
		urls   根路由-(p1,p2)
		wsgi   服务器网关接口-[webserver gateway interface]   用在以后项目部署上，前期用不到
```

启动项目

```
python manage.py runserver   使用开发者服务器启动项目，默认会运行在本机的8000端口上
启动服务器命令
	-python manage.py runserver
	-python manage.py runserver 9000
	-python manage.py runserver 0.0.0.0:9000
```

创建一个应用

```
python manage.py startapp App/django-admin startapp App
App结构
	__init__
	views 视图函数  参数是request 方法的返回值类型是HttpResponse
	models 模型
	admin 后台管理
	apps 应用配置
	tests 单元测试
	migrations 迁移目录
		__init__
```

拆分路由器

```
python manage.py startapp Two
Two下创建urls
	urlpatterns = [
        url(r'^index/',views.index)
	]
创建views方法
主路由引用
	url(r'^two/',include('Two.urls'))
访问url
	127.0.0.1:8000/two/index/
```

编写第一个请求

```
1.编写一个路由
	url(p1,p2)
		url(r'^index/',views.index)
	p1-正则匹配规则 p2-对应的视图函数
2.编写视图函数
	-本质上还是一个函数
		def index(request):
			return HttpResponse('123')
		要求：只是默认第一个参数是一个request，必须返回一个response
	-返回值
		-HttpResponse()
			HttpResponse('123')
			HttpResponse('<h1>123</h1>')
		-render
			在App下创建templates [注意：名字是固定的，不能打错单词]
			render方法的返回值类型也是一个HttpResponse类型的 [要求：第一个参数是request，第二个参数是页面]
	××× 需要在settings里的INSTALLED_APPS设置App路径 ×××
	将应用注册到项目的settings中INSTALLED_APPS中
		加载路由的方式
			INSTALLED_APPS --> 'Two' / 'Two.apps.TwoConfig'(1.9之后才能使用)
3.模板配置有两种情况
	-在App中进行模板配置
		-只需在App的根目录创建templates文件夹即可
		-必须在INSTALL_APP下安装app
	-在项目目录中进行模板配置
		-需要在项目目录中创建templates文件夹并标记
		-需要在settings中进行注册
			settings --> TEMPLATES --> DIRS --> os.path.join(BASE_DIR,'templates')
	[开发中常用项目目录下的模板--模板可以继承、复用]
```

django的工作机制

```
1.用manage.py runserver启动Django服务器时就载入了在同一个目录下的settings.py。
该文件包含了项目中的配置信息，如URLConf等，其中最重要的配置就是ROOT_URLCONF，它告诉Django模块应该用作本站的URLConf，默认是urls.py

2.当访问url的时候，Django会根据ROOT_URLCONF设置来装载URLConf

3.然后按顺序逐个匹配URLConf里的URLpatterns
如果找到则会调用相关的视图函数，并把HttpRequest对象作为第一个参数（通常是request）

4.最后该view函数负责返回一个HttpResponse对象
```













