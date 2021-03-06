
当今的网站实际上都是富应用程序（rich application）。在本章中，将学习如何使用Django（http://djangoproject.com/ ）来开发一个名为“学习笔记”（Learning Log）的项目，这是一个在线日志系统，能够记录所学习的有关特定主题的知识。

为这个项目制定规范，然后为应用程序使用的数据定义模型。将使用Django的管理系统来输入一些初始数据，再学习编写视图和模板，让Django能够为我们的网站创建网页。

Django是一个Web框架——一套用于帮助开发交互式网站的工具。Django能够响应网页请求，还能帮助更轻松地读写数据库、管理用户等。

## 18.1　建立项目

### 18.1.1　制定规范

完整的规范详细说明了项目的目标，阐述了项目的功能，并讨论了项目的外观和用户界面。与任何良好的项目规划和商业计划书一样，规范应突出重点，帮助避免项目偏离轨道。这里不会制定完整的项目规划，而只列出一些明确的目标，以突出开发的重点。制定的规范如下：

> 编写一个名为“学习笔记”的Web应用程序，让用户能够记录感兴趣的主题，并在学习每个主题的过程中添加日志条目。“学习笔记”的主页对这个网站进行描述，并邀请用户注册或登录。用户登录后，就可创建新主题、添加新条目以及阅读既有的条目。

> 学习新的主题时，记录学到的知识可帮助跟踪和复习这些知识。优秀的应用程序让这个记录过程简单易行。

### 18.1.2　建立虚拟环境

要使用Django，首先需要建立一个虚拟工作环境。`虚拟环境`是系统的一个位置，可以在其中安装包，并将其与其他Python包隔离。将项目的库与其他项目分离是有益的，且为了在第20章将“学习笔记”部署到服务器，这也是必须的。

为项目新建一个目录，将其命名为learning_log，再在终端中切换到这个目录，并创建一个虚拟环境。
```sh
python -m venv ll_env
```

### 18.1.3　安装virtualenv
如果你使用的是较早的Python版本，或者系统没有正确地设置，不能使用模块venv ，可安装virtualenv包。为此，可执行如下命令：`pip3 install --user virtualenv`。

> 如果使用的是Linux系统，且上面的做法不管用，可使用系统的包管理器来安装virtualenv。例如，要在Ubuntu系统中安装virtualenv，可使用命令`sudo apt-get install python-virtualenv`。

在终端中切换到目录learning_log，并像下面这样创建一个虚拟环境：`virtualenv ll_env`。

> 如果系统安装了多个Python版本，需要指定virtualenv使用的版本。例如，命令virtualenv ll_env --python=python3 创建一个使用Python 3的虚拟环境。

### 18.1.4　激活虚拟环境
建立虚拟环境后，需要使用下面的命令激活它：`source ll_env/bin/activate`。
要停止使用虚拟环境，可执行命令deactivate ：`deactivate`。

### 18.1.5　安装Django

创建并激活虚拟环境后，就可安装Django了：`pip3 install Django`。

由于是在虚拟环境中，因此在所有的系统中，安装Django的命令都相同：不需要指定标志--user ，也无需使用python -m pip install package_name 这样较长的命令。

> Django仅在虚拟环境处于活动状态时才可用。

### 18.1.6　在Django中创建项目
在依然处于活动的虚拟环境的情况下，执行如下命令来新建一个项目：`django-admin.py startproject learning_log .`

> 千万别忘了这个句点，否则部署应用程序时将遭遇一些配置问题。如果忘记了这个句点，就将创建的文件和文件夹删除（ll_env除外），再重新运行这个命令。

目录learning_log包含4个文件，其中最重要的是settings.py、urls.py和wsgi.py。文件settings.py指定Django如何与你的系统交互以及如何管理项目。在开发项目的过程中，将修改其中一些设置，并添加一些设置。文件urls.py告诉Django应创建哪些网页来响应浏览器请求。文件wsgi.py帮助Django提供它创建的文件，这个文件名是web server gateway interface（Web服务器网关接口）的首字母缩写。

### 18.1.7　创建数据库
Django将大部分与项目相关的信息都存储在数据库中，因此我们需要创建一个供Django使用的数据库。请在处于活动虚拟环境中的情况下执行下面的命令：`python manage.py migrate`。

修改数据库称为`迁移`数据库。首次执行命令migrate 时，将让Django确保数据库与项目的当前状态匹配。在使用SQLite的新项目中首次执行这个命令时，Django将新建一个数据库。Django将创建必要的数据库表，用于存储这个项目（Synchronize unmigrated apps，同步未迁移的应用程序 ）中使用的信息，再确保数据库结构与当前代码（Apply all migrations，应用所有的迁移）匹配。

> SQLite是一种使用单个文件的数据库，是编写简单应用程序的理想选择，因为它让你不用太关注数据库管理的问题。

### 18.1.8　查看项目
下面来核实Django是否正确地创建了项目。为此，可执行命令runserver：`python3 manage.py runserver`或`python3 manage.py runserver 0.0.0.0:8000`来制定IP地址和端口号。

Django启动一个服务器，能够查看系统中的项目，了解它们的工作情况。在浏览器中输入URL以请求网页时，该Django服务器将进行响应：生成合适的网页，并将其发送给浏览器。

首先Django通过检查确认正确地创建了项目；其次，指出了使用的Django版本以及当前使用的设置文件的名称；然后，指出了项目的URL。URL http://127.0.0.1:8000/ 表明项目将在计算机（即localhost）的端口8000上侦听请求。localhost是一种只处理当前系统发出的请求，而不允许其他任何人查看正在开发的网页的服务器。

现在打开一款Web浏览器，并输入URL：http://localhost:8000/ ；如果这不管用，请输入http://127.0.0.1:8000/ 。将看到一个页面，这个页面是Django创建的，到目前为止一切正常。现在暂时不要关闭这个服务器。若要关闭这个服务器，按Ctrl + C即可。

> 如需远程访问：除上面增加IP和端口的参数外，需要在`settings.py`配置文件中增加`ALLOWED_HOSTS = ['IP']`的参数。

### **练习：**

18-1 新项目 ：为更深入地了解Django做了些什么，可创建两个空项目，看看Django创建了什么。新建一个文件夹，并给它指定简单的名称，如InstaBook或FaceGram（不要在目录learning_log中新建该文件夹），在终端中切换到该文件夹，并创建一个虚拟环境。在这个虚拟环境中安装Django，并执行命令django-admin.py startproject instabook . （千万不要忘了这个命令末尾的句点）。

看看这个命令创建了哪些文件和文件夹，并与项目“学习笔记”包含的文件和文件夹进行比较。这样多做几次，直到对Django新建项目时创建的东西了如指掌。然后，将项目目录删除。

## 18.2　创建应用程序

Django项目由一系列应用程序组成，它们协同工作，让项目成为一个整体。本章只创建一个应用程序，它将完成项目的大部分工作。

当前，在前面打开的终端窗口中应该还运行着runserver 。请再打开一个终端窗口，并切换到manage.py所在的目录。激活该虚拟环境，再执行命令startapp ：
```sh
source ll_env/bin/activate
python3 manage.py startapp learning_logs
```

### 18.2.1　定义模型
打开文件models.py

```python
from django.db import models

# Create your models here.

class Topic(models.Model):
    """用户学习的主题"""
    text = models.CharField(max_length=200)
    date_added = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        """返回模型的字符串表示"""
        return self.text
```

创建了一个名为Topic 的类，它继承了Model——Django中一个定义了模型基本功能的类。Topic类只有两个属性：text和date_added 。

属性text是一个`CharField`——由字符或文本组成的数据。需要存储少量的文本，如名称、标题或城市时，可使用CharField 。定义CharField 属性时，必须告诉Django该在数据库中预留多少空间。在这里，将max_length设置成了200（即200个字符），这对存储大多数主题名来说足够了。

属性date_added是一个`DateTimeField`——记录日期和时间的数据。传递了实参auto_add_now=True，每当用户创建新主题时，这都让Django将这个属性自动设置成当前日期和时间。

> 要获悉可在模型中使用的各种字段，请参阅Django Model Field Reference（Django模型字段参考），其网址为https://docs.djangoproject.com/en/1.8/ref/models/fields/ 。

需要告诉Django，默认应使用哪个属性来显示有关主题的信息。Django调用方法`__str__()`来显示模型的简单表示。编写了方法`__str__()`，返回存储在属性text中的字符串。

> 如果使用的是Python 2.7，应调用方法`__unicode__()`，而不是`__str__()`，但其中的代码相同。

### 18.2.2　激活模型
要使用模型，必须让Django将应用程序包含到项目中。为此，打开settings.py（它位于目录learning_log/learning_log中），将看到一个这样的片段，即告诉Django哪些应用程序安装在项目中：

这是一个`列表`(原书中说是元组)，告诉Django项目是由哪些应用程序组成的。请将INSTALLED_APPS修改成下面这样，将前面的应用程序添加到这个元组中：


```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # My APPs
    'learning_logs',
]
```

通过将应用程序编组，在项目不断增大，包含更多的应用程序时，有助于对应用程序进行跟踪。这里新建了一个名为My apps的片段，当前它只包含应用程序learning_logs。

接下来，需要让Django修改数据库，使其能够存储与模型Topic 相关的信息。为此，在终端窗口中执行下面的命令：
```sh
python manage.py makemigrations learning_logs
```

命令makemigrations让Django确定该如何修改数据库，使其能够存储与定义的新模型相关联的数据。输出表明Django创建了一个名为0001_initial.py的迁移文件，这个文件将在数据库中为模型Topic创建一个表。

下面来应用这种迁移，让Django替我们修改数据库：
```sh
python manage.py migrate
```

这个命令的大部分输出都与我们首次执行命令migrate的输出相同。我们需要检查的是输出行
```
Running migrations:
  Applying learning_logs.0001_initial... OK
```
在这里，Django确认为learning_logs应用迁移时一切正常（OK）。

每当需要修改“学习笔记”管理的数据时，都采取如下三个步骤：修改models.py；对learning_logs 调用makemigrations ；让Django迁移项目。

### 18.2.3　Django管理网站
为应用程序定义模型时，Django提供的管理网站（admin site）能够轻松地处理模型。网站的管理员可使用管理网站，但普通用户不能使用。在本节中，建立管理网站，并通过它使用模型Topic来添加一些主题。

#### 18.2.3.1 创建超级用户

Django允许创建具备所有权限的用户——`超级用户`。权限决定了用户可执行的操作。最严格的权限设置只允许用户阅读网站的公开信息；注册了的用户通常可阅读自己的私有数据，还可查看一些只有会员才能查看的信息。为有效地管理Web应用程序，网站所有者通常需要访问网站存储的所有信息。

为在Django中创建超级用户，请执行下面的命令并按提示做：
```sh
python3 manage.py createsuperuser
```

#### 18.2.3.2 向管理网站注册模型

Django自动在管理网站中添加了一些模型，如User和Group，但对于创建的模型，必须手工进行注册。

创建应用程序learning_logs时，Django在models.py所在的目录中创建了一个名为admin.py的文件，修改导入要注册的模型Topic，再使用admin.site.register()让Django通过管理网站管理模型。

```python
from django.contrib import admin

# Register your models here.
from learning_logs.models import Topic

admin.site.register(Topic)
```

现在，使用超级用户账户访问管理网站：访问http://IP:8000/admin/ ，并输入刚创建的超级用户的用户名和密码，访问的网页能够添加和修改用户和用户组，还可以管理与刚才定义的模型Topic相关的数据。

#### 18.2.3.3 添加主题

向管理网站注册Topic后，添加第一个主题。为此，单击Topics进入主题网页，它几乎是空的。单击Add，将看到一个用于添加新主题的表单。在第一个方框中输入Chess，再单击Save，这将返回到主题管理页面，其中包含刚创建的主题。

下面再创建一个主题，以便有更多的数据可供使用。再次单击Add，并创建另一个主题Rock Climbing。当单击Save时，将重新回到主题管理页面，其中包含主题Chess和Rock Climbing。

### 18.2.4　定义模型Entry
要记录学到的国际象棋和攀岩知识，需要为用户可在学习笔记中添加的条目定义模型。每个条目都与特定主题相关联，这种关系被称为多对一关系，即多个条目可关联到同一个主题。修改`models.py`。

```python
class Entry(models.Model):
    """学到的有关某个主题的具体知识"""
    topic = models.ForeignKey(Topic, on_delete=models.CASCADE)
    text = models.TextField()
    date_added = models.DateTimeField(auto_now_add=True)

    class Meta:
        verbose_name_plural = 'entries'

    def __str__(self):
        """返回模型的字符串表示"""
        return "{}...".format(self.text[:50])
```

像Topic一样，Entry 也继承了Django基类Model。第一个属性topic是一个ForeignKey 实例。外键是一个数据库术语，它引用了数据库中的另一条记录；这些代码将每个条目关联到特定的主题。每个主题创建时，都给它分配了一个键（或ID）。需要在两项数据之间建立联系时，Django使用与每项信息相关联的键。

接下来是属性text，它是一个TextField 实例。这种字段不需要长度限制。属性date_added能够按创建顺序呈现条目，并在每个条目旁边放置时间戳。

在Entry 类中嵌套了Meta类。Meta存储用于管理模型的额外信息，在这里，它让我们能够设置一个特殊属性，让Django在需要时使用Entries来表示多个条目。如果没有这个类， Django将使用Entrys来表示多个条目。最后，方法`__str__()`告诉Django，呈现条目时应显示哪些信息。由于条目包含的文本可能很长，Django只显示text的前50个字符，还添加了一个省略号，指出显示的并非整个条目。

### 18.2.5　迁移模型Entry
由于添加了一个新模型，因此需要再次迁移数据库。

```sh
python3 manage.py makemigrations learning_logs
python3 manage.py migrate
```

生成了一个新的迁移文件——0002_entry.py，告诉Django如何修改数据库，使其能够存储与模型Entry 相关的信息。执行命令migrate，Django应用了这种迁移且一切顺利。

### 18.2.6　向管理网站注册Entry
还需要注册模型Entry 。为此，需要修改admin.py。

```python
from django.contrib import admin

# Register your models here.
from learning_logs.models import Topic, Entry

admin.site.register(Topic)
admin.site.register(Entry)
```

返回到http://IP/admin/ ，将看到learning_logs下列出了Entries。单击Entries的Add链接，或者单击Entries再选择Add entry。将看到一个下拉列表，能够选择要为哪个主题创建条目，还有一个用于输入条目的文本框。从下拉列表中选择Chess，并添加一个条目。

单击Save时，将返回到主条目管理页面。在这里，使用text[:50] 作为条目的字符串表示的好处得以展示：管理界面中，只显示了条目的开头部分而不是其所有文本，这使得管理多个条目容易得多。

再来创建一个国际象棋条目，并创建一个攀岩条目，以提供一些初始数据。

### 18.2.7　Django shell
输入一些数据后，就可通过交互式终端会话以编程方式查看这些数据了。这种交互式环境称为Django shell，用以测试项目和排除其故障。
```sh
python manage.py shell
```

```python
from learning_logs.models import Topic
Topic.objects.all()
topics = Topic.objects.all()
for topic in topics:
    print(topic.id, topic)
```

将返回的查询集存储在topics 中，然后打印每个主题的id属性和字符串表示。从输出可知，主题Chess的ID为1，而Rock Climbing的ID为2。

知道对象的ID后，就可获取该对象并查看其任何属性。

```python
t = Topic.objects.get(id=1)
t.text
t.date_added
```

还可以查看与主题相关联的条目。前面给模型Entry定义了属性topic ，这是一个ForeignKey ，将条目与主题关联起来。利用这种关联，Django能够获取与特定主题相关联的所有条目。

```python
t.entry_set.all()
```

为通过外键关系获取数据，可使用相关模型的小写名称、下划线和单词set。

编写用户可请求的网页时，将使用这种语法。确认代码能获取所需的数据时，shell很有帮助。如果代码在shell中的行为符合预期，那么它们在项目文件中也能正确地工作。如果代码引发了错误或获取的数据不符合预期，那么在简单的shell环境中排除故障要比在生成网页的文件中排除故障容易得多。虽然不会太多地使用shell，但应继续使用它来熟悉对存储在项目中的数据进行访问的Django语法。

> 每次修改模型后，都需要重启shell，这样才能看到修改的效果。要退出shell会话，可按Ctr + D；如果你使用的是Windows系统，应按Ctr + Z，再按回车键。

### **练习：**
18-2 简短的条目 ：当前，Django在管理网站或shell中显示Entry 实例时，模型Entry 的方法`__str__()`都在它的末尾加上省略号。请在方法`__str__()`中添加一条if 语句，以便仅在条目长度超过50字符时才添加省略号。使用管理网站来添加一个长度少于50字符的条目，并核实显示它时没有省略号。

18-3 Django API ：编写访问项目中的数据的代码时，你编写的是查询。请浏览有关如何查询数据的文档，其网址为https://docs.djangoproject.com/zh-hans/3.0/topics/db/queries/ 。其中大部分内容都是你不熟悉的，但等你自己开发项目时，这些内容会很有用。

18-4 比萨店 ：新建一个名为pizzeria 的项目，并在其中添加一个名为pizzas 的应用程序。定义一个名为Pizza 的模型，它包含字段name ，用于存储比萨名称，如Hawaiian和Meat Lovers。定义一个名为Topping 的模型，它包含字段pizza 和name ，其中字段pizza 是一个关联到Pizza 的外键，而字段name 用于存储配料，如pineapple 、Canadian bacon 和sausage 。

向管理网站注册这两个模型，并使用管理网站输入一些比萨名和配料。使用shell来查看你输入的数据。

## 18.3　创建网页：学习笔记主页

使用Django创建网页的过程通常分三个阶段：定义URL、编写视图和编写模板。

首先，必须定义URL模式。`URL模式`描述了URL是如何设计的，让Django知道如何将浏览器请求与网站URL匹配，以确定返回哪个网页。

每个URL都被映射到特定的视图——`视图函数`获取并处理网页所需的数据。视图函数通常调用一个模板，后者生成浏览器能够理解的网页。为明白其中的工作原理，创建学习笔记的主页。定义该主页的URL、编写其视图函数并创建一个简单的模板。

### 18.3.1　映射URL

用户通过在浏览器中输入URL以及单击链接来请求网页，因此需要确定项目需要哪些URL。主页的URL最重要，是用户用来访问项目的基础URL。当前，基础URL（http://IP:8000/） 返回默认的Django网站。修改这一点，将这个基础URL映射到“学习笔记”的主页。

打开项目主文件夹learning_log中的文件urls.py

```python
from django.contrib import admin
from django.urls import path, include
#from django.conf.urls import include, url

urlpatterns = [
    path('admin/', admin.site.urls),
    path(r'', include('learning_logs.urls', namespace='learning_logs')),
    ]
```

添加了一行代码来包含模块learning_logs.urls 。这行代码包含实参namespace ，能够将learning_logs的URL同项目中的其他URL区分开来，这在项目开始扩展时很有帮助。

默认的urls.py包含在文件夹learning_log中，现在需要在文件夹learning_logs中创建另一个urls.py文件

```python
"""定义learning_logs的URL模式"""

from django.urls import path

from . import views

urlpatterns = [
    # 主页
    path(r'', views.index, name='index'),
    ]
```

为弄清楚当前位于哪个urls.py文件中，在这个文件开头添加了一个文档字符串。接下来导入函数url ，因为需要使用它来将URL映射到视图。还导入了模块views，其中的句点让Python从当前的urls.py模块所在的文件夹中导入视图。在这个模块中，变量urlpatterns 是一个列表，包含可在应用程序learning_logs中请求的网页。

实际的URL模式是一个对函数url()的调用，这个函数接受三个实参。第一个是一个正则表达式。Django在urlpatterns中查找与请求的URL字符串匹配的正则表达式，因此正则表达式定义了Django可查找的模式。

> 目前版本的Django使用path函数，不使用url函数，不使用正则表达式来描述路径。

> 正则表达式`r'^$'` ：其中的`r`让Python将接下来的字符串视为`原始字符串`，而引号告诉Python正则表达式始于和终于何处。脱字符（^ ）让Python查看字符串的开头，而美元符号让Python查看字符串的末尾。这个正则表达式让Python查找开头和末尾之间没有任何东西的URL。Python忽略项目的基础URL（http://localhost:8000/） ，因此这个正则表达式与基础URL匹配。其他URL都与这个正则表达式不匹配。如果请求的URL不与任何URL模式匹配，Django将返回一个错误页面。

url()的第二个实参指定了要调用的视图函数。请求的URL与前述正则表达式匹配时，Django将调用views.index。第三个实参将这个URL模式的名称指定为index，能够在代码的其他地方引用它。每当需要提供到这个主页的链接时，都将使用这个名称，而不编写URL。

> 正则表达式通常被称为regex，几乎每种编程语言都使用它。它们的用途多得难以置信，但需要经过一定的练习才能熟悉。

### 18.3.2　编写视图

视图函数接受请求中的信息，准备好生成网页所需的数据，再将这些数据发送给浏览器——这通常是使用定义了网页是什么样的模板实现的。

learning_logs中的文件views.py是执行命令python manage.py startapp 时自动生成的。修改之。

```python
from django.shortcuts import render

# Create your views here.
def index(request):
    """学习笔记的主页"""
    return render(request, 'learning_logs/index.html')
```

URL请求与刚才定义的模式匹配时，Django将在文件views.py中查找函数index()，再将请求对象传递给这个视图函数。在这里，不需要处理任何数据，因此这个函数只包含调用render()的代码。这里向函数render()提供了两个实参：原始请求对象以及一个可用于创建网页的模板。下面来编写这个模板。

### 18.3.3　编写模板
模板定义了网页的结构。模板指定了网页是什么样的，而每当网页被请求时，Django将填入相关的数据。模板提供能够访问视图提供的任何数据的能力。主页视图没有提供任何数据，因此相应的模板非常简单。

在文件夹learning_logs中新建一个文件夹，并将其命名为templates。在文件夹templates中，再新建一个文件夹，并将其命名为learning_logs。这好像有点多余（在文件夹learning_logs中创建了文件夹templates，又在这个文件夹中创建了文件夹learning_logs），但建立了Django能够明确解读的结构，即便项目很大，包含很多应用程序亦如此。在最里面的文件夹learning_logs中，新建一个文件，并将其命名为index.html，再在这个文件中编写如下代码：

```html
<p>Learning Log</p>

<p>Learning Log helps you keep track of your learning, for any topic you're
learning about.</p>
```

这个文件非常简单。标签`<p></p>`标识段落；标签`<p>`指出了段落的开头位置，而标签`</p>`指出了段落的结束位置。这里定义了两个段落：第一个充当标题，第二个阐述了用户可使用“学习笔记”来做什么。

现在，如果请求这个项目的基础URL——http://IP:8000/ ，将看到刚才创建的网页，而不是默认的Django网页。Django接受请求的URL，发现该URL与模式`r'^$'` 匹配，因此调用函数views.index() ，这将使用index.html包含的模板来渲染网页。

创建网页的过程看起来可能很复杂，但将URL、视图和模板分离的效果实际上很好。这让我们能够分别考虑项目的不同方面，且在项目很大时，让各个参与者可专注于其最擅长的方面。

### **练习：**
18-5 膳食规划程序 ：假设你要创建一个应用程序，帮助用户规划一周的膳食。为此，新建一个文件夹，并将其命名为meal_planner，再在这个文件夹中新建一个Django项目。接下来，新建一个名为meal_plans 的应用程序，并为这个项目创建一个简单的主页。

18-6 比萨店主页 ：在你为完成练习18-4而创建的项目Pizzeria中，添加一个主页。

## 18.4　创建其他网页

制定创建网页的流程后，可以开始扩充“学习笔记”项目了。将创建两个显示数据的网页，其中一个列出所有的主题，另一个显示特定主题的所有条目。对于每个网页，都将指定URL模式，编写一个视图函数，并编写一个模板。但这样做之前，先创建一个父模板，项目中的其他模板都将继承它。

### 18.4.1　模板继承
创建网站时，几乎都有一些所有网页都将包含的元素。在这种情况下，可编写一个包含通用元素的父模板，并让每个网页都继承这个模板，而不必在每个网页中重复定义这些通用元素。这种方法能专注于开发每个网页的独特方面，还能让修改项目的整体外观容易得多。

#### 18.4.1.1 父模板
首先创建一个名为base.html的模板，并将其存储在index.html所在的目录中。这个文件包含所有页面都有的元素；其他的模板都继承base.html。当前，所有页面都包含的元素只有顶端的标题。将在每个页面中包含这个模板，因此将这个标题设置为到主页的链接。

```html
<p>
    <a href="{% url 'learning_logs:index' %}">Learning Log</a>
</p>
    
{% block content %}{% endblock content %}
```

这个文件的第一部分创建一个包含项目名的段落，该段落也是一个到主页的链接。为创建链接，使用了一个模板标签 ，它是用大括号和百分号（`{% %}`）表示的。模板标签是一小段代码，生成要在网页中显示的信息。在这个实例中，模板标签`{% url 'learning_logs:index' %}`生成一个URL，该URL与learning_logs/urls.py中定义的名为index的URL模式匹配。在这个示例中，learning_logs 是一个命名空间，而index是该命名空间中一个名称独特的URL模式。

在简单的HTML页面中，链接是使用`锚`标签定义的：`<a href="link_url">link text</a>`。

让模板标签来生成URL，可让链接保持最新容易得多。要修改项目中的URL，只需修改urls.py中的URL模式，这样网页被请求时，Django将自动插入修改后的URL。在这个项目中，每个网页都将继承base.html，因此从现在开始，每个网页都包含到主页的链接。

插入了一对块标签。这个块名为content ，是一个占位符，其中包含的信息将由子模板指定。

子模板**并非必须**定义父模板中的每个块，因此在父模板中，可使用任意多个块来预留空间，而子模板可根据需要定义相应数量的块。

> 在Python代码中，几乎总是缩进四个空格。相比于Python文件，模板文件的缩进层级更多，因此每个层级通常只缩进两个空格。

#### 18.4.1.2 子模板
重新编写index.html，使其继承base.html

```html
{% extends "learning_logs/base.html" %}

{% block content %}
    <p>index.html: Learning Log helps you keep track of your learning, for any topic you're learning about.</p>
{% endblock content %}
```

标题Learning Log替换成了从父模板那里继承的代码。**子模板的第一行必须包含标签{% extends %} **，让Django知道它继承了哪个父模板。文件base.html位于文件夹learning_logs中，因此父模板路径中包含learning_logs。这行代码导入模板base.html的所有内容，让index.html能够指定要在content 块预留的空间中添加的内容。

接着插入了一个名为content 的{% block %} 标签，以定义content块。不是从父模板继承的内容都包含在content块中，在这里是一个描述项目“学习笔记”的段落。使用标签{% endblock content %} 指出了内容定义的结束位置。

模板继承的优点开始显现出来了：在子模板中，只需包含当前网页特有的内容。这不仅简化了每个模板，还使得网站修改起来容易得多。要修改很多网页都包含的元素，只需在父模板中修改该元素，所做的修改将传导到继承该父模板的每个页面。在包含数十乃至数百个网页的项目中，这种结构使得网站改进起来容易而且快捷得多。

> 在大型项目中，通常有一个用于整个网站的父模板——base.html，且网站的每个主要部分都有一个父模板。每个部分的父模板都继承base.html，而网站的每个网页都继承相应部分的父模板。这样可以轻松地修改整个网站的外观、网站任何一部分的外观以及任何一个网页的外观。这种配置提供了一种效率极高的工作方式。

### 18.4.2　显示所有主题的页面
有了高效的网页创建方法，就能专注于另外两个网页了：显示全部主题的网页以及显示特定主题中条目的网页。所有主题页面显示用户创建的所有主题，是第一个需要使用数据的网页。

#### 18.4.2.1 URL模式
首先，定义显示所有主题的页面的URL。通常，使用一个简单的URL片段来指出网页显示的信息；使用单词topics，因此URL http://IP:8000/topics/ 将返回显示所有主题的页面。下面修改learning_logs/urls.py。

```python
urlpatterns = [
    # 主页
    path(r'', views.index, name='index'),
    # 显示所有的主题
    path(r'topic/', views.topics, name='topics'), 
    ]
```

在用于主页URL的正则表达式中添加了`topics/`。Django检查请求的URL时，这个模式与这样的URL匹配：基础URL后面跟着topics 。可以在末尾包含斜杠，也可以省略它，但单词topics 后面不能有任何东西，否则就与该模式不匹配。其URL与该模式匹配的请求都将交给views.py中的函数topics() 进行处理。

#### 18.4.2.2 视图
函数topics() 需要从数据库中获取一些数据，并将其发送给模板。需要在views.py中添加的代码。

```python
from .models import Topic

def topics(request):
    """显示所有的主题"""
    topics = Topic.objects.order_by('date_added')
    context = {'topics': topics}
    return render(request, 'learning_logs/topics.html', context)
```

首先导入了与所需数据相关联的模型。函数topics() 包含一个形参：Django从服务器那里收到的request 对象。查询数据库——请求提供Topic对象，并按属性date_added对它们进行排序。将返回的查询集存储在topics中。

接下来，定义了一个将要发送给模板的上下文。上下文是一个字典，其中的键是将在模板中用来访问数据的名称，而值是要发送给模板的数据。在这里，只有一个键—值对，它包含将在网页中显示的一组主题。创建使用数据的网页时，除对象request和模板的路径外，还将变量context 传递给render()。

#### 18.4.2.3 模板

显示所有主题的页面的模板接受字典context ，以便能够使用topics() 提供的数据。创建一个文件，将其命名为topics.html，并存储到index.html所在的目录中。

```html
{% extends "learning_logs/base.html" %}

{% block content %}

    <p>Topics</p>
    
    <ul>
        {% for topic in topics %}
        <li>{{ topic }}</li>
        {% empty %}
        <li>No topics have been added yet.</li>
        {% endfor %}
    </ul>
    
{% endblock content %}
```

就像模板index.html一样，首先使用标签`{% extends %}`来继承base.html，再开始定义content块。这个网页的主体是一个项目列表，其中列出了用户输入的主题。在标准HTML中，项目列表被称为无序列表，用标签`<ul> </ul>` 表示。

接着使用了一个相当于for 循环的模板标签，它遍历字典context中的列表topics。模板中使用的代码与Python代码存在一些重要差别：Python使用缩进来指出哪些代码行是for循环的组成部分，而在模板中，每个for循环都必须使用`{% endfor %}`标签来显式地指出其结束位置。因此在模板中，循环类似于下面这样：
```
{% for item in list %}
    do something with each item
{% endfor %}
```
在循环中，要将每个主题转换为一个项目列表项。要在模板中打印变量，需要将变量名用双花括号括起来。每次循环时，代码`{{ topic }}`都被替换为topic的当前值。这些花括号不会出现在网页中，它们只是用于告诉Django使用了一个模板变量。HTML标签`<li> </li>` 表示一个项目列表项，在标签对`<ul> </ul>` 内部，位于标签`<li> </li>`之间的内容都是一个项目列表项。

接着使用了模板标签`{% empty %}`，告诉Django在列表topics为空时该怎么办：这里是打印一条消息，告诉用户还没有添加任何主题。最后两行分别结束for循环和项目列表。

现在需要修改父模板，使其包含到显示所有主题的页面的链接。

```base.html
<p>
    base.html: 
    <a href="{% url 'learning_logs:index' %}">Learning Log</a>
    <a href="{% url 'learning_logs:topics' %}">Topics</a>
</p>
```

在到主页的链接后面添加了一个连字符，然后添加了一个到显示所有主题的页面的链接——使用的也是模板标签url。这一行让Django生成一个链接，它与`learning_logs/urls.py`中名为topics的URL模式匹配。

现在刷新浏览器中的主页，将看到链接Topics。

### 18.4.3　显示特定主题的页面
接下来，创建一个专注于特定主题的页面——显示该主题的名称及该主题的所有条目。同样，定义一个新的URL模式，编写一个视图并创建一个模板。还将修改显示所有主题的网页，让每个项目列表项都是一个链接，单击它将显示相应主题的所有条目。

#### 18.4.3.1 URL模式

显示特定主题的页面的URL模式与前面的所有URL模式都稍有不同，因为它将使用主题的id属性来指出请求的是哪个主题。例如，如果用户要查看主题Chess（其id为1）的详细页面，URL将为http://IP:8000/topics/1/ 。下面是与这个URL匹配的模式，它包含在learning_logs/urls.py中。

```python
urlpatterns = [
    # 主页
    path(r'', views.index, name='index'),
    # 显示所有的主题
    url(r'^topics/(?P<topic_id>\d+)/$', views.topics, name='topics'), 
    ]
```

这个URL模式中的正则表达式——`r'^topics/(?P<topic_id>\d+)/$'` 。`r`让Django将这个字符串视为原始字符串，并指出正则表达式包含在引号内。这个表达式的第二部分（`/(?P<topic_id>\d+)/` ）与包含在两个斜杠内的整数匹配，并将这个整数存储在一个名为topic_id的实参中。这部分表达式两边的括号捕获URL中的值；`?P<topic_id>`将匹配的值存储到topic_id 中；而表达式`\d+`与包含在两个斜杆内的任何数字都匹配，不管这个数字为多少位。

发现URL与这个模式匹配时，Django将调用视图函数topic() ，并将存储在topic_id 中的值作为实参传递给它。在这个函数中，将使用topic_id 的值来获取相应的主题。

#### 18.4.3.2 视图

函数topic() 需要从数据库中获取指定的主题以及与之相关联的所有条目。

```python
def topic(request, topic_id):
    """显示单个主题及其所有的条目"""
    topic = Topic.objects.get(id=topic_id)
    entries = topic.entry_set.order_by('-date_added')
    context = {'topic': topic, 'entries': entries}
    return render(request, 'learning_logs/topic.html', context)
```

这是第一个除request对象外还包含另一个形参的视图函数。这个函数接受正则表达式`(?P<topic_id>\d+)`捕获的值，并将其存储到topic_id 中。使用get()来获取指定的主题，就像前面在Django shell中所做的那样。获取与该主题相关联的条目，并将它们按date_added 排序：date_added前面的减号指定按`降序排列`，即先显示最近的条目。将主题和条目都存储在字典context 中，再将这个字典发送给模板topic.html。

> `Topic.objects.get(id=topic_id)`和`topic.entry_set.order_by('-date_added')`代码被称为查询，向数据库查询特定的信息。在项目中编写这样的查询时，先在Django shell中进行尝试大有裨益。相比于编写视图和模板，再在浏览器中检查结果，在shell中执行代码可更快地获得反馈。

#### 18.4.3.3 模板

这个模板需要显示主题的名称和条目的内容；如果当前主题不包含任何条目，还需向用户指出这一点。

```html
{% extends 'learning_logs/base.html' %}

{% block content %}
    <p>Topic: {{ topic }}</p>
    
    <p>Entries:</p>
    <ul>
    {% for entry in entries %}
        <li>
            <p>{{ entry.date_added|date:'M d, Y H:i' }}</p>
            <p>{{ entry.text|linebreaks }}</p>
        </li>
    {% empty %}
        <li>
            There are no entries for this topic yet.
        </li>
    {% endfor %}
    </ul>

{% endblock content %}
```

像这个项目的其他页面一样，这里也继承了base.html。接下来，显示当前的主题，它存储在模板变量{{ topic }} 中。为什么可以使用变量topic呢？因为它包含在字典context中。接下来，开始定义一个显示每个条目的项目列表，并像前面显示所有主题一样遍历条目。

每个项目列表项都将列出两项信息：条目的时间戳和完整的文本。为列出时间戳，显示属性date_added的值。在Django模板中，竖线（`|` ）表示`模板过滤器`——对模板变量的值进行修改的函数。过滤器`date: 'M d, Y H:i'`以这样的格式显示时间戳：January 1, 2015 23:00。接下来的一行显示text的完整值，而不仅仅是entry的前50个字符。过滤器linebreaks将包含换行符的长条目转换为浏览器能够理解的格式，以免显示为一个不间断的文本块。最后使用模板标签`{% empty %}`打印一条消息，告诉用户当前主题还没有条目。

#### 18.4.3.4 将显示所有主题的页面中的每个主题都设置为链接
在浏览器中查看显示特定主题的页面前，需要修改模板topics.html，让每个主题都链接到相应的网页。

```html
{% extends 'learning_logs/base.html' %}

{% block content %}
topic.html:
    <p>Topic: {{ topic }}</p>
    
    <p>Entries:</p>
    <ul>
    {% for entry in entries %}
        <li>
            <p>{{ entry.date_added|date:'M d, Y H:i' }}</p>
            <p>{{ entry.text|linebreaks }}</p>
        </li>
    {% empty %}
        <li>
            There are no entries for this topic yet.
        </li>
    {% endfor %}
    </ul>

{% endblock content %}
```

### **练习：**
18-7 模板文档 ：请浏览Django模板文档，其网址为https://docs.djangoproject.com/zh-hans/3.0/ref/templates/ 。自己开发项目时，可再回过头来参考该文档。

18-8 比萨店页面 ：在练习18-6中开发的项目Pizzeria中添加一个页面，它显示供应的比萨的名称。然后，将每个比萨名称都设置成一个链接，单击这种链接将显示一个页面，其中列出了相应比萨的配料。请务必使用模板继承来高效地创建页面。

----
![欢迎关注我的微信公众号](https://blog.flashield.com/images/WeChat_QRCode_200.png)
欢迎关注我的微信公众号一起交流！