---
layout: post
title: 使用django框架搭建投票系统
description: 使用django框架搭建投票系统
category: 日记1
tags: pyhon django 
---


#### 利用 django 搭建投票系统（ django-1.8.18 python-2.7

## 创建 django 项目

# 1 安装 django package
# 2 创建 django 目录
```
sudo makedir my-site
suco chmod -R 777 my-site
```
# 3 生成 django 项目框架
django 项目开发有一套自己的命令，可以完成项目创建，生成模型，同步数据库,创建应用等等，可以使用
```
django-help
```
列出所有支持的命令，创建项目对应的命令是
```
django-admin startproject my-site
```
# 4 检查 django 项目是否创建成功
```
# 执行 python manage.py runserver 运行项目
python manage.py runserver
```
访问页面，效果如下

![django 项目成功启动页](/assets/img/djangosuccess.png)
# 5 观察项目接口和入口程序
django 的项目文件结构如下图所示
![django 项目结构](/assets/img/tree.jpg)
其中 mysite 是 package 名，你可以使用任何你想得到的合法的 package name 作为名称

* manage.py 是项目的入口文件
* settings.py 是项目的配置文件
* urls.py 是项目的额路由影射文件
* wsgi.py 是 django 自带的 wsgi 服务器部署文件

## 创建应用 polls 并整合到项目文件中
# 1 创建 app
django 的项目创建是有一个个 app 组合完成的，每一个 app 有着自己独特的功能，众多的 app 组合起来形成完成的项目， django 创建 app 的命令是

```
django-admin start polls
```

poll的文件结构如下
![polls 文件目录](/assets/img/pollsstructure.png)

* admin.py model注册文件，供后台管理模型所用
* model.py 模型文件
* view.py 视图文件
* test.py 测试文件

＃ 2 注册路由
项目从 manage.py 进入，加载配置，影射路由，如果新app中有视图，需要配置 mysite/urls.py 文件，加载路由影射文件，步骤如下

- 创建 app 路由影射文件, 在 app(polls) 目录下创建 urls.py 文件

```
# polls/urls.py

from django.conf.urls import include,url
from . import view

urlpatterns = [
    url(r'^$', views.IndexView, name='index'),
]
```

url 方法接受3个参数 url, method, name,name 在后面反向构造url的时候会用到

- 注册 polls 路由
在 mysite／urls.py 中注册 polls 的路由
```
 url(r'^polls/', include('polls.urls', namespace='polls')),
```
namespace 的作用是避免不同app中相同的viewname造成冲突，通过 namespace ,在调用
url namespace:url-name 即可

- 创建 method ,注册完毕之后，调用 ／polls 即可调用 app 中的视图，我们还缺少 control 方法没有编辑
```
from django.Http import HttpResponse,HttpResponseRedirect,Http404
# polls/views.py
def Index():
    return HttpResponse("helloworld");
```

- 运行项目， 检查 ／polls 执行
![app view](/assets/img/appview.jpg)

## 创建模型文件，熟悉 CRUD 操作
django 的数据迁移工作十分的流畅，dev 定义好数据库的 table schema, 生成 sql 文件，执行 同步文件即可同步数据库文件

- 定义模型文件

```
# polls/models
def Question(models.Model):
    question_text = models.CharField(max_length = 200)
    pub_date = models.DateTimeField('published date')

def Choice(model.Model):
    question = models.ForeignKey(Question)
    votes = models.IntegerField(default=0)
    choice_text = models.CharField(max_length=200)
```


- 在 mysite／settings.py  installedapp中加入 polls

- 扫描模型change

```
# 运行 python manage.py makemigrations 会扫描 mysite 项目下所有 installed app中的model changes
python manage.py makemigrations
```

- 将 migrations 转化为 sql 文件
```
python manage.py sqlmigrate poll 001
```

- 同步数据库 

```
python manage.py migrate 
```

- 进入 DBMS 中检查数据库是否创建成功，django的默认数据库是 sqlite3

```
sqlite3 db.sqlite3
# 列出所有的数据库
.tables
# 列出table schema
.schema polls_choice
```

- 进入 shell 后台，创建 record

```
python manage.py shell
from django.utils import timezone
from polls.models import Question,Vote
# create operation
a = Question(question_text="what is up", pub_date=timezone.now())
a.save()

# query operation
question.objects.all()

# update operation
a = Question.objects.get(pk=1)
a.question_text = "what is the matter"
a.save()

# delete 
a = Question.objects.get(pk=1)
a.delete()

```

## 视图,传递参数，模版引擎 （csrf url-reverse redirect get-post variable function for loop layout)

接下来就是
* layout
* template(varialbe function control statement )
* csrf
* sql-injection
* post get url-reverse redirect
* static files
* exception handling
* session cookie cache


我们会根据创建的数据库 question vote 来创建几个文件
* list 列出所有问题
* detail 问题详情页
* vote 投票页
* result 投票结果页
会涉及到 csrf get post template url reverse exception-handling

# 1 创建页面
```
# polls.urls 
url(r'^(P<question_id>[0-9]+)\$', views.Detail, name='detail'),
url(r'^(P<question_id>[0-9]+)\result$', views.Result, name='result'),
url(r'^(P<question_id>[0-9]+)\vote$', views.Vote, name='vote'),
```

# 2 创建方法
```
from polls.models import Question,Vote
from django.template import loader

def Index(request):
    # 获取数据
    list = Question.objects.all()
    # 获取模版
    template = loader.get_template("polls/index.html")
    # 封装模型
    context = {"latest_question_list":list}
    # 返回响应
    return HttpResponse(template.render(context, request))

```

# 3 创建视图
```
# polls/templates/polls/index.html
{%if latest_question_list %}
    <ul>
        {% for question in latest_question_list %}
        {% endfor %}
    </ul>
{% else %}
{% endif %}
```

# 4 detail.html result.html 类似
```
from django.Http import Http404
from django.shortcut import render
# polls.views
def Result(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist!")
    return render(request, "polls/detail.html", {"question":question})

```

result.html 
```
<h1>{{question.question_text}}</h1>
<ul>
    {% for vote in question.vote_set.all() %}
        <li>{{vote.vote_text}}--{{vote.votes}}vote{{choice.votes|pluralize}}</li>
    {% endfor %}
</ul>
```

 
```
# mysite/polls/templates/polls/detail.html
<h1>{{question.question_text}}</h1>
{% if error_message %}
    <p><strong>{{error_message}}</strong></p>
{% endif %}

<form action="{{url 'polls:vote' question.id }}" method="post">
{% for vote in question.vote_set.all %}
    <input type="radio" name="vote" id="vote{{forloop.counter}}" value="{{vote.vote_text}}" />
{% endfor %}
<input type="submit" value="submit"/>
</form>
```

views.py
```
def vote(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
        vote = question.vote_set.get(request.POST['vote'])
    except Question.DoesNotExist:
        raise Http404(' record does not exist')
    except (KeyError, Vote.DoesNotExist):
        raise Http404(" vote does not exist")
    else:
        # 投票数加1
        vote.votes+=1
        vote.save()
        # 跳转到 result 页面
        return HttpResponseRedirect(reverse("polls:results", args=(question.id,)))
```

# 静态文件
默认静态文件是存放在 app 中的 static 目录下的，与template 类似，在部署项目后，可以使用
在 html 中可以通过加载 

使用，项目部署完成之后，可以通过
```
python manage.py collectstatic
```
对项目的所有 app 中的 static 文件进行汇总









