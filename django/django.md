### 简介

> Django是一个开放源代码的Web应用框架，它最初是被开发来用于管理劳伦斯出版集团旗下的一些以新闻内容为主的网站的，即是CMS（内容管理系统）软件。并于2005年7月在BSD许可证下发布。这套框架是以比利时的吉普赛爵士吉他手Django Reinhardt来命名的。
> 	重量级，替开发者想了太多的事情，帮开发者做了很多的选择，内置了很多的功能
> 官方网站
> 	http://www.djangoproject.com
> 使用版本1.11.7
> 	LTS：长期支持版本
> 	以后再学2.2 LTS

### 虚拟环境

```
创建虚拟环境
	virtualenv django1905
	source django1905/bin/activate
```

### 虚拟化技术

```
（1）虚拟机      
（2）虚拟容器
	Docker  
		持很多种语言
（3）虚拟环境--迷你
	python专用
	将python依赖隔离
```

### 安装

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

### 创建django项目

```
django-admin startproject 项目名字
	tree命令观察项目结构 [sudo apt install tree]
```

### 项目结构

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

### 启动项目

```
python manage.py runserver   使用开发者服务器启动项目，默认会运行在本机的8000端口上
启动服务器命令
	-python manage.py runserver
	-python manage.py runserver 9000
	-python manage.py runserver 0.0.0.0:9000
```

### 创建一个应用

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

### 拆分路由器

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

### 编写第一个请求

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

### django的工作机制

```
1.用manage.py runserver启动Django服务器时就载入了在同一个目录下的settings.py。
该文件包含了项目中的配置信息，如URLConf等，其中最重要的配置就是ROOT_URLCONF，它告诉Django模块应该用作本站的URLConf，默认是urls.py

2.当访问url的时候，Django会根据ROOT_URLCONF设置来装载URLConf

3.然后按顺序逐个匹配URLConf里的URLpatterns
如果找到则会调用相关的视图函数，并把HttpRequest对象作为第一个参数（通常是request）

4.最后该view函数负责返回一个HttpResponse对象
```

### 模板显示

```
先挖坑
	{{ var }}
再填坑
	渲染模板的时候传递上下文进来 [上下文是一个字典]
	context = {'key':'value'}
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
		context = {'xxx':'xxxx'}
	渲染
		result = three_index.render(context=context)
		return HttpResponse(result)
```

### 修改数据库

```
在settings中的DATABASE中进行修改，实际上都是关系型数据库
mysql
	'ENGINE': 'django.db.backends.mysql',
	'NAME': 数据库名字
	'USER': 用户名字
	'PASSWORDD': 密码
	'HOST': 主机
	'PORT': 端口号 -引号加不加都可以
×注意迁移时驱动问题
	-mysqlclient:python2,3都能直接使用，致命缺点：对mysql安装有要求，必须在指定位置存在配置文件
	-mysql-python:python2支持很好，python3不支持
	-pymysql:会伪装成mysqlclient和mysql-python
```

### DML

```
迁移
	生成迁移
		python manage.py makemigrations
	执行迁移
		python manage.py migrate [才会真正在数据库产生表]
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

### django shell

```
使用django终端，python manage.py shell，集成了django环境的python终端，通常用来调试 
eg:
  from Two.models import Student
  students = Student.objects.all()
  for students in students.all():
  	print(students.name)
```

### 数据级联 — 一对多

```
一对多模型关系
	class Grade(models.Model):
		g_name = models.CharField(max_length=32)
	class Student(models.Model):
		s_name = models.CharField(max_length=16)
		s_grade = models.ForeignKey(Grade)
多获取一
	显性属性：就是你可以在类中直接观察到的属性 ->通过多方获取一方 那么可以使用多方调用显性属性直接获取一方数据
	eg：根据学生找班级名字
        student = Student.objects.get(pk=2)
        grade = student.s_grade
        return HttpResponse(grade.g_name)
一获取多
	隐形属性：就是我们在类中观察不到的，但是可以使用的属性 ->通过一方获取多方 那么可以使用一方数据的隐形属性获取多方数据
	eg：根据班级找所有的学生
		grade = Grade.objects.get(pk=2)
		students = grade.student_set.all()
		content = {
            'students':students
		}
		return render(request,'students_list.html',content)
```

### 元信息

```
class Meta：指定表名和字段名字
	db_table = '表名'
	[表的字段一般都是下划线：s_name
	类的属性一般都是驼峰式：sName]
```

### 定义字段

```
字段类型：Charfield，TextField，IntegerField，FloatField，BooleanField，DecimalField，NullBooleanrField，AutoField，FileField，ImageField，DatetimeField
字段约束：max_length，default，unique，index，primary_key，db_column
```

### 模型过滤（查询）

```
Django默认通过模型的object对象实现模型数据查询
Django有两种过滤器用于筛选记录：
	filter:返回符合筛选条件的数据集
	exclude:返回不符合筛选条件的数据集
链式调用：
	多个filter和exclude可以连接在一起查询
	Person.objects.filter().filter().xxxx.exclude().exclude().yyyy
	Person.objects.filter(p_age__gt=50).filter(p_age__lt=80)  注意数据类型
	Person.objects.exclude(p_age__gt=50)
	Person.objects.filter(p_age__in=[50,60,70])
```

### 创建对象的方式

```
1.方式一：常用
	Person = Person()
    person.p_name = 'zs'
    person.p_age = 18
2.方式二：直接实例化对象，设置属性 创建对象，传入属性
	person = Model.objects.create(p_name='zs',p_age=18)
	person.save()
3.方式三：
	person = Person(p_age=18)
4.方式四：在模型类中增加类方法去创建对象 [__init__已经在父类model.Model中使用，在自定义的模型中无法使用]
	@classmethod
	def create(cls,p_name=p_name,p_age=18):
		return cls(p_name=p_name,p_age=p_age)
	person = Person.create('zs')	
```

### 查询集

查询集表示从数据库获取的对象集合，查询集可以有多个过滤器

```
过滤器：过滤器就是一个函数，基于所给的参数限制查询结果集结果，返回查询结果集的方法称为过滤器
查询经过过滤器筛选后返回新的查询集，所以可以写成链式调用
获取查询结果集 QuerySet
-all 模型.objects.all()
-filter 模型.objects.filter()
-exclude 模型.objects.exclude()
-order_by 
	persons = Person.objects.order_by('id') 默认是根据id排序 [注意要写的是模型的属性]
-values
	persons = Person.objects.objects.order_by('id')
	persons.values()
-切片
	限制查询集，可以使用下标的方法进行限制 左闭右开区间[,)
	不支持负数 下标没有负数
	实际上相当于 limit offset
	studentList = Student.objects.all()[0:5]
		第一个参数是offest 第二个参数是limit
-懒查询/缓存集
	查询集的缓存：每个查询集都包含一个缓存，来最小化对数据库的访问
	在新建的查询集中，缓存首次为空，第一次对查询集求值，会发生数据缓存，django会将查询出来的数据做一个缓存，并返回查询结果，以后的查询直接使用查询集的缓存，不会真正的去查询数据库
	[懒查询，只有我们在迭代结果集，或者获取单个对象属性的时候，它才会去查询数据，为了优化我们的结果和查询]
```

### 获取单个对象

```
get
	不存在会抛异常 DoesNotExist
	存在多于一个 MultipleObjectsReturned
	[使用这个函数，记得捕获异常]
last
	返回查询集的最后一个对象
first
	需要主动进行排序
	persons = Person.objects.all().first()
内置函数：框架自己封装的方法，帮助我们来处理业务逻辑
	count 返回当前查询集中的对象个数
	exists 判断查询集中是否有数据，如果有数据返回True，没有反之
```

### 字段查询

```
对sql中where的实现，作为方法filter(),exclude(),get()的参数
语法：属性名称__比较运算符=值
	Person.objects.filter(p_age__gt=18)
条件
	属性__操作符=临界值
	gt：大于 great than
	gte：大于等于 great than equals
	lt：小于 less than
	lte：小于等于 less than equals
		filter(age__gt=30)
	in：是否包含在范围内
		filter(pk__in=[2,4,6])
	exact：判断大小写不敏感
		filter(isDelete=False)
	startswitch：类似于模糊查询like
	endswitch：以XX结束
	contains：是否包含，大小写敏感
		filter(name__contains='赵')
	isnull,isnotnull：是否为空
		filter(name__isnull=False)
	ignore：忽略大小写
		iexact
		icontains
		istartswith
		iendswith
		以上四个在运算符前加上 i(ignore)就不区分大小写了
```

### 时间

```
models.DateTimeField(auto_now_add=True)
year
month 会出现时区问题 需要在settings中设置 USE-TZ=False
day
week_day
hour
minute
second
orders = Order.objects.filter(time__month=9)
```

### 聚合函数

```
[mysql oracle中所说的聚合函数/多行函数/组函数/高级函数，都是一个东西]
模型：
	class Customer(models.Model):
          c_name = models.CharField(max_length=16)
          c_cost = models.IntegerField(default=10)
使用：
	使用aggregate()函数返回聚合函数
	Avg：平均值
	Count：数量
	Max：最大
	Min：最小
	Sum：求和
	eg:Student.objects.aggregate(Max('age'))
```

### 跨关系查询

```
模型：
	class Grade(models.Model):
		g_name = models.CharField(max_length=16)
	class Student(models.Model):
		s_name = models.CharField(max_length=16)
		s_grade = models.ForeignKey(Grade)
使用：
	模型类名__属性名__比较运算符，实际上就是处理数据库中的join
	Grade ->g_name  Student ->s_name s_grade(外键)
	
	gf = Student.objects.filter(name='Tom')
	print(gf[0].s_grade.name)
	||
	grades = Grade.objects.filter(Student__s_name='Tom')  查询Tom所在的班级
```

### F对象

常适用于表内属性的值的比较

```
模型：
	class Commpany(models.Model):
		c_name = models.Charfield(max_length=16)
		c_gril_num = models.IntegerField(default=5)
		c_boy_num = models.IntegerField(default=3)
获取字段信息，通常用在模型的自我属性比较，支持算术运算
eg：男生比女生少的公司
	companies = Company.objects.filter(c_boy_num__lt=F('c_gril_num'))
eg：女生比男生多15个人
	companies = Company.objects.filter(c_boy_num__lt=F('c_gril_num')-15)
```

### Q对象

常适用于逻辑运算--与或非

```
eg：年龄小于25:
	Student.objects.filter(Q(sage__lt=25))
eg：男生人数多于5，女生人数多于10：
	companies = Company.objects.filter(c_boy_num__gt=1).filter(c_gril_num__gt=5)
	companies = Company.objects.filter(Q(c_boy_num__gt=5)|Q(c_girl_num__gt=10))
支持逻辑运算--与或非
eg：年龄大于等于25
	Student.objects.filter(~Q(sage__lt=25))
```



## Template

### 概念：模板

```
	在Django框架中，模板是可以帮助开发者快速生成，呈现给用户页面的工具，模板的设计方式实现了我们MVT中VT的解耦，VT有着N:M的关系，一个V可以调用任意T，一个T可以供任意V使用
	模板处理分为两个过程：加载、渲染
模板中的动态代码段除了做基本的静态填充，可以实现一些基本的运算，转换和逻辑，早期的web服务器，只能处理静态资源请求。模板能处理动态资源请求，依据计算能生成相应的页面
	[注意：在Django中使用的就是Django模板，在flask种使用得是jinja2模板]
```

### 语法

#### 变量

```
视图传递给模板的数据 --获取视图函数传递的数据使用{{ var }}接收
遵守标识符规则：拒绝关键字 保留字 数字。。。如果变量不存在，则插入空字符串
来源：-视图中传递过来的 -标签中，逻辑创建出来的
```

#### 模板中的点语法

```
属性或者方法
	Student.name/student.getname
	class Student(models.Model):
		s_name = models.CharField(max_length=16)
		def get_name(self):
			return self.s-name
弊端：调用对象的方法，不能传递参数[因为连括号都没有]
索引：student.0.gname
字典：student_dict.hobby
```

#### 标签

标签分为单标签和双标签  [双标签必须闭合]

```
[for]
	for
		for i in xxx
	empty
		{% empty %} 判断之前的代码有没有数据，如果没有则显示empty下面的代码
        eg：{% for 变量 in 列表 %}
                语句1
                {% empty %}
                语句2
            {% endfor %}
	forloop
		循环状态的记录
		{{ forloop.counter }} 表示当前是第几次循环，从1开始
		{{ forloop.counter0 }} 表示当前是第几次循环，从0开始
		{{ forloop.recounter }} 表示当前是第几次循环，倒着数，到1停
		{{ forloop.recounter0 }} 表示当前是第几次循环，倒着数，到0停
		{{ forloop.first }} 是否是第一个 布尔值
		{{ forloop.last }} 是否是最后一个 布尔值
		
[if 判断]
	if-else
	if-elif-else
	
[注释]
	单行注释 {# 被注释掉的内容 #}
	多行注释 {% comment %}
				内容
			{% endcomment %}
			
[withratio 乘]
	{% widthratio 数 分母 分子 %}
    {% widthratio count 1 5 %}
    
[整除]
	{% if num|divisibleby:2 %} 整除
    {% if forloop.counter|divisibleby:2 %} 奇偶行变色
    
[ifequal]
	{% ifequal value1 value2 %}
    	语句
    {% endifequal %}
    {% ifequal forloop.counter 5 %}
```

### 过滤器

```
|：管道符 将前面的输出作为后面的输入
add：加
	<h4>{{ count|add:2 }}</h4>
	<h4>{{ count|add:-2 }}</h4>
upper：全部大写
lower：全部小写
safe：确认安装，进行渲染
	code="""
		<h2>睡着了</h2>
		<script type="text/javascript">
			var lis = documnet.getElementsByTagName("li")
			for(var i=0;i<lis.length;i++){
                var li = lis[i]
                li.innerHTML="快乐！！！";
			}
		</script>
	"""
endautoescape：
	{% autoescape off %}
		code
	{% endautoescape %}
```

### 结构标签

```
block：用来规划页面布局，填充页面
	首次出现代表规划，第二次出现代表填坑，带三次出现也代表填坑，默认会覆盖...如果不想被覆盖--block.super
extends：继承
	面向对象的体现，提高模板的复用率
include：包含
	将其它模板作为一部分，包裹到我们的页面中
```

### 加载静态资源

仅在DEBUG模式下可以使用  如果settings中的DEBUG=False 那么是不可以使用的

```
static ->html css js img font
静态资源路径 ->/static/css/xxx.css [不推荐硬编码]
--需要在settings中添加 STATICFILES_DIRS = [os.path.join(BASE_DIR,'static')]
	{% load static %}
	{% static,'css/xxx.css' %}
(load必须在模板中使用)
```

### 常见的请求状态码

```
2xx 成功           200 成功
3xx 重定向         301 永久重定向  302 重定向
4xx 客户端错误      403 防跨站攻击   404 路径错误  405 请求方式错误
5xx 服务端错误      500 业务逻辑错误
```



## view

### 概念

```
视图函数MTV中的View，相当于MVC中的Controller作用，控制器-接收用户输入（请求），协调模板模型，对数据进行处理，负责的模型和模板的数据交互。
视图函数返回值类型：-以Json数据形势返回
					前后端分离 return JsonResponse
				 -以网页的形势返回 
				 	HttpResponse  render redirect
				 	重定向到另一个网页 错误视图(40X,50X)
```

### url匹配正则

```
注意事项：正则匹配时从上到下进行遍历，匹配到就不会继续向后查找了
		匹配的正则前方不需要加反斜线 
		正则前需要加(r)表示字符串不转义
规则：按照书写顺序，从上到下匹配，没有最优匹配的概念，匹配到就停止了
	eg：hehe/hehehe
```

### url接受参数

```python
1.如果需要从url中获取一个值，需要对正则加小括号
	url(r'^grade/(\d+)/',views.getStudents),
    [注意-url匹配中添加了()取参，在请求调用的函数中必须接收]
    def getStudents(request,classId):
        一个参数
2.如果需要获取url路径中的多个参数，那就添加多个括号，默认按照顺序匹配路径名字
	url(r'^news/(\d{4})/(\d+)(\d+)/',views.getNews),
    匹配年月日 def getNews(request,year,month,day):
        多个参数
    eg：def get_time(request,hour,minute,second):
        return HttpResponse("Time %s:%s:%s" %(hour,minute,second))
3.参数也可以使用关键字参数形式
	url(r'^news/(?P<key>\d{4})/(?P<month>\d)+/(?P<day>\d+)/',views.getNews),
    	多个参数并且指定位置
    eg：def get_date(request,month,day,year):
        return HttpResponse("Date %s-%s-%s" %(year,month,day))
    
总结路径参数：
	位置参数-使用圆括号包含规则，一个圆括号代表一个参数，代表视图函数上的一个参数，参数个数和视图函数上的参数一一对应（除默认request）
        eg：127.0.0.1:8000/vie/testRoute/1/2/3
            r('^testRoute/(\d+)/(\d+)/(\d+)/')
    关键字参数-可以在圆括号指定参数名字（?P<name>reg)，视图函数中存在和圆括号中name对应的参数，参数不区分顺序，个数也需要保持一致，一一对应
```

### 内置函数

```
locals()
将局部变量，使用字典的形式打包
key-变量的名字 value-变量的值
```

### 反向解析

用处：获取请求资源路径，避免硬编码

```python
配置
	1.在根urls中
		url(r'^views/',include('ViewsLearn.urls',namespace='view'))
        	在根路由中的include方法中添加namespace参数
    2.在子urls中
    	url(r'^hello/(\d+)',views.hello,name='sayhello'),
        	在子路由中添加name参数
    3.在模板中使用
    	<a href="{% url 'view:sayhello' %}">Hello</a>
        	调用的时候，反向解析的路径不能有编译错误，格式是{% url 'namespace:name' %}

如果存在位置参数
	{% url 'namespace:name' value1 value2... %}
如果存在关键字参数
	{% url 'namespace:name' key1=value1 key2=value2... %}
在模板中使用
	<a href="{% url 'view:sayhello' year=2017 %}">hello</a>

优点：如果在视图，模板中使用硬编码连接，在url配置发生改变时，需要变更的代码会非常多，这样导致我们的代码结构不是很容易维护，使用反向解析可以提高我们代码的扩展性和可维护性。
```



### request对象

概念：django框架根据Http请求报文自动生成的一个对象，包含了请求的各种信息

```
path：请求的wanzheng路径
method：GET 1.11版本最大数据量2K
		POST 参数在请求体中，文件上传等
		请求的方法，常用GET、POST
		应用场景：请后端分离的底层，判断请求方式，执行对应的业务逻辑
GET：QueryDict类字典结构 key-value
	一个key可以对应多个值 get获取最后一个，getlist获取多个 [类似字典的参数，包含了get请求方式的所有参数]
POST：类似字典的参数，包含了post请求方式的所有参数
enconding：编码方式，常用utf-8
FILES：类似字典的参数，包含了上传的文件 文件上传的时候会使用
	页面请求方式必须是post form的属性enctype=multipart/form-data
	flask和django通用的，一个是专属django
COOKIES：类似字典的参数，包含了上传的文件 获取cookie
session：类似字典，表示会话
META：应用反爬虫 REMOTE_ADDR 拉入黑名单
	客户端的的所有信息 ip
	print(request.META)
	for key in request.MATE:
		print(key,request.META.get(key))
	print("Remote IP",request.META.get("REMOTE_ADDR"))
```

### HttpResponse对象

当浏览器访问服务器的时候，服务器响应的数据类型

**分类：HTML响应 JsonResponse（前后端分离）**

#### HTML响应

```
1.基类HttpResponse 不使用模板，直接HttpResponse()
	def hello(request):
		response = HttpResponse()
		response.content = "啦啦啦"
		response.status_code = 404
		response.write("略略略")
		response.flush()
	return response
 方法
	init 初始化内容
	write(xxx) 直接写出文本
	flush() 冲刷缓冲区
	set_cookie(key,value='xxx',max_age=None,exprise=None)
	delete_cookie(key) 删除cookie-上面的是设置
	
2.render转发：方法的返回值类型也是一个HttpResponse

3.HttpResponseRedirect重定向
	HttpResponse的子类，响应重定向：可以实现服务器内部跳转
		return HttpResponseRedirect('/grade/2017')使用的时候推荐使用反向解析
	状态码：302
	简写方式：简写redirect方法的返回值类型就是HttpResponseRedirect
	
	反向解析：
		-页面中的反向解析 url方法
		 url位置参数
			{% url 'namespance:name' value1 value2 %}
		 url关键字参数
		 	{% url 'namespance:name' key1=value1 key2=value2 %}
		 -python代码中的反向解析（一般python代码的反向解析都会结合重定向一起使用）
		  位置参数
		  	reverse('namespance:name',args=(value1,value2...))
		  	reverse('namespance:name',args=[value1,value2...])
		  关键字参数
		  	reverse('namespance:name',kwargs={key1:value1,key2:value2...})
```

#### JsonResponese

```
这个类是HttpResponse的子类，它主要和父类的区别在于：
	-默认Content-Type=application/json
	-第一个参数data应该是一个字典类型，当safe这个参数被设置为False，那data可以填入任何能被转换为JSON格式的对象 [list,tuple,set]
	 默认的safe参数是True，如果你传入的参数不是字典类型，那么它就会抛出TypeError的异常
def get_info(request):
	data={
        "status":200,
        "msg":"ok",
	}
	return JsonResponse(data=data)
```

### HttpResponse子类

```
HttpResponseRedirect -302
HttpResponsePermanentRedirect -301 重定向，永久性
HttpResponseBadRequest -400
HttpResponseNotFound -404
HttpResponseForbidden -403 csrf防跨站攻击
HttpResponseNotAllowed -405
HttpResponseServerError -500
Http404 -Exception -raise主动抛异常出来
```



## 会话技术

> 为什么会有会话技术？
>
> ​	服务器如何识别客户端？Http在Web开发中基本都是短连接，请求生命周期从Request开始，到Response就结束
>
> 会话技术：cookie session token(自定义的session)

### cookie

```
客户端会话技术，数据都存储在客户端，以key-value进行存储，支持过期时间max_age，默认请求会携带本网站的所有cookie，cookie不能跨域名，不能跨浏览器，cookie默认不支持中文base64
	-cookie是服务器创建，保存在浏览器端
	-设置cookie应该是服务器 response
	-获取cookie应该在浏览器 request
	-删除cookie应该在服务器 response
cookie使用：
	-设置cookie：response.set_cookie(key,value)
	-获取cookie：username = request.COOKIES.get("username")
	-删除cookie：response.delete_cookie("content")

可以加盐：加密 获取的时候需要解密
	加密 response.set_signed_cookie('content',uname,"xxxx")
	解密 
		uname = request.COOKIES.get('content') 获取的是加盐之后的数据
		uname = request.get_signed_cookie('content',salt="xxxx")

通过Response将cookie写到浏览器上，下一次访问，浏览器会根据不同的规则携带cookie过来
	max_age：整数，指定cookie过期时间 单位秒
	expries：整数，指定过期时间，还支持一个 datetime 或 timedelta ，可以指定一个具体日期时间
	[max_age和expries两个选一个指定]
	过期时间的几个关键时间
		-max_age 设置为0，浏览器关闭失效；设置为None，永不过期；
		-expries=timedelta(day=10) 10天后过期
```

### session

```
服务端会话技术，数据都存储在服务端，默认存在内存RAM，在django被持久化到了数据库中，该表叫做Django_session，这个表中有三个字段，分别为session_key,session_data,expris_data
Django中session的默认过期时间是14天，支持过期，主键是字符串，默认做了数据安全，使用了BASE64，依赖于cookies
	-使用了base64之后，这个字符串会在最后面添加一个=
	-在前面添加了一个混淆串
session使用：
	设置session  request.session["username"] = username
	获取session  username = request.session.get("usrname")
	使用session退出  del request.session['username'] cookie是脏数据
					response.delete_cookie('sessionid') session是脏数据
					request.session.flush() 冲刷
session常用操作：
	-get(key,default=None) 根据键获取会话的值
	-clear() 清除所有会话
	-flush() 删除当前会话数据并删除会话的cookie
	-delete request['session_id'] 删除会话
	-session.session_key获取session的key
	-request.session['user'] = username
数据存储到数据库中会进行的编码是base64
```

### token

#### 概念

```
Token的中文意思是“令牌”，主要用来身份验证，Facebook，Twitter，Google+，Github等大型网站都在使用。比起传统的身份验证方法，Token有扩展性强，安全性高的特点，非常适合用在web应用或者移动应用上，如果使用在移动端或客户端开发中，通常以Json形式传输，服务端会话技术，自定义的session，给他一个不能重复的字符串，数据存储在服务器中。
```

#### 验证方法

```
使用基于Token的身份验证方法，在服务端不需要存储用户的登记记录。
-客户端使用用户名跟密码请求登录
-服务端收到请求，去验证用户名与密码
-验证成功后，服务端会签发一个Token，再把这个Token发送给客户端
-客户端收到Token以后可以把它存储起来，比如放在 Cookie里或者 Local Storage里
-客户端每次向服务器端请求资源的时候需要带着服务端签发的Token
-服务端收到请求，然后去验证客户端请求里面带着的Token，如果验证成功，就向客户端返回请求的数据
```

#### python常用的Token生成方法

```
1) binascii.b2a_base64(os.urandom(24))[:-1]
	>>> import binascii
	>>> import os
	>>> binascii.b2a_base64(os.urandom(24))[:-1]
		b'J1pJPotQJb6Ld+yBKDq8bqcJ71wXw+Xd'
	总结：这种算法的优点是性能快；缺点是有特殊字符，需要加replace来做处理

2) sha1(os..urandom(24)).hexdigest()
	>>> import hashlib
	>>> import os
	>>> hashlib.sha1(os.urandom(24)).hexdigest()
		'21b7253943332d0237a720701bcb8161b82db776'
	总结：这种算法的优点是安全，不需要做特殊处理；缺点是覆盖范围差一些

3) uuid4().hex
	>>> import uuid
	>>> import os
	>>> uuid.uuid4().hex
		'c58a80d3b7864b0686757b95e9626e47'
	总结：uuid使用起来比较方便；缺点是安全性略差一些
 4) base64.b32encode(os.urandom(20)/base64.b64encode(os.urandom(24))
 	>>> import base64
 	>>> import os
 	>>> base64.b32encode(os.urandom(20))
 		b'NJMTBMOYIXHNRATTOTVONT4BXJAC25TX'
 	>>> base64.b32encode(os.urandom(24))
 		b'l1eU6UzSlWsowm8M8lH5VaFhZEAQ4kQj'
 	总结：可以用base64的地方，选择binascii.b2a_base64是不错的选择
 		根据W3的SessionID的字串中对identifier的定义，SessionID中使用的是base64，但在Cookie的值内使用需要注意“=”这个特殊字符的存在；
		 如果要安全字符（字母数字），SHA1也是一个不错的选择，性能也不错；
```

#### token的应用

```
import hashlib

# 待加密内容
strdata = "xiaojingjiaaseafe16516506ng"

h1 = hashlib.md5()
h1.update(strdata.encode(encoding='utf-8'))

strdata_tomd5 = h1.hexdigest()

print("原始内容：", strdata, ",加密后：", strdata_tomd5)


import time
import base64
import hmac

# 生产token
def generate_token(key, expire=3600):
    r'''''
        @Args:
            key: str (用户给定的key，需要用户保存以便之后验证token,每次产生token时的key 都可以是同一个key)
            expire: int(最大有效时间，单位为s)
        @Return:
            state: str
    '''
    ts_str = str(time.time() + expire)
    ts_byte = ts_str.encode("utf-8")
    sha1_tshexstr = hmac.new(key.encode("utf-8"), ts_byte, 'sha1').hexdigest()
    token = ts_str + ':' + sha1_tshexstr
    b64_token = base64.urlsafe_b64encode(token.encode("utf-8"))
    return b64_token.decode("utf-8")


# 验证token
def certify_token(key, token):
    r'''''
        @Args:
            key: str
            token: str
        @Returns:
            boolean
    '''
    token_str = base64.urlsafe_b64decode(token).decode('utf-8')
    token_list = token_str.split(':')
    if len(token_list) != 2:
        return False
    ts_str = token_list[0]
    if float(ts_str) < time.time():
        # token expired
        return False
    known_sha1_tsstr = token_list[1]
    sha1 = hmac.new(key.encode("utf-8"), ts_str.encode('utf-8'), 'sha1')
    calc_sha1_tsstr = sha1.hexdigest()
    if calc_sha1_tsstr != known_sha1_tsstr:
        # token certification failed
        return False
        # token certification success
    return True


key = "xiaojingjing"
print("key：", key)
user_token = generate_token(key=key)

print("加密后：", user_token)
user_de = certify_token(key=key, token=user_token)
print("验证结果：", user_de)

key = "xiaoqingqing"
user_de = certify_token(key=key, token=user_token)
print("验证结果：", user_de)
```

#### cookie与session的区别

```
1.cookie数据存放在客户端上，session数据放在服务器上
2.cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗  [考虑到安全应当使用session]
3.session会在一定时间内保存在服务器上，当访问增多时，会比较占用服务器的性能  [考虑到减轻服务器性能方面，应当使用COOKIE]
```

#### session与token的区别

```
1.作为身份认证，token安全性比session好，因为每个请求都有签名还能防止监听以及重放攻击
2.session是一种HTTP存储机制，目的是为无状态的HTTP提供的持久机制。session认证只是简单的把User信息存储到session里，因为SID的不可预测性，暂且认为是安全的。这是一种认证手段，但是如果有了某个User的SID，就相当于拥有该User的全部权利 [SID不应该共享给其他网站或第三方]
3.token如果指的是OAuth Token或类似机制的话，提供的是认证和授权，认证是针对用户，授权是针对App。其目的是让某App有权利访问某用户的信息。这里的token是唯一的，不可以转移到其他App上，也不可以转到其他用户上
```

#### csrf豁免

```
csrf 防跨站攻击
实现机制
	页面中存在{% csrf_token %}时
	在渲染时，会向Response中添加csrftoken的Cookie
	在提交时，会被添加到请求体中，会被验证有效性
[例子：https://www.jianshu.com/p/d1407591e8de]

解决csrf的问题/csrf豁免：
	-注释中间件
	-在表单中添加{% csrf_token %}
	-在方法上添加 @csrf_exempt
```



























