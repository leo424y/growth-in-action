樣式與UI美化
===

我們的前端樣式實在是太醜了，讓我們想辦法來美化一下它們吧——這時候我們就需要一個前端框架來幫助我們做這件事。這裡的前端框架並不是指那種MV*框架，而是UI框架。

響應式設計
---

考慮到易學程度，以其響應式設計的問題，我們決定用Bootstrap來作為這裡的前端框架。Bootstrap是Twitter推出的一個用於前端開發的開源工具包，似乎也是當前“最受歡迎”的前端框架。它提供了全面、美觀的文件。你能在這裡找到關於 HTML 元素、HTML 和 CSS 元件、jQuery 外掛方面的所有詳細文件。並且我們能在 Bootstrap 的幫助下通過同一份程式碼快速、有效適配手機、平板、PC 裝置。

它是一個支援響應式設計的框架，即頁面的設計與開發應當根據使用者行為以及裝置環境(系統平臺、螢幕尺寸、螢幕定向等)進行相應的響應和調整。如下圖所示：

![響應式設計](http://growth-in-action.phodal.com/images/responsive-design.png)

我們在不同的設計上看到的是不同的佈局，這會依據我們的裝置大小做出調整——使用媒體查詢(media queries)實現。

### 引入前端框架

下好Bootstrap，將裡面的內容複製到``static/``目錄，如下所示：

```
.
├── css
│   ├── bootstrap-theme.css
│   ├── bootstrap-theme.css.map
│   ├── bootstrap-theme.min.css
│   ├── bootstrap-theme.min.css.map
│   ├── bootstrap.css
│   ├── bootstrap.css.map
│   ├── bootstrap.min.css
│   ├── bootstrap.min.css.map
│   └── styles.css
├── fonts
│   ├── glyphicons-halflings-regular.eot
│   ├── glyphicons-halflings-regular.svg
│   ├── glyphicons-halflings-regular.ttf
│   ├── glyphicons-halflings-regular.woff
│   └── glyphicons-halflings-regular.woff2
└── js
    ├── bootstrap.js
    ├── bootstrap.min.js
    └── npm.js
```

它包含了JavaScript、CSS還有字型，需要注意的一點是bootstrap依賴於jquery。因此，我們需要下載jquery並放到這個目錄裡。然後在我們的head裡引入這些css

```
<head>
    <title>{% block head_title %}Welcome to my blog{% endblock %}</title>
    <link rel="stylesheet" type="text/css" href="{% static 'css/bootstrap.min.css' %}">
</head>
```

在我們的body結尾的地方：

```
<script src="{% static 'js/jquery.min.js' %}"></script>
<script src="{% static 'js/bootstrap.min.js' %}"></script>
</body>
</html>
```

在這裡，將Script放在body的尾部有利於使用者開啟頁面的速度。而對於一些純前端的框架來說，它們就需要放在頁面開始的地方。

頁面美化
---

現在，我們就可以建立一個導航了。

### 新增導航

根據Bootstrap的官方文件的Demo，我們可以建立對應的導航。

```html
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

        </nav>
    </div>
</header>
```

它在桌面下的效果大致如下圖所示：

![桌面瀏覽器下的Bootstrap導航](http://growth-in-action.phodal.com/images/bootstrap-nav-desktop.png)

而在移動瀏覽器下則是這樣的效果：

![移動裝置上的導航](http://growth-in-action.phodal.com/images/nav-in-mobile.png)

當我們點選右上角的選單按鈕時，會出現我們的選單

![點選導航後的結果](http://growth-in-action.phodal.com/images/nav-in-mobile-with-click.png)

### 新增標語

接著，我們可以快速的建立一個標語：

```
<main class="bs-docs-masthead" id="content" role="main">
    <div class="container">
        <div id="carbonads-container">
            THE ONLY FAIR IS NOT FAIR <br>
            ENJOY CREATE & SHARE
        </div>
    </div>
</main>
```

這裡的程式碼都比較簡單，我想也不需要太多的解釋。

### 優化列表

接著，我們可以簡單的對首頁的部落格列表做一個優化，方法比較簡單：

 - 為部落格列表新增一個``row``的class，表示它可以滾動
 - 在每一篇部落格裡新增``col-sm-4``的class，在不同的大小下會有不同的佈局

程式碼如下所示：

```
{% extends 'base.html' %}
{% block title %}Welcome to my blog{% endblock %}

{% block content %}
<h2>部落格</h2>
<div class="row">
    {% if posts %}
    {% for post in posts %}
    <div class="col-sm-4">
        <h2><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></h2>
        {{post.body | slice:":80"}}
        {{post.posted}} - By {{post.author}}
    </div>
    {% endfor %}
    {% else %}
    <p>There are no posts.</p>
    {% endif %}
</div>
{% endblock %}
```

它在桌面和自動裝置上的效果如下圖所示：

![桌面裝置效果](http://growth-in-action.phodal.com/images/desktop-blogposts.png)

![移動裝置效果](http://growth-in-action.phodal.com/images/mobile-blogposts.png)

### 新增footer

最後，我們可以在頁面的最下方新增一個footer，來做一些版權聲明：

```html
<footer class="footer">
    <div class="container">
        <p class="text-muted">@Copyright Phodal.com</p>
    </div>
</footer>
```

它擁有一些簡單的樣式，來將footer固定在頁面的最下方：

```css
.footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  /* Set the fixed height of the footer here */
  height: 60px;
  background-color: #f5f5f5;
}
.footer .container {
  width: auto;
  max-width: 680px;
  padding: 0 15px;
}

.footer .container .text-muted {
  margin: 20px 0;
}
```
