移動單頁面應用
===

為了實現在移動裝置上的訪問，這裡就以riot.js為例做一個簡單的Demo。不過，首先我們需要在後臺判斷使用者是來自於某種裝置，再對其進行特殊的處理。

移動裝置處理
---

幸運的是我們又找到了一個庫名為``django_mobile``，可以根據使用者的User-Agent來區別裝置，併為其分配一個移動裝置專用的模板。因此，我們需要安裝這個庫：

```
pip install django_mobile
```

並將``'django_mobile.middleware.MobileDetectionMiddleware'``和``'django_mobile.middleware.SetFlavourMiddleware'``新增MIDDLEWARE_CLASSES中：

```
MIDDLEWARE_CLASSES = (
    'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django.contrib.flatpages.middleware.FlatpageFallbackMiddleware',
    'django_mobile.middleware.MobileDetectionMiddleware',
    'django_mobile.middleware.SetFlavourMiddleware'
)
```

修改Template配置，新增對應的loader和context_processor，如下所示的內容即是修改完後的結果：

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            'templates/'
        ],
        'OPTIONS': {
            'context_processors':    [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                'django_mobile.context_processors.flavour'
            ],
        },
    },
]

# dirty fixed for https://github.com/gregmuellegger/django-mobile/issues/72
TEMPLATE_LOADERS = TEMPLATES[0]['OPTIONS']['loaders']
```

我們在LOADERS中新增了``'django_mobile.loader.Loader'``，在``context_processors``中新增了``django_mobile.context_processors.flavour``。

然後在template目錄中建立``template/mobile/index.html ``檔案，即可。


前後端分離
---

為了方便我們講述模組化，也不改變系統原有架構，我決定挖個大坑使用Riot.js來展示這一部分的內容。

### Riot.js

Riot擁有建立現代客戶端應用的所有必需的成分:

 - “響應式” 檢視層用來建立使用者介面
 - 用來在各獨立模組之間進行通訊的事件庫
 - 用來管理URL和瀏覽器回退按鈕的路由器（Router）

等等。

接著讓我們引入riot.js這個庫，順便也引入rxjs吧:

```
<script src="{% static 'js/mobile/riot+compiler.min.js' %}"></script>
<script src="{% static 'js/mobile/rx.core.min.js' %}"></script>
```

###ReactiveJS構建服務

由於我們所要做的服務比較簡單，並且我們也更願意使用Promise來載入API服務，因此我們引入了這個庫來加速我們的開發。下面是我們用於獲取部落格API的程式碼：

```
var responseStream = function (blogId) {
    var url = '/api/blogpost/?format=json';

    if(blogId) {
        url = '/api/blogpost/' + blogId + '?format=json';
    }

    return Rx.Observable.create(function (observer) {
        jQuery.getJSON(url)
            .done(function (response) {
                observer.onNext(response);
            })
            .fail(function (jqXHR, status, error) {
                observer.onError(error);
            })
            .always(function () {
                observer.onCompleted();
            });
    });
};
```

當我們想訪問特定部落格的時候，我們就傳部落格ID進去——這時會使用``'/api/blogpost/' + blogId + '?format=json'``作為URL。接著我們建立了自己定製的事件流——使用jQuery去獲取API：

 - 成功的時候(done)，我們將用onNext()來通知觀察者
 - 失敗的時候(fail)，我們就呼叫onError()來通知觀察者
 - 不論成功或者失敗，都會執行always

在使用的時候，我們只需要呼叫其``subscribe``方法即可：

```
responseStream().subscribe(function (response) {

})
```

### 建立部落格列表頁

現在，我們可以修改原生的部落格模板，將其中的container內容變為：

```
<div class="container" id="container">
    <blog></blog>
</div>
```

接著，我們可以建立一個``blog.tag``檔案，新增載入這個檔案:`

```
<script src="{% static 'riot/blog.tag' %}" type="riot/tag"></script>
```

為了呼叫這個tag的內容，我們需要在我們的``main.js``加上一句：

```javascript
riot.mount("blog");
```

隨後我們可以在我們的tag檔案中，來對blog的內容進行操作。

```html
<blog class="row">
    <div class="col-sm-4" each={ opts }>
        <h2><a href="#/blogDetail/{id}" onclick={ parent.click }>{ title }</a></h2>
        { body }
        { posted } - By { author }
    </div>
    <script>
        var self = this;
        this.on('mount', function (id) {
            responseStream().subscribe(function (response) {
                self.opts = response;
                self.update();
            })
        })

        click(event)
        {
            this.unmount();
            riot.route("blogDetail/" + event.item.id);
        }
    </script>
</blog>
```

在Riot中，變數預設是以opts的方式傳遞起來的，因此我們也遵循這個方式。在模板方面，我們遍歷每個部落格取出其中的內容：

```
<div class="col-sm-4" each={ opts }>
    <h2><a href="#/blogDetail/{id}" onclick={ parent.click }>{ title }</a></h2>
    { body }
    { posted } - By { author }
</div>
```

而部落格的資料需要依賴於我們監聽``mount``事件才會去獲取——即我們載入了這個tag。

```
this.on('mount', function (id) {
    responseStream().subscribe(function (response) {
        self.opts = response;
        self.update();
    })
})
```

在這個頁面中，還有一個單擊事件``onclick={ parent.click }``，即當我們點選某個部落格的標題時執行的函數:

```
click(event)
{
    this.unmount();
    riot.route("blog/" + event.item.id);
}
```
我們將解除安裝當前的tag，然後載入blogDetail的內容。

### 部落格詳情頁

在我們載入之前，我們需要先配置好blogDetail。我們仍然使用正規表示式``blogDetail/*``來獲取部落格的id:

```
riot.route.base('#');

riot.route('blog/*', function(id) {
    riot.mount("blogDetail", {id: id})
});

riot.route.start();
```

然後將由相應的tag來執行：

```
<blogDetail class="row">
    <div class="col-sm-4">
        <h2>{ opts.title }</h2>
        { opts.body }
        { opts.posted } - By { opts.author }
    </div>
    <script>
        var self = this;

        this.on('mount', function (id) {
            responseStream(this.opts.id).subscribe(function (response) {
                self.opts = response;
                self.update();
            })
        })
    </script>
</blogDetail>
```
同樣的，我們也將去獲取這篇部落格的內容，然後顯示。

### 新增導航

在上面的例子裡，我們少了一部分很重要的內容就是在頁面間跳轉，現在就讓我們來建立``navbar.tag``吧。

首先，我們需要重新規則一下route，在系統初始化的時候我們將使用的路由是blog，在進入詳情頁的時候，我們用blog/*。

```javascript
riot.route.base('#');

riot.route('blog/*', function (id) {
    riot.mount("blogDetail", {id: id})
});

riot.route('blog', function () {
    riot.mount("blog")
});

riot.route.start();

riot.route("blog");
```

然後將我們的navbar標籤放在``blog``和``blogDetail``中，如下所示：

```
<blogDetail class="row">
    <navbar title="{ opts.title }"></navbar>
    <div class="col-sm-4">
        <h2>{ opts.title }</h2>
        { opts.body }
        { opts.posted } - By { opts.author }
    </div>
</blogDetail>

當我們到了部落格詳情頁，我們將把標題作為參數傳給title。接著，我們在navbar中我們就可以創造一個breadcrumb導航了:

```
<navbar>
    <ol class="breadcrumb">
        <li><a href="#/" onclick={parent.clickTitle}>Home</a></li>
        <li if="opts.title">{ opts.title} </li>
    </ol>
</navbar>
```

最後可以在我們的blogDetail標籤中新增一個點選事件來跳轉到首頁：

```
clickTitle(event) {
    self.unmount(true);
    riot.route("blog");
}
```
