建立移動應用
===

依照國際慣例，我們還將用Ionic 2繼續建立hello,world。

hello,world
---

開始之前我們需要先安裝Ionic的命令列工具，後面我們需要用這個工具來建立工程。

```bash
npm install -g ionic@beta
```

如果沒有意外，我們將安裝成功，然後可以使用``ionic``命令:

它自帶了一系列的工具來加速我們的開發，這些工具可以在後面的章節中學習到。

```
Available tasks: (use --help or -h for more info)

   start  ..........  Starts a new Ionic project in the specified PATH
   serve  ..........  Start a local development server for app dev/testing
   platform  .......  Add platform target for building an Ionic app
   run  ............  Run an Ionic project on a connected device
   emulate  ........  Emulate an Ionic project on a simulator or emulator
   build  ..........  Build (prepare + compile) an Ionic project for a given platform.

   plugin  .........  Add a Cordova plugin
   resources  ......  Automatically create icon and splash screen resources (beta)
		      Put your images in the ./resources directory, named splash or icon.
		      Accepted file types are .png, .ai, and .psd.
		      Icons should be 192x192 px without rounded corners.
		      Splashscreens should be 2208x2208 px, with the image centered in the middle.

   upload  .........  Upload an app to your Ionic account
   share  ..........  Share an app with a client, co-worker, friend, or customer
   lib  ............  Gets Ionic library version or updates the Ionic library
   setup  ..........  Configure the project with a build tool (beta)
   io  .............  Integrate your app with the ionic.io platform services (alpha)
   security  .......  Store your app's credentials for the Ionic Platform (alpha)
   push  ...........  Upload APNS and GCM credentials to Ionic Push (alpha)
   package  ........  Use Ionic Package to build your app (alpha)
   config  .........  Set configuration variables for your ionic app (alpha)
   browser  ........  Add another browser for a platform (beta)
   service  ........  Add an Ionic service package and install any required plugins
   add  ............  Add an Ion, bower component, or addon to the project
   remove  .........  Remove an Ion, bower component, or addon from the project
   list  ...........  List Ions, bower components, or addons in the project
   info  ...........  List information about the users runtime environment
   help  ...........  Provides help for a certain command
   link  ...........  Sets your Ionic App ID for your project
   hooks  ..........  Manage your Ionic Cordova hooks
   state  ..........  Saves or restores state of your Ionic Application using the package.json file
   docs  ...........  Opens up the documentation for Ionic
   generate  .......  Generate pages and components
```

現在，我們就可以用第一個命令``start``來建立我們的項目。


```bash
ionic start growth-blog-app --v2
```

在這個過程中，它將下載Ionic 2項目的基礎項目，並執行安裝命令。

```
Creating Ionic app in folder /Users/fdhuang/repractise/growth-blog-app based on tabs project
Downloading: https://github.com/driftyco/ionic2-app-base/archive/master.zip
[=============================]  100%  0.0s
Downloading: https://github.com/driftyco/ionic2-starter-tabs/archive/master.zip
[=============================]  100%  0.0s
Installing npm packages...
```

然後到``growth-blog-app``目錄，我們會看到類似於下面的內容：

```
.
├── README.md
├── app
│   ├── app.js
│   ├── pages
│   │   ├── page1
│   │   │   ├── page1.html
│   │   │   ├── page1.js
│   │   │   └── page1.scss
│   │   ├── page2
│   │   │   ├── page2.html
│   │   │   ├── page2.js
│   │   │   └── page2.scss
│   │   ├── page3
│   │   │   ├── page3.html
│   │   │   ├── page3.js
│   │   │   └── page3.scss
│   │   └── tabs
│   │       ├── tabs.html
│   │       └── tabs.js
│   └── theme
│       ├── app.core.scss
│       ├── app.ios.scss
│       ├── app.md.scss
│       ├── app.variables.scss
│       └── app.wp.scss
├── config.xml
├── gulpfile.js
├── hooks
│   ├── README.md
│   └── after_prepare
│       └── 010_add_platform_class.js
├── ionic.config.json
├── package.json
└── www
    └── index.html
```

在這2.0版本的Ionic，頁面開始以目錄來劃分，一個頁面路徑下有自己的``html``、``js``、``scss``。

 - ``tabs``負責這些頁面間跳轉
 - ``theme``則負責系統相應樣式的修改
 - ``config.xml``帶有相應的Cordova配置
 - ``hooks``則對系統新增和編譯時進行一些預處理
 - ``ionic.config.json``則是ionic的一些相關配置選項
 - ``package.json``則存放相應的node.js的包的依賴
 - ``www``目錄用於存放出最後構建出來的內容，以及一些靜態資源

由於Angular 2.0使用的是Typescript，所以在這裡我們將用typescript進行展示，因此我們的執行命令變成~~：

```
ionic start growth-blog-app --v2 --ts
```

``--ts``表示使用的是``typescript``來建立項目，安裝的過程是一樣的，不一樣的是後面寫的程式碼。

執行相應的啟動serve命令，我們就可以開始我們的項目了：

```bash
ionic serve
```

這時候Ionic將做一些額外的事，才能啟動我們的服務，如：

 - 刪除``www/build``目錄下的檔案
 - 編譯SASS到CSS
 - 編譯檔案到HTML
 - 編譯字型
 - 等等

最後，它將啟動一個Web服務，URL為[http://localhost:8100](http://localhost:8100)

```bash
  Running 'serve:before' gulp task before serve
  [20:59:16] Starting 'clean'...
  [20:59:16] Finished 'clean' after 6.07 ms
  [20:59:16] Starting 'watch'...
  [20:59:16] Starting 'sass'...
  [20:59:16] Starting 'html'...
  [20:59:16] Starting 'fonts'...
  [20:59:16] Starting 'scripts'...
  [20:59:16] Finished 'scripts' after 43 ms
  [20:59:16] Finished 'html' after 51 ms
  [20:59:16] Finished 'fonts' after 54 ms
  [20:59:16] Finished 'sass' after 738 ms
  7.6 MB bytes written (5.62 seconds)
  [20:59:22] Finished 'watch' after 6.62 s
  [20:59:22] Starting 'serve:before'...
  [20:59:22] Finished 'serve:before' after 3.87 μs

  Running live reload server: http://localhost:35729
  Watching: www/**/*, !www/lib/**/*
  √ Running dev server:  http://localhost:8100
  Ionic server commands, enter:
    restart or r to restart the client app from the root
    goto or g and a url to have the app navigate to the given url
    consolelogs or c to enable/disable console log output
    serverlogs or s to enable/disable server log output
    quit or q to shutdown the server and exit
  ionic $
```

接著，就可以開啟相應的Web頁面，如下圖所示：

![Ionic Web預覽介面](http://growth-in-action.phodal.com/images/ionic-web-view.jpg)

### 構建應用

由於Ionic是基於Cordova的，我們需要安裝Cordova業完成後續的工作。

```bash
sudo npm install -g cordova
```

為了構建不同的平臺的應用，我們就需要新增不同的平臺，如:

```bash
ionic platform add android
```

上面的命令可以為項目新增Android平臺的支援，過程如下面的日誌所示：

```
Adding android project...
Creating Cordova project for the Android platform:
	Path: platforms/android
	Package: io.ionic.starter
	Name: V2_Test
	Activity: MainActivity
	Android target: android-23
Android project created with cordova-android@5.1.1
Running command: /Users/fdhuang/repractise/growth-blog-app/hooks/after_prepare/010_add_platform_class.js /Users/fdhuang/repractise/growth-blog-app
```

最後，再執行``run``就可以在對應的平臺上執行，如:

```
ionic run android
```

部落格列表頁
---

現在，讓我們來結合我們的部落格APP，做一個相應的展示部落格的APP。在這一步我們所要做的事情比較簡單：

 - 獲取部落格列表API
 - 渲染部落格列表

### 列表頁

在上一個章節裡我們已經有了一個部落格詳細的API，我們只需要獲取這個API並顯示即可。不過，讓我們簡單地熟悉一下顯示資料的這部分內容：

```html
<ion-navbar *navbar>
  <ion-title>部落格</ion-title>
</ion-navbar>

<ion-content class="blog-list">
  <ion-item *ngFor="#blogpost of blogposts">
    <h1 *ngIf="blogpost">
      {{blogpost.title}}
    </h1>

    <p *ngIf="blogpost">
      {{blogpost.body}}
    </p>
  </ion-item>
</ion-content>
```

上面是一個基本的詳情頁的模板，其中定義了一系列的Ionic自定義標籤，如：

 - <ion-navbar> 顯示在導航欄中的內容
 - <ion-content> 顯示APP的內容
 - <ion-item> 即將部落格展示成每一項

而從上面的內容中，我們可以看到：我們在ngFor中遍歷了blogposts，然後顯示每篇文章的標題和內容。對應的程式碼也就比較簡單了:

```javascript
import {Page} from 'ionic-angular';

@Page({
  templateUrl: 'build/pages/blog/list/index.html',
  providers: [BlogpostServices]
})
export class BlogList {
  public blogposts;

  function Object() { [native code] }() {

  }
}
```

但是我們要去哪裡獲取部落格的值呢，我們先看看改造後的BlogList的Controller：


```javascript
import {Page} from 'ionic-angular';
import {BlogpostServices} from '../../../services/BlogpostServices';

@Page({
  templateUrl: 'build/pages/blog/list/index.html',
  providers: [BlogpostServices]
})
export class BlogList {
  private blogListService;
  public blogposts;

  function Object() { [native code] }(blogpostServices:BlogpostServices) {
    this.blogListService = blogpostServices;
    this.initService();
  }

  private initService() {
    this.blogListService.getBlogpostLists().subscribe(
      data => {this.blogposts = JSON.parse(data._body);},
      err => console.log('Error: ' + JSON.stringify(err)),
      () => console.log('Get Blogpost')
    );
  }
}

```

我們初始化了一個blogListService，然後我們呼叫這個服務去獲取部落格列表。

```javascript
    this.blogListService.getBlogpostLists().subscribe(
      data => {this.blogposts = JSON.parse(data._body);},
      err => console.log('Error: ' + JSON.stringify(err)),
      () => console.log('Get Blogpost')
    );
```

當我們獲取到資料的時候，我們就解析這個資料，並將這個值賦予blogposts。如果這其中遇到什麼錯誤，就會顯示相應的錯誤資訊。

現在，讓我們建立一個獲取部落格的服務：

```
import {Inject} from 'angular2/core';
import {Http} from 'angular2/http';
import 'rxjs/add/operator/map';

export class BlogpostServices {
  private http;

  function Object() { [native code] }(@Inject(Http) http:Http) {
    this.http = http
  }

  getBlogpostLists() {
    var url = 'http://127.0.0.1:8000/api/blogpost/?format=json';
    return this.http.get(url).map(res => res);
  }
}
```

我們將通過這個API來獲取相關的資料，並將資料返回到BlogList類中。接著將更新blogposts的值，並重新渲染頁面。

### 詳情頁

在我們的部落格API中，每個內容都對應有一個id，如下所示:

```
{
    "title": "這是一個標題",
    "author": "Phodal2",
    "body": "這是一個測試的內容",
    "slug": "this-is-a-test",
    "id": 3
}
```

我們只需要訪問這個id，就可以獲取這個結果，如：[http://localhost:8000/api/blogpost/3/](http://localhost:8000/api/blogpost/3/)

因此，我們所需要做的就是：

 - 在渲染部落格列表的時候，為每一項賦予一個ID
 - 點選某一項時，將跳轉到詳情頁，並去獲取相應的API的資料，並渲染到頁面上。

好了，我們可以用ionic的生成命令來建立部落格詳情頁。

```bash
ionic g page blog-detail --ts
```

它將在app/pages目錄下，生成下面的內容：

```bash
app/pages/blog-detail/
├── blog-detail.html
├── blog-detail.ts
└── blog-detail.scss
```

我們可以遵循之前新增Django App的習慣，先新增Router。因此我們可以在``app.ts``新增新的Route:

```
const ROUTES = [
  {path: '/app/blog/:id', component: BlogDetailPage}
];

@App({
  template: '<ion-nav [root]="rootPage"></ion-nav>',
  config: {}
})
@RouteConfig(ROUTES)
export class MyApp {
  rootPage:any = TabsPage;

  function Object() { [native code] }(platform:Platform) {
    this.rootPage = TabsPage;
    this.initializeApp(platform)
  }


  private initializeApp(platform:Platform) {
    platform.ready().then(() => {
      StatusBar.styleDefault();
    });
  }
}
```

我們用RouteConfig來關聯我們的URL和App Component。

同上面的部落格列表頁面一樣，我們也可以直接新增我們的API服務。有所區別的是，我們需要依據id去獲取我們的部落格內容。

```javascript

  getBlogpostDetail(id) {
    var url = 'http://localhost:8000/api/blogpost/' + id + '?format=json';
    return this.http.get(url).map(res => res);
  }
```

和之前的部落格列表一樣，我們需要幾乎一樣的方法來獲取資料：

```javascript
import {Page, NavController, NavParams} from 'ionic-angular';
import {BlogpostServices} from "../../services/BlogpostServices";

@Page({
  templateUrl: 'build/pages/blog-detail/blog-detail.html',
  providers: [BlogpostServices]
})
export class BlogDetailPage {
  private navParams;
  private blogServices;
  private blogpost;

  function Object() { [native code] }(public nav:NavController, navParams:NavParams, blogServices:BlogpostServices) {
    this.nav = nav;
    this.navParams = navParams;
    this.blogServices = blogServices;

    this.initService();
  }

  private initService() {
    let id = this.navParams.get('id');
    this.blogServices.getBlogpostDetail(id).subscribe(
      data => {
        this.blogpost = JSON.parse(data._body);
        console.log(this.blogpost);
      },
      err => console.log('Error: ' + JSON.stringify(err)),
      () => console.log('Get Blogpost')
    );
  }
}
```

現在我們幾乎已經完成了部落格詳情頁的工作，我們可以直接通過URL來訪問部落格詳情頁：[http://localhost:8100/#/app/blog/1](http://localhost:8100/#/app/blog/1)。結果如下圖所示：

![訪問部落格詳情頁](http://growth-in-action.phodal.com/images/blog-detail-page.png)

不過，這時候我們的列表頁並沒有和詳情頁關聯到一起。我們還需要做一些額外的工作：

 - 在列表頁的每一項中新增對點選事件的處理

在我們的模板頁裡ion-item裡新增一個click事件，這個事件將呼叫navigate函數，並把部落格id傳到這個函數裡。

```html
<ion-item *ngFor="#blogpost of blogposts" (click)="navigate(blogpost.id)">
  <h1 *ngIf="blogpost">
    {{blogpost.title}}
  </h1>

  <p *ngIf="blogpost">
    {{blogpost.body}}
  </p>
</ion-item>
```

隨後在我們的部落格詳情頁的初始化裡，我們要初始化一個NavController:

```javscript
function Object() { [native code] }(nav: NavController, blogpostServices:BlogpostServices) {
    this.blogListService = blogpostServices;
    this.nav = nav;
    this.initService();
  }
```

接著，在navigate裡我們只需要將BlogDetailPage頁面及參數push給navController，並交由它來渲染頁面。

```javascript
navigate(id){
  this.nav.push(BlogDetailPage, {
    id: id
  });
}
```

現在，我們可以試試從首頁跳轉到這個部落格詳情頁。

Profile
---

現在，我們要做一個更有意思的東西了。不過這個內容是為後面的建立文章提供一個技術基礎。在使用者授權這一部分，我們使用不同的技術來實現，如Cookies、HTTP基本認證等等。而在手機端繼續Cookie來進行使用者授權，不是一件簡單的事。因此我們就需要JSON Web Tokens，這是一種基於token 的認證方案。

###Json Web Tokens

同樣，為了實現這部分功能，我們仍然可以使用其他框架來幫助我們完成基礎功能。這裡我們就用到了一個名為``djangorestframework-jwt``的庫，從它的名字上我們就可以知道，它就是基於Django REST Framework之上的JWT實現。還是繼續使用pip來安裝這個庫，記得把它新增到``requirements.txt``中。

```
pip install djangorestframework-jwt
```

接著，我們需要在我們的URL中配置用於獲取token的API即可使用。

```
urlpatterns = patterns(
    '',
    # ...

    url(r'^api-token-auth/', 'rest_framework_jwt.views.obtain_jwt_token'),
)
```

在我們完成了上面的步驟之後，我們可以用``curl``命令或者Chrome瀏覽器的Postman來做測試：

 - 向伺服器傳送我們的使用者名稱和密碼，獲取對應的Token。

如下是``curl``建立的請求，在這其中我們傳送了我們的使用者和密碼。

```
curl -H "Content-Type: application/json" -X POST -d '{"username":"admin","password":"root"}' http://localhost:8000/api-token-auth
```

然後服務端給我們返回了對應的Token，它可以用於後面的建立文章、獲取使用者資訊等等的功能。下面是一個Token的示例：

```
{"token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJlbWFpbCI6ImhAcGhvZGFsLmNvbSIsInVzZXJfaWQiOjIsInVzZXJuYW1lIjoiYWRtaW4iLCJleHAiOjE0NjQ4NzQ1MDZ9.B5LEeIlGDTGggD6dh9akGRKx0Hk09wjylQRLas6kjGM"}
```

### 登入表單

現在，我們要先做的一件事就是，建立一個用於登入的表單。

```
<form #loginCreds="ngForm" (ngSubmit)="login(loginCreds.value)">
  <ion-item>
    <ion-label>Username</ion-label>
    <ion-input type="text" ngControl="username"></ion-input>
  </ion-item>

  <ion-item>
    <ion-label>Password</ion-label>
    <ion-input type="password" ngControl="password"></ion-input>
  </ion-item>

  <div padding>
    <button block type="submit">登入</button>
  </div>
</form>
```

我們建立一個名為``loginCreds``的ngForm，在我們提交的時候我們就呼叫login方法，並把其中的值（username、password）傳過去。而在我們的程式碼裡，我們所要做的就是和上面一樣將資料post到之前的Auth API的地址：

```
function Object() { [native code] }(http: Http, nav:NavController) {
  this.nav = nav;
  this.http = http;
  this.local.get('id_token').then(
    (data) => {
      this.user = this.jwtHelper.decodeToken(data).username;
    }
  );
}

login(credentials) {
  this.contentHeader = new Headers({"Content-Type": "application/json"});
  this.http.post(this.LOGIN_URL, JSON.stringify(credentials), {headers: this.contentHeader})
    .map(res => res.json())
    .subscribe(
      data => this.authSuccess(data.token),
      err => console.log(err)
    );
}

authSuccess(token) {
  this.local.set('id_token', token);
  this.user = this.jwtHelper.decodeToken(token).username;
}
```

在我們成功的獲取到Token的時候，儲存這個Token，並呼叫jwtHelper來解碼Token，並從中獲取我們的username。

同時，對於我們來說要登出就是一件容易的，刪除這個token，將使用者名稱清空。
```
logout() {
  this.local.remove('id_token');
  this.user = null;
}
```

### Profile

當我們獲取到這個Token，我們也可以順便獲取使用者的使用者名稱、郵件等等的資訊給使用者。我們所要做的就是再獲取一次API，但是在獲取這次API的時候，我們需要上傳我們的Token。因此我們需要一個簡單的AuthHelper來幫助我們。

####AuthHttp

雖然我們要做的僅僅只是在我們的Header中，新增一個欄位，它的值就是Token的值。但是這部分的邏輯交給Angular2-JWT來做可能會好一點，它提供了一個AuthHTTP方法可以讓每次請求都帶上這個Header。首先我們需要安裝這個庫：

```
npm install angular2-jwt
```

然後在我們``app.ts``中新增這個provider，並指明它的header字首是JWT。

```javascript
@App({
  template: '<ion-nav [root]="rootPage"></ion-nav>',
  config: {}, // http://ionicframework.com/docs/v2/api/config/Config/
  providers: [
    provide(AuthHttp, {
      useFactory: (http) => {
        var authConfig = new AuthConfig({headerPrefix: 'JWT'});
        return new AuthHttp(authConfig, http);
      },
      deps: [Http]
    })
  ]
})
```

現在，我們就可以用和Http一樣的方式去獲取使用者資訊。

#### 獲取使用者資訊

現在我們所需要做的就是發出我們的API去獲取使用者的資訊：

```javascript
authSuccess(token) {
  this.local.set('id_token', token);
  this.user = this.jwtHelper.decodeToken(token).username;
  let params:URLSearchParams = new URLSearchParams();
  params.set('username', this.user);

  this.authHttp.request('http://localhost:8000/api/user/', {
      search: params
    })
    .map(res => res.text())
    .subscribe(
      data => this.local.set('user_info', JSON.stringify(JSON.parse(data)[0])),
      err => console.log(err)
    );
}
```

只是我們的API似乎還不支援這樣的功能。它的實現方式和我們之前的AutoComplete是一樣的，也是搜尋使用者名稱：

```python
def list(self, request):
    search_param = self.request.query_params.get('username', None)
    if search_param is not None:
        queryset = User.objects.filter(username__contains=search_param)

    serializer = UserSerializer(queryset, many=True)
    return Response(serializer.data)
```


然後再顯示這些資料：

```
<div *ngIf="auth.authenticated()">
  <div padding>
    <h1>Welcome, {{ user }}</h1>
    <h2 *ngIf="user_info">Last login {{user_info.last_login}}</h2>
    <button block (click)="logout()">Logout</button>
  </div>
</div>
```

不過由於我們Django自帶的使用者管理模組只有這點資訊，我們也就只能顯示這些資訊了。下一步，我們就可以實現在我們的APP裡去建立部落格。

建立部落格
---

在我們開始在APP端實現這個功能之前，我們先要實現一個高階點的使用者授權管理——即只有使用者登入或者使用者的請求中帶有Token的時候，我們才能建立部落格。於是在這裡我們所要做的就是實現一個``IsAuthenticatedOrReadOnly``，來判斷使用者是否有許可權，如果沒有的話，那麼只讓使用者看到部落格的內容。程式碼如下所示：

```python
SAFE_METHODS = ['GET', 'HEAD', 'OPTIONS']

class IsAuthenticatedOrReadOnly(BasePermission):
    """
    The request is authenticated as a user, or is a read-only request.
    """

    def has_permission(self, request, view):
        if (request.method in SAFE_METHODS or
            request.user and
            request.user.is_authenticated()):
            return True
        return False

class BlogpsotSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Blogpost
        fields = ('title', 'author', 'body', 'slug', 'id')


# ViewSets define the view behavior.
class BlogpostSet(viewsets.ModelViewSet):
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
    queryset = Blogpost.objects.all()
    serializer_class = BlogpsotSerializer

```

接著，我們可以建立一個Modal來做這個工作。對於我們的部落格表單來說，和登入沒有太大的區別。

```
  <form #blogpostForm="ngForm" (ngSubmit)="create(blogpostForm.value)">
    <ion-item>
      <ion-label>標題</ion-label>
      <ion-input type="text" ngControl="title"></ion-input>
    </ion-item>

    <ion-item>
      <ion-label>作者</ion-label>
      <ion-input type="text" ngControl="author"></ion-input>
    </ion-item>

    <ion-item>
      <ion-label>URL</ion-label>
      <ion-input type="text" ngControl="slug"></ion-input>
    </ion-item>

    <ion-item>
      <ion-label>內容</ion-label>
      <ion-textarea type="text" ngControl="body"></ion-textarea>
    </ion-item>

    <div padding>
      <button block type="submit">建立</button>
    </div>

  </form>
```

稍有不同的是在我們的標題欄裡會有一個關閉按鈕。

```
<ion-toolbar>
  <ion-title>
    建立部落格
  </ion-title>
  <ion-buttons start>
    <button (click)="close()">
      <span primary showWhen="ios">取消</span>
      <ion-icon name="md-close" showWhen="android,windows"></ion-icon>
    </button>
  </ion-buttons>
</ion-toolbar>
```

對於我們的實現程式碼來說，也是類似的，除了我們在發表成功的時候做的事情不一樣——關閉這個Modal。

```
  close() {
    this.viewCtrl.dismiss();
  }

  create(value) {
    this.contentHeader = new Headers({"Content-Type": "application/json"});
    this.authHttp.post('http://127.0.0.1:8000/api/blogpost/', JSON.stringify(value), {headers: this.contentHeader})
      .map(res => res.json())
      .subscribe(
        data => this.postSuccess(data),
        err => console.log(err)
      );
  }

  postSuccess(data) {
    this.close()
  }
```

同時，我們需要在我們的首頁裡新增這樣的一個入口。

```
  <button fab fab-bottom fab-right (click)="createBlog()" calm>
    <ion-icon name="add"></ion-icon>
  </button>
```

以及它的處理邏輯：

```
  createBlog() {
    let modal = Modal.create(CreateBlogModal);
    this.nav.present(modal)
  }
```
