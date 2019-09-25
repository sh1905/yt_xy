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

模板显示

```
先挖坑
	{{ var }}
再填坑
	渲染模板的时候传递上下文进来 [上下文是一个字典]
	content = {'key':'value'}
模板的兼容性很强
	不传入不会报错，多传入也会自动优化掉
浏览器不认模板
	浏览器也叫做html解析器，只识别html文件
	在到达浏览器之前，已经进行了转换，将模板语言转换成了html
for支持
	{% for %}
render底层实现：应用场景-发送邮件，邮件的内容需要使用render方法来操作
	加载
		three_index = loader.get_template('three.html')
		content = {'xxx':'xxxx'}
	渲染
		result = three_index.render(content=content)
		return HttpResponse(result)
```

修改数据库

```
在settings中的DATABASE中进行修改，实际上都是关系型数据库
mysql
	'ENGINE':'django.db.backends.mysql',
	NAME 数据库名字
	USER 用户名字
	PASSWORDD 密码
	HOST 主机
	PORT 端口号
```

DML

```
迁移
	生成迁移
		python manage.py makemigrations
	执行迁移
		python manage.py migrate
ORM
	Object relational Mapping 对象关系映射
	将业务逻辑和sql进行了一个接耦合，通过models定义实现数据库表的定义
模型定义
	-继承models.Model
	-会自动添加主键列
	-必须指定字符串类型属性的长度
		class Stident(models.Model):
			name = models.CharField(max_length=16)
			age = models.IntegerField(default=1)
存储数据
	创建对象进行save()
数据查询
	模型.objects.all()
	模型.objects.get(pk=2)
更新
	基于查询 save()
删除
	基于查询 delete()
```

django shell

```
python manage.py shell
django终端 集成了django环境的python终端，通常用来调试
eg:
  from Two.models import Student
  students = Student.objects.all()
  for students in students.all():
  	print(students.name)
```

数据级联 — 一对多

```
模型关系
	class Grade(models.Model):
		g_name = models.CharField(max_length=32)
	class Student(models.Model):
		s_name = models.CharField(max_length=16)
		s_grade = models.ForeignKey(Grade)
多获取一
	就是一个书写的属性
	eg：根据学生找班级名字
        student = Student.objects.get(pk=2)
        grade = student.s_grade
        return HttpResponse(grade.g_name)
一获取多
	多的set
	eg：根据班级找所有的学生
		grade = Grade.objects.get(pk=2)
		students = grade.student_set.all()
		content = {
            'students':students
		}
		return render(request,'students_list.html',content)
```













