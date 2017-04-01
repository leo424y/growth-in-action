更完善的部落格系統
===

在Django框架中，內建了很多應用在它的"contrib"包中，這些包括：

 - 一個可擴充套件的認證系統
 - 動態站點管理頁面
 - 一組產生RSS和Atom的工具
 - 一個靈活的評論系統
 - 產生Google站點地圖（Google Sitemaps）的工具
 - 防止跨站請求偽造（cross-site request forgery）的工具
 - 一套支援輕量級標記語言（Textile和Markdown）的模板庫
 - 一套協助建立地理資訊系統（GIS）的基礎框架

這意味著，我們可以直接用Django一些內建的元件來完成很多功能，先讓我們來看看怎麼完成一個簡單的評論功能。


靜態頁面
---

Django帶有一個可選的“flatpages”應用，可以讓我們儲存簡單的“扁平化(flat)”頁面在資料庫中，並且可以通過Django的管理介面以及一個Python API來處理要管理的內容。這樣的一個靜態頁面，一般包含下面的幾個屬性：

 - 標題
 - URL
 - 內容(Content)
 - Sites
 - 自定義模板（可選）

為了使用它來建立靜態頁面，我們需要在資料庫中儲存對應的對映關係，並建立對應的靜態頁面。

### 安裝 flatpages

為此我們需要新增兩個應用到``settings.py``檔案的``INSTALLED_APPS``中：

 - ``django.contrib.sites``——“sites”框架，它用於將物件和功能與特定的站點關聯。同時，它還是域名和你的Django 站點名稱之間的對應關係所儲存的位置，即我們需要在這個地方設定我們的網站的域名。
 - ``django.contrib.flatpages``，即上文說到的內容。

在新增``django.contrib.sites``的時候，我們需要建立一個``SITE_ID``。通過這個值等於1，除非我們打算用這個框架去管理多個站點。程式碼如下所示：

```
SITE_ID = 1

INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django.contrib.sites',
    'django.contrib.flatpages',
    'blogpost'
)
```

接著，還新增對應的中介軟體``django.contrib.flatpages.middleware.FlatpageFallbackMiddleware``到``settings.py``檔案的``MIDDLEWARE_CLASSES``中。

然後，我們需要建立對應的URL來管理所有的靜態頁面。下面的程式碼是將靜態頁面都放在pages路徑下，即如果我們有一個about的頁面，那麼對應的URL會變成 http://localhost/pages/about/。

```python
url(r'^pages/', include('django.contrib.flatpages.urls')),
```

當然我們也可以將其配置為類似於 http://localhost/about/ 這樣的URL：

```
urlpatterns += [
    url(r'^(?P<url>.*/)$', views.flatpage),
]
```

最後，我們還需要做一個資料庫遷移：

```
Operations to perform:
  Apply all migrations: contenttypes, auth, admin, sites, blogpost, sessions, flatpages, django_comments
Running migrations:
  Rendering model states... DONE
  Applying flatpages.0001_initial... OK
```

### 建立模板

接著，我們可以在``templates``目錄下建立``flatpages``檔案，用於存放我們的模板檔案，下面是一個簡單的模板：

```
{% extends 'base.html' %}
{% block title %}關於我{% endblock %}

{% block content %}
<div>
<h2>關於部落格</h2>
    <p>一方面，找到更多志同道合的人；另一方面，擴大影響力。</p>
    <p>內容包括</p>
    <ul>
        <li>成長記錄</li>
        <li>技術筆記</li>
        <li>生活思考</li>
        <li>個人試驗</li>
    </ul>
</div>
{% endblock %}
```

當我們完成模板後，我們就需要登入後臺，並新增對應的靜態頁面的配置：

![管理員介面建立flatpage](http://growth-in-action.phodal.com/images/admin-flatpages-create.jpg)

然後從高階選項中填寫我們的靜態頁面的路徑，我們就可以完成靜態頁面的建立。如下圖所示：

![flatpage高階選項](http://growth-in-action.phodal.com/images/flatpages-advance-option.png)

最後，還要有個連結加到首頁的導航中：

```html
<li>
    <a href="/pages/about/">關於我</a>
</li>
```

下面讓我們為我們的部落格新增一個簡單的評論功能吧！

評論功能
---

在早期的Django版本(1.6以前)中，Comments是自帶的元件，但是後來它被從標準元件中移除了。因此，我們需要安裝comments這個包：

```
pip install django-contrib-comments
```

再把它及它的版本新增到``requirements.txt``，如下所示：

```
django==1.9.4
selenium==2.53.1
fabric==1.10.2
djangorestframework==3.3.3
djangorestframework-jwt==1.7.2
django-cors-headers==1.1.0
django-contrib-comments==1.7.1
```

接著，將``django.contrib.sites``和``django_comments``新增到``INSTALLED_APPS``，如下:

```python
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django.contrib.sites',
    'django_comments',
    'rest_framework',
    'blogpost'
)
```

然後做一下資料庫遷移我們就可以完成對其的初始化：

```
Operations to perform:
  Apply all migrations: contenttypes, admin, blogpost, auth, sites, sessions, django_comments
Running migrations:
  Rendering model states... DONE
  Applying sites.0001_initial... OK
  Applying django_comments.0001_initial... OK
  Applying django_comments.0002_update_user_email_field_length... OK
  Applying django_comments.0003_add_submit_date_index... OK
  Applying sites.0002_alter_domain_unique... OK
(growth-django)
```

然後再新增URL到urls.py:

```
url(r'^comments/', include('django_comments.urls')),
```

現在，我們就可以登入後臺，來建立對應的評論，但是這是時候評論是不會顯示到頁面上的。所以我們需要對我們的部落格詳情頁的模板進行修改，在其中新增一句:

```html
{% render_comment_list for post %}
```

用於顯示對應部落格的評論，最近我們的模板檔案如下面的內容所示：

```
{% extends 'base.html' %}
{% load comments %}

{% block head_title %}{{ post.title }}{% endblock %}
{% block title %}{{ post.title }}{% endblock %}

{% block content %}
<div class="mdl-card mdl-shadow--2dp">
    <div class="mdl-card__title">
        <h2 class="mdl-card__title-text"><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></h2>
    </div>
    <div class="mdl-card__supporting-text">
        {{post.body}}
    </div>
    <div class="mdl-card__actions">
        {{post.posted}} - By {{post.author}}
    </div>
</div>

{% render_comment_list for post %}

{% endblock %}
```

遺憾的是，當我們重新整理頁面的時候，頁面報錯了，原因如下所示：

![SITE_ID報錯](http://growth-in-action.phodal.com/images/site_id_issue.jpg)

我們還需要定義一個``SITE_ID``，新增下面的程式碼到``settings.py``檔案中即可：

```python
SITE_ID = 1
```

然後，我們就可以從後臺建立評論：

![後臺建立評論](http://growth-in-action.phodal.com/images/create-comment-backend.jpg)

Sitemap
---

我們在之前的文章中提到過SEO的重要性，這裡只是簡單地對Sitemap的內容進行展開。

### 站點地圖介紹

Sitemap譯為站點地圖，它用於告訴搜尋引擎他們網站上有哪些可供抓取的網頁。常見的Sitemap的形式是以xml出現了，如下是我部落格的sitemap.xml的一部分內容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
<url>
  <loc>https://www.phodal.com/blog/mezzanine-add-new-page/</loc>
  <lastmod>2014-08-03</lastmod>
  <changefreq>Monthly</changefreq>
  <priority>0.2</priority>
</url>
</urlset>
```

從上面的內容中，我們可以發現它包含了下面的一些XML標籤：

 - urlset，封裝該檔案，並指明當前協議的標準。
 - url，每個URL實體的父標籤。
 - loc，指明頁面的URL
 - lastmod（可選），內容最後的修改時間
 - changefreq（可選），內容的修改頻率，用於告知搜尋引擎抓取頻率。它包含的值有：``always``、``hourly``、``daily``、``weekly``、``monthly``、``yearly``、``never``
 - priority（可選），範圍是從0.0~1.0，搜尋引擎用於對你網站在搜尋結果的排序，即內部的優先順序排序。需要注意的是如果你把所有頁面的優先順序設定為1，那麼它就和沒有設定的效果是一樣的。

從上面的內容中，我們可以發現：

 > 站點地圖能夠提供與其中列出的網頁相關的寶貴後設資料：後設資料是網頁的相關資訊，例如網頁的最近更新時間、網頁的更改頻率以及網頁相較於網站中其他網址的重要程度。 ——內容來自 Google Sitemap幫助文件。


現在，我們一共有三種類型的頁面：

 - 首頁，通常來說首頁的priority應該是最高的，而它的``changefreq``可以設定為``daily``、``weekly``，這取決於你的部落格的更新頻率。如果你是做一些UGC(使用者生成內容)的網站，那麼你應該設定為``always``、``hourly``。
 - 動態生成的部落格詳情頁，這些內容一般很少進行改變，所以這的changefreq會比較低，如``yearly``或者``monthly``——並且沒有高的必要性，它會導致搜尋引擎一直抓取你的內容。這會對伺服器造成一定的壓力，並且無助於你網站的排名。
 - 靜態頁面，如About頁面，它可以有一個高的``priority``，但是它的``changefreq``也不一定很高。

下面就讓我們從首頁說起。

### 建立首頁的Sitemap

與上面建立靜態頁面時一樣，我們也需要新增``django.contrib.sitemaps``到``INSTALLED_APPS``中。

然後，我們需要指定一個URL規則。通常來說，這個URL是叫sitemap.xml——一個約定俗成的標準。我們需要建立一個sitemaps物件來儲存所有的sitemaps:

```
url(r'^sitemap\.xml$', sitemap, {'sitemaps': sitemaps}, name='django.contrib.sitemaps.views.sitemap')
```

由於，我們使用的檢視處理方法是``django.contrib.sitemaps.views.sitemap``，程式碼如下所示:

```

@x_robots_tag
def sitemap(request, sitemaps, section=None,
            template_name='sitemap.xml', content_type='application/xml'):

    req_protocol = request.scheme
    req_site = get_current_site(request)
```

在這個方法裡，它指定了預設模板的位置，即在``template``目錄中。

現在，我們需要建立幾種不同類型的sitemap，如下是首頁的Sitemap，它繼承自Django的Sitemap類：

```python
class PageSitemap(Sitemap):
    priority = 1.0
    changefreq = 'daily'

    def items(self):
        return ['main']

    def location(self, item):
        return reverse(item)

```

它定義了自己的priority是最高的1.0，同時每新頻率為``daily``。然後在items裡面去取它所要獲取的URL，即``urls.py``中對應的``name``的``main``的URL。在這裡我們只返回了``main``一個值，依據於下面的location方法中的``reverse``，它找到了main對應的URL，即首頁。

最後結合首頁sitemap.xml的``urls.py``程式碼如下所示：

```python
from sitemap.sitemaps import PageSitemap

sitemaps =  {
    "page": PageSitemap
}

urlpatterns = patterns('',
    url(r'^$', blogpostViews.index, name='main'),
    url(r'^blog/(?P<slug>[^\.]+).html', 'blogpost.views.view_post', name='view_blog_post'),
    url(r'^comments/', include('django_comments.urls')),
    url(r'^admin/', include(admin.site.urls)),
    url(r'^pages/', include('django.contrib.flatpages.urls')),
    url(r'^sitemap\.xml$', sitemap, {'sitemaps': sitemaps}, name='django.contrib.sitemaps.views.sitemap')
) + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

除此，我們還需要建立自己的``sitemap.xml``模板——自帶的系統模板比較簡單。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
{% spaceless %}
{% for url in urlset %}
<url>
    <loc>{{ url.location }}</loc>
    {% if url.lastmod %}<lastmod>{{ url.lastmod|date:"Y-m-d" }}</lastmod>{% endif %}
    {% if url.changefreq %}<changefreq>{{ url.changefreq }}</changefreq>{% endif %}
    {% if url.priority %}<priority>{{ url.priority }}</priority>{% endif %}
</url>
{% endfor %}
{% endspaceless %}
</urlset>
```

最後，我們訪問[http://localhost:8000/sitemap.xml](http://localhost:8000/sitemap.xml)，我們就可以獲取到我們的``sitemap.xml``：

```
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    <url>
        <loc>http://www.phodal.com/</loc>
        <changefreq>daily</changefreq>
        <priority>1.0</priority>
    </url>
</urlset>
```

下一步，我們仍可以直接建立出對應的靜態頁面的Sitemap。

### 建立靜態頁面的Sitemap

相似的，我們也需要從items方法中，定義出我們所要建立頁面的物件。

```python
from django.contrib.sitemaps import Sitemap
from django.core.urlresolvers import reverse
from django.apps import apps as django_apps

class FlatPageSitemap(Sitemap):
    priority = 0.8

    def items(self):
        Site = django_apps.get_model('sites.Site')
        current_site = Site.objects.get_current()
        return current_site.flatpage_set.filter(registration_required=False)
```

只不過這個方法可能會稍微麻煩一些，我們需要從資料庫中取出當前的站點。再取出當前站點中的flatpage集合，對過濾那些不需要註冊的頁面，即程式碼中的``registration_required=False``。

最後再將這個物件放入sitemaps即可：

```
from sitemap.sitemaps import PageSitemap, FlatPageSitemap

sitemaps =  {
    "page": PageSitemap,
    'flatpages': FlatPageSitemap
}
```

現在，我們可以完成部落格的Sitemap了。

### 建立部落格的Sitemap

同上面一樣的是，我們依然需要在items方法中返回所有的部落格內容。並且在lastmod中，返回這篇部落格的發表日期——以免他們返回的是同一個日期：

```python
class BlogSitemap(Sitemap):
    changefreq = "never"
    priority = 0.5

    def items(self):
        return Blogpost.objects.all()

    def lastmod(self, obj):
        return obj.posted
```

最近我們的Sitemap.xml，如下所示:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    <url>
        <loc>http://www.phodal.com/about/</loc>
        <priority>0.8</priority>
    </url>
    <url>
        <loc>http://www.phodal.com/</loc>
        <changefreq>daily</changefreq>
        <priority>1.0</priority>
    </url>
    <url>
        <loc>http://www.phodal.com/blog/hello.html</loc>
        <lastmod>2016-03-24</lastmod>
        <changefreq>never</changefreq>
        <priority>0.5</priority>
    </url>
</urlset>
```

### 提交到搜尋引擎

這裡我們以Google Webmaster為例簡單的介紹一下如何使用各種站長工具來提交sitemap.xml。

我們可以登入Google的Webmaster：[https://www.google.com/webmasters/tools/home?hl=zh-cn](https://www.google.com/webmasters/tools/home?hl=zh-cn)，然後點選新增屬性來建立一個新的網站:

![新增網站](http://growth-in-action.phodal.com/images/add-property.png)

這時候Google需要確認這個網站是你的，所以它提供幾種方法來驗證，除了下面的推薦方法：

![推薦的驗證方式](http://growth-in-action.phodal.com/images/google-add-website.png)

我們可以使用下面的這一些方法：

![備選的難方法](http://growth-in-action.phodal.com/images/google-addition-method.png)

我個人比較喜歡用HTML Tag的方式來實現

![HTML標籤驗證](http://growth-in-action.phodal.com/images/html-tag.png)

在我們完成驗證之後，我們就可以在後臺手動提交Sitemap.xml了。

![提交Sitemap.xml](http://growth-in-action.phodal.com/images/google-add-sitemap.png)

點選上方的**新增/測試站點地圖**即可。
