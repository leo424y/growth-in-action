應用API
===

在下一章開始之前，我們先來搭建一下API平臺，不僅僅可以提供一些額外的功能，還可以為我們的APP提供API。

部落格列表
---

### Django REST Framework

在這裡，我們需要用到一個名為Django REST Framework的RESTful API庫。通過這個庫，我們可以快速建立我們所需要的API。

Django REST Framework 這個名字很直白，就是基於 Django 的 REST 框架。因此，首先我們仍是要安裝這個庫：

```
pip install djangorestframework
```

然後把它新增到``INSTALLED_APPS``中：

```python
INSTALLED_APPS = (
    ...
    'rest_framework',
)
```

如下所示：

```
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'blogpost'
)
```

接著我們可以在我們的API中建立一個URL，用於匹配它的授權機制。

```
urlpatterns = [
    ...
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
```

不過這個API，目前並沒有多大的用途。只有當我們在製作一些需要許可權驗證的介面時，它才會突顯它的重要性。

### 建立部落格列表API

為了方便我們繼續展開後面的內容，我們先來建立一個部落格列表API。參考Django REST Framework的官方文件，我們可以很快地建立出下面的Demo:

```
from django.contrib.auth.models import User
from rest_framework import serializers, viewsets
from blogpost.models import Blogpost


class BlogpsotSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Blogpost
        fields = ('title', 'author', 'body', 'slug')

class BlogpostSet(viewsets.ModelViewSet):
    queryset = Blogpost.objects.all()
    serializer_class = BlogpsotSerializer
```

在上面這個例子中，API由兩個部分組成：

 - ViewSets，用於定義檢視的展現形式——如返回哪些內容，需要做哪些許可權處理
 - Serializers，用於定義API的表現形式——如返回哪些欄位，返回怎樣的格式

我們在我們的URL中，會定義相應的規則到ViewSet，而ViewSet則通過``serializer_class``找到對應的``Serializers``。我們將Blogpost的所有物件賦予queryset，並返回這些值。在BlogpsotSerializer中，我們定義了我們要返回的幾個欄位：``title``、``author``、``body``、``slug``。

接著，我們可以在我們的``urls.py``配置URL。

```python
...

from rest_framework import routers
from blogpost.api import BlogpostSet

apiRouter = routers.DefaultRouter()
apiRouter.register(r'blogpost', BlogpostSet)

urlpatterns = [,
    ...
    url(r'^api/', include(apiRouter.urls)),
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

我們使用預設的Router來配置我們的URL，即DefaultRouter，它提供了一個非常簡單的機制來自動檢測URL規則。因此，我們只需要註冊好我們的url——``blogpost``以及它值``BlogpostSet``即可。隨後，我們再為其定義一個根URL即可:

```python
url(r'^api/', include(apiRouter.urls))
```

### 測試 API

現在，我們可以訪問[http://127.0.0.1:8000/api/](http://127.0.0.1:8000/api/)來訪問我們現在的API。由於Django REST Framework提供了一個UI機制，所以我們可以在網頁上直接看到我們所有的API：

![Django REST Framework列表](http://growth-in-action.phodal.com/images/django-rest-framework-api-lists.png)

然後，點選頁面中的[http://127.0.0.1:8000/api/blogpost/](http://127.0.0.1:8000/api/blogpost/)，我們就可以訪問部落格相關的API了，如下圖所示:

![部落格API](http://growth-in-action.phodal.com/images/drf-blogppost-set-list.png)

在頁面上顯示了所有的部落格內容，在頁面的下面有一個表單可以先讓我們來建立資料：

![建立部落格的表單](http://growth-in-action.phodal.com/images/api-post-form.png)

直接在表單中新增資料，我們就可以完成資料建立了。

當然，我們也可以直接用命令列工具來測試，執行：

```bash
curl -i  http://127.0.0.1:8000/api/blogpost/
```

即可返回相應的結果：

![CuRL API](http://growth-in-action.phodal.com/images/curl-api.png)

自動完成
---

AutoComplete是一個很有意思的功能，特別是當我們的文章很多的時候，我們可以讓讀者有機會能搜尋到相應的功能。以Google為例，Google在我們輸入一些關鍵字的時候，會向我們推薦一些比較流行的詞條可以讓我們選擇。

![Google AutoComplete](http://growth-in-action.phodal.com/images/google-autocomplete.png)

同樣的，我們也可以實現一個同樣的效果用於我們的部落格搜尋：

![自動完成](http://growth-in-action.phodal.com/images/autocomplete-example.png)

當我們輸入某一些關鍵字的時候，就會出現文章的標題，隨後我們只需要點選相應的標題即可跳轉到文章。

### 搜尋API

為了實現這個功能我們需要對之前的部落格API做一些簡單的改造——可以支援搜尋部落格標題。這裡我們需要稍微擴充套件一下我們的部落格API即可：

```python
class BlogpostSet(viewsets.ModelViewSet):
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
    serializer_class = BlogpsotSerializer
    search_fields = 'title'

    def list(self, request):
        queryset = Blogpost.objects.all()

        search_param = self.request.query_params.get('title', None)
        if search_param is not None:
            queryset = Blogpost.objects.filter(title__contains=search_param)

        serializer = BlogpsotSerializer(queryset, many=True)
        return Response(serializer.data)
```

我們新增了一個名為``search_fields``的變數，顧名思義就是定義搜尋欄位。接著我們覆寫了ModelViewSet的list方法，它是用於列出(list)所有的結果。我們會嘗試在我們的請求中獲取搜尋參量，如果沒有的話我們就返回所有的結果。如果搜尋的參數中含有標題，則從所有部落格中過濾出標題中含有搜尋標題中的內容，再返回這些結果。如下是一個搜尋的URL：[http://127.0.0.1:8000/api/blogpost/?format=json&title=test](http://127.0.0.1:8000/api/blogpost/?format=json&title=test)，我們搜尋標題中含有``test``的內容。

同時，我們還需要為我們的apiRouter設定一個basename，即下面程式碼中最後的``Blogpost``

```python
apiRouter.register(r'blogpost', BlogpostSet, 'Blogpost')
```

### 頁面實現

接著，我們就可以在頁面上實現這個功能。在這裡我們使用一個名為[Bootstrap-3-Typeahead](https://github.com/bassjobsen/Bootstrap-3-Typeahead)的外掛來實現，下載這個外掛以及它對應的CSS：[https://github.com/bassjobsen/typeahead.js-bootstrap-css](https://github.com/bassjobsen/typeahead.js-bootstrap-css)，並新增到``base.html``中，然後建立一個``main.js``檔案負責相關的邏輯處理。

```html
<script src="{% static 'js/jquery.min.js' %}"></script>
<script src="{% static 'js/bootstrap.min.js' %}"></script>
<script src="{% static 'js/bootstrap3-typeahead.min.js' %}"></script>
<script src="{% static 'js/main.js' %}"></script>
```

接著我們需要在頁面上建立對應的UI，我們可以直接在``登入``後面新增這個搜尋按鈕：

```
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
```

我們主要是使用input標籤，標籤上對應有一個id

```
<input type="text" id="typeahead-input" class="form-control" placeholder="Search"
```

對應於這個ID，我們就可以開始編寫我們的功能了：

```javascript
$(document).ready(function () {
    $('#typeahead-input').typeahead({
        source: function (query, process) {
            return $.get('/api/blogpost/?format=json&title=' + query, function (data) {
                return process(data);
            });
        },
        updater: function (item) {
            return item;
        },
        displayText: function (item) {
            return item.title;
        },
        afterSelect: function (item) {
            location.href = 'http://localhost:8000/blog/' + item.slug + ".html";
        },
        delay: 500
    });
});
```

$(document).ready()方法可以是在DOM完成載入後，執行其中的函數。接著我們開始監聽``#typeahead-input``，對應的便是id為``typeahead-input``的元素。可以看到在這其中有五個物件：

 - source，即搜尋的來源，我們返回的是我們搜尋的URL。
 - updater，即每次更新要做的事
 - displayText，顯示在頁面上的內容，如在這裡我們返回的是部落格的標題
 - afterSelect，每使用者選中某一項後做的事，這裡我們直接中轉到對應的部落格。
 - delay，延時500ms。

雖然我們使用的是外掛來完成我們的功能，但是總體的處理邏輯是：

1. 監聽我們的輸入文字
2. 獲取API的返回結果
3. 對返回結果進行處理——如高亮輸入文字、顯示到頁面上
4. 處理使用者點選事件

跨域支援
---

當我們想為其他的網頁提供我們的API時，可能會報錯——原因是不支援跨域請求。為了方便我們下一章更好的展開，內容我們在這裡對跨域進行支援。

### 新增跨域支援

有一個名為``django-cors-headers``的外掛用於實現對跨域請求的支援，我們只需要安裝它，並進行一些簡單的配置即可。

```bash
pip install django-cors-headers
```

安裝過程如下：

```
Collecting django-cors-headers
  Downloading django-cors-headers-1.1.0.tar.gz
Building wheels for collected packages: django-cors-headers
  Running setup.py bdist_wheel for django-cors-headers ... done
  Stored in directory: /Users/fdhuang/Library/Caches/pip/wheels/b0/75/89/7b17f134fc01b74e10523f3128e45b917da0c5f8638213e073
Successfully built django-cors-headers
Installing collected packages: django-cors-headers
Successfully installed django-cors-headers-1.1.0
```

我們還需要新增到``django-cors-headers=1.1.0``到``requirements.txt``檔案中，以及新增到``settings.py``中：

```
INSTALLED_APPS = (
    ...
    'corsheaders',
    ...
)
```

以及對應的中介軟體：

```
MIDDLEWARE_CLASSES = (
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
)
```

同時還有對應的配置：

```
CORS_ALLOW_CREDENTIALS = True
```

現在，讓我們進行下一步，開始APP吧！
