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











