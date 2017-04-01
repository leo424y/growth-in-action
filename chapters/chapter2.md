三步建立部落格應用
===

Tasking
---

在我們不瞭解Django的時候，要對這樣一個任務進行Tasking，有點困難。不過，我們還是可以簡單地看看是應該如何去做：

 - 生成APP。對於大部分主流的Web框架來說，它們都可以手動地生成一些腳手架，如Ruby語言中的Ruby On Rails、Node.js中的Express等等。
 - 建立對應的Model，即其在資料庫中儲存的模型與我們在程式碼中要使用的模型。
 - 建立程式對應的View，用於處理資料。
 - 建立程式的Template，用於顯示資料。
 - 編寫測試來保證功能。

對於其他應用來說也是差不多的。

建立BlogpostAPP
---

### 生成APP

現在我們可以開始建立我們的APP，使用下面的程式碼來建立：

$ django-admin startapp blogpost

會在blogpost目錄下，生成下面的檔案：


```
.
├── __init__.py
├── admin.py
├── apps.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
```

### 建立Model

現在，我們需要來建立部落格的Model即可。對於一篇基本的部落格來說，它會包含下面的幾部分內容：

 - 標題
 - 作者
 - 連結（中文更需要一個好的連結）
 - 內容
 - 釋出日期

我們就可以按照上面的內容來建立我們的Blogpost model：

```python
from django.db import models
from django.db.models import permalink


class Blogpost(models.Model):
    title = models.CharField(max_length=100, unique=True)
    author = models.CharField(max_length=100, unique=True)
    slug = models.SlugField(max_length=100, unique=True)
    body = models.TextField()
    posted = models.DateField(db_index=True, auto_now_add=True)

    def __unicode__(self):
        return '%s' % self.title

    @permalink
    def get_absolute_url(self):
        return ('view_blog_post', None, { 'slug': self.slug })
```

上面的``get_absolute_url``方法就是用於返回部落格的連結。之所以使用手動而不是自動生成，是因為自動生成不靠譜，而且不利

然後在Admin註冊這個Model

```python
from django.contrib import admin
from blogpost.models import Blogpost

class BlogpostAdmin(admin.ModelAdmin):
    exclude = ['posted']
    prepopulated_fields = {'slug': ('title',)}

admin.site.register(Blogpost, BlogpostAdmin)
```

接著我們需要先將``blogpost``這個APP新增到``setting.py``中的``INSTALLED_APPS``欄位中。然後做資料庫遷移：

```shelln
python manage.py migrate
```

這時會提示:

```shell
Operations to perform:
  Apply all migrations: admin, contenttypes, auth, sessions
Running migrations:
  No migrations to apply.
  Your models have changes that are not yet reflected in a migration, and so won't be applied.
  Run 'manage.py makemigrations' to make new migrations, and then re-run 'manage.py migrate' to apply them.
```

是因為我們忘記了先執行

``` shell
python manage.py makemigrations
```

進入後臺，我們就可以看到BLOGPOST的一欄裡，就可以對其進行相關的操作。

![Django後臺介面](http://growth-in-action.phodal.com/images/django-admin-ui.png)

點選Blogpost的Add後，我們就會進入如下的新增部落格介面：

![Django新增部落格](http://growth-in-action.phodal.com/images/admin-blog.png)

實際上，這樣做的意義是將刪除(Delete)、修改(Update)、新增(Create)這些內容交給使用者後臺來做，當然它也不需要在View/Template層來做。在我們的Template層中，我們只需要關心如何來顯示這些資料。

現在，我們可以執行一次新的程式碼提交——因為現在的程式碼可以正常工作。這樣出現問題時，我們就可以即時的返回上一版本的程式碼。

```
git add .
git commit -m "create blogpost model"
```

然後再進行下一步地操作。

### 配置URL

現在，我們就可以在我們的``urls.py``裡新增相應的route來訪問頁面，程式碼如下所示：

```python
from django.conf import settings
from django.conf.urls import include, url
from django.conf.urls.static import static
from django.contrib import admin


urlpatterns = [
    (r'^$', 'blogpost.views.index'),
    url(r'^blog/(?P<slug>[^\.]+).html', 'blogpost.views.view_post', name='view_blog_post'),
    url(r'^admin/', include(admin.site.urls))
]
```

在上面的程式碼裡，我們建立了兩個route：

 - 指向首頁，其view是index
 - 指向部落格詳情頁，其view是view_post

指向部落格詳情頁的URL正則``r'^blog/(?P<slug>[^\.]+).html``，會將形如blog/hello-world.html中的hello-world提取出來作為參數傳給view_post方法。

接著，我們就可以建立兩個view。

建立View
---

### 建立部落格列表頁

對於我們的首頁來說，我們可以簡單的只顯示五篇部落格，所以我們所需要做的就是從我們的Blogpost物件中，取出前五個結果即可。程式碼如下所示：

```python
from django.shortcuts import render, render_to_response, get_object_or_404
from blogpost.models import Blogpost

def index(request):
    return render_to_response('index.html', {
        'posts': Blogpost.objects.all()[:5]
    })
```

Django的render_to_response方法可以根據一個給定的上下文字典渲染一個給定的目標，並返回渲染後的HttpResponse。即將相應的值，如這裡的Blogpost.objects.all()[:5]，填入相應的index.html中，再返回最後的結果。

首先，我們需要建立一個templates資料夾，然後在setting.py的TEMPLATES欄位將該目錄指定為預設目錄
```python
 TEMPLATES = [
      {
          'BACKEND': 'django.template.backends.django.DjangoTemplates',
          'DIRS': ['templates/'],
          'APP_DIRS': True,
          'OPTIONS': {
              'context_processors': [
                  'django.template.context_processors.debug',
                  'django.template.context_processors.request',
                 'django.contrib.auth.context_processors.auth',
                 'django.contrib.messages.context_processors.messages',
             ],
         },
     },
 ]
```
另外，在templates目錄下我們需要新建base.html, index.html和blogpost_detail.html三個模板。

```html
{% load staticfiles %}
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>{% block head_title %}Welcome to my blog{% endblock %}</title>
    <link rel="stylesheet" type="text/css" href="{% static 'css/bootstrap.min.css' %}">
    <link rel="stylesheet" type="text/css" href="{% static 'css/styles.css' %}">
</head>
<body data-twttr-rendered="true" class="bs-docs-home">
<header class="navbar navbar-static-top bs-docs-nav" id="top" role="banner">
    <div class="container">
        <div class="navbar-header">
            <button class="navbar-toggle collapsed" type="button" data-toggle="collapse"
                    data-target=".bs-navbar-collapse">
                <span class="sr-only">切換檢視</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a href="/" class="navbar-brand">Growth部落格</a>
        </div>
        <nav class="collapse navbar-collapse bs-navbar-collapse" role="navigation">
            <ul class="nav navbar-nav">
                <li>
                    <a href="/pages/about/">關於我</a>
                </li>
                <li>
                    <a href="/pages/resume/">簡歷</a>
                </li>
            </ul>
            <ul class="nav navbar-nav navbar-right">
                <li><a href="/admin" id="loginLink">登入</a></li>
            </ul>
            <div class="col-sm-3 col-md-3 pull-right">
                <form class="navbar-form" role="search">
                    <div class="input-group">
                        <input type="text" id="typeahead-input" class="form-control" placeholder="Search" name="search" data-provide="typeahead">
                        <div class="input-group-btn">
                            <button class="btn btn-default search-button" type="submit"><i class="glyphicon glyphicon-search"></i></button>
                        </div>
                    </div>
                </form>
            </div>
        </nav>
    </div>
</header>
<main class="bs-docs-masthead" id="content" role="main">
    <div class="container">
        <div id="carbonads-container">
            THE ONLY FAIR IS NOT FAIR <br>
            ENJOY CREATE & SHARE
        </div>
    </div>
</main>
<div class="container" id="container">
    {% block content %}

    {% endblock %}
</div>
<footer class="footer">
    <div class="container">
        <p class="text-muted">@Copyright Phodal.com</p>
    </div>
</footer>
<script src="{% static 'js/jquery.min.js' %}"></script>
<script src="{% static 'js/bootstrap.min.js' %}"></script>
<script src="{% static 'js/bootstrap3-typeahead.min.js' %}"></script>
<script src="{% static 'js/main.js' %}"></script>
</body>
</html>
```

在我們的index.html中，我們就可以拿到前五篇部落格。我們只需要遍歷出posts，拿出每個post相應的值，就可以完成列表頁。

```html
{% extends 'base.html' %}
{% block title %}Welcome to my blog{% endblock %}

{% block content %}
<h1>Posts</h1>
{% for post in posts %}
<h2><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></h2>
<p>{{post.posted}} - By {{post.author}}</p>
<p>{{post.body}}</p>
{% endfor %}
{% endblock %}
```

在上面的模板裡，我們還取出了部落格的連結用於跳轉到詳情頁。

### 建立部落格詳情頁

依據上面拿到的slug，我們就可以建立對應的詳情頁的view，程式碼如下所示：

```python
def view_post(request, slug):
    return render_to_response('blogpost_detail.html', {
        'post': get_object_or_404(Blogpost, slug=slug)
    })
```

這裡的``get_object_or_404``將會根據slug來獲取相應的部落格，如果取不出相應的部落格就會返回404。因此，我們的詳情頁和上面的列表頁也是類似的。

```html
{% extends 'base.html' %}
{% block head_title %}{{ post.title }}{% endblock %}
{% block title %}{{ post.title }}{% endblock %}

{% block content %}
<h2>{{ post.title }}</a></h2>
<p>{{post.posted}} - By {{post.author}}</p>
<p>{{post.body}}</p>
{% endblock %}
```

隨後，我們就可以再提交一次程式碼了。

測試
---

TDD雖然是一個非常好的實踐，但是那是對於那些已經習慣寫測試的人來說。如果你寫測試的經歷非常少，那麼我們就可以從寫測試開始。

在這裡我們使用的是Django這個第三方框架來完成我們的工作，所以我們並不對這個框架的功能進行測試。雖然有些時候正是因為這些第三方框架的問題而導致的Bug，但是我們僅僅只是使用一些基礎的功能。這些基礎的功能也已經在他們的框架中測試過了。

### 測試首頁

先來做一個簡單的測試，即測試我們訪問首頁的時候，呼叫的函數是上面的index函數

```python
from django.core.urlresolvers import resolve
from django.http import HttpRequest
from django.test import TestCase

from blogpost.views import index, view_post


class HomePageTest(TestCase):
    def test_root_url_resolves_to_home_page_view(self):
        found = resolve('/')
        self.assertEqual(found.func, index)
```

但是這樣的測試看上去沒有多大意義，不過它可以保證我們的route可以和我們的URL對應上。在編寫完測試後，我們就可以命令提示行中執行:

```bash
python manage.py test
```

來檢視測試的結果：

```
Creating test database for alias 'default'...

.
----------------------------------------------------------------------
Ran 1 test in 0.031s

OK
Destroying test database for alias 'default'...
(growth-django)
```

執行通過，現在我們可以進行下一個測試了——我們可以測試頁面的標題是不是我們想要的結果：

```python
    def test_home_page_returns_correct_html(self):
        request = HttpRequest()
        response = index(request)
        self.assertIn(b'<title>Welcome to my blog</title>', response.content)
```

這裡我們需要去請求相應的頁面來獲取頁面的標題，並用assertIn方法來斷言返回的首頁的html中含有``<title>Welcome to my blog</title>``。

### 測試詳情頁

同樣的我們也可以用測試是否呼叫某個函數的方法，來看部落格的詳情頁的route是否正確？

```python
class BlogpostTest(TestCase):
    def test_blogpost_url_resolves_to_blog_post_view(self):
        found = resolve('/blog/this_is_a_test.html')
        self.assertEqual(found.func, view_post)
```

與上面測試首頁不一樣的是，在我們的Blogpost測試中，我們需要建立資料，以確保這個流程是沒有問題的。因此我們需要用``Blogpost.objects.create``方法來建立一個資料，然後訪問相應的頁面來看是否正確。

```python
def test_blogpost_create_with_view(self):
    Blogpost.objects.create(title='hello', author='admin', slug='this_is_a_test', body='This is a blog',
                            posted=datetime.now)
    response = self.client.get('/blog/this_is_a_test.html')
    self.assertIn(b'This is a blog', response.content)
```

或許你會疑惑這個資料會不會被注入到資料庫中，請看執行測試時返回的結果的第一句：

```
Creating test database for alias 'default'...
```

Django將會建立一個資料庫用於測試。

同理，我們也可以為首頁新增一個相似的測試：

```python
def test_blogpost_create_with_show_in_homepage(self):
    Blogpost.objects.create(title='hello', author='admin', slug='this_is_a_test', body='This is a blog',
                            posted=datetime.now)
    response = self.client.get('/')
    self.assertIn(b'This is a blog', response.content)
```

我們用同樣的方法建立了一篇部落格，然後在首頁測試返回的內容中是否含有``This is a blog``。
