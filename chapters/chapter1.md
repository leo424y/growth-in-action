深入淺出Django
===

Django簡介
---

Django是一個高階的Python Web開發框架，它的目標是使得開發複雜的、資料庫驅動的網站變得更加簡單。

由於Django最初是被開發來用於管理勞倫斯出版集團旗下的一些以新聞內容為主的網站的。所以，我們可以發現在使用Django的很多網站裡，都是用於作為CMS（內容管理系統）來使用的。使用Django的一些比較知名的網站如下圖所示：

![使用Django的網站](http://growth-in-action.phodal.com/images/who-use-django.jpg)

Django是一個MTV框架，其架構模板看上去與傳統的MVC架構並沒有太大的區別。其對比如下表所示：

傳統的MVC架構| Django 架構
-----------|-----------
Model      | Model(Data Access Logic)
View       |Template(Presentation Logic)
View       | View(Business Logic)
Controller | Django itself

在Django中View只用來描述你要看到的內容，Template才是最後用於顯示的內容。而在MVC架構中，這隻相當於是View層。它的核心包含下面的四部分：

 - 一個 物件關係對映，作為資料模型和關係性資料庫間的媒介（Model層）；
 - 一個基於正規表示式的URL分發器（即MVC中的Controller)；
 - 一個用於處理HTTP請求的系統，含web模板系統(View層)；

其核心框架還包含：

 - 一個輕量級的、獨立的Web伺服器，只用於開發和測試。
 - 一個表單序列化及驗證系統，用於將HTML表單轉換成適用於資料庫儲存的資料。
 - 一個快取框架，並且可以從幾種快取方式中選擇。
 - 中介軟體支援，能對請求處理的各個階段進行處理。
 - 內建的分發系統允許應用程式中的元件採用預定義的訊號進行相互間的通訊。
 - 一個序列化系統，能夠生成或讀取採用XML或JSON表示的Django模型例項。
 - 一個用於擴充套件模板引擎的能力的系統。

### Django應用架構

Django的每一個模組在內部都稱之為APP，在每個APP裡都有自己的三層結構。如下圖所示：

![Django 應用架構](http://growth-in-action.phodal.com/images/django_app_arch.jpg)

這樣做不僅可以在開發的時候更容易理解系統，而且可以提高程式碼的可複用性——因為每一個APP都是獨立的應用，在下次使用時我們只需要簡單的複製和貼上。

說了這麼多，還不如從一個hello,world開始。

Django hello,world
---

###安裝Django

安裝Django之前，我們可以用virtualenv工具來建立一個虛擬的Python執行環境。環境問題是一個很複雜的問題，在我們使用Python的過程中，我們會不斷地安裝一些庫，而這些庫可能會有不同的版本。並且在安裝Python庫的過程中，我們會遇到許可權問題——即我們需要超級使用者的許可權才能將庫安裝到系統的環境之下。隨後在這個軟體的生涯中，我們還需要保證這個項目所依賴的模組不會發生變動。而這些都是很棘手的一些事，這時候我們就需要建立一個虛擬的執行環境，而virtualenv就是這樣的一個工具。

####virtualenv

安裝Python包我們需要用到pip命令，它是Python語言中的一個包管理工具。如果你沒有安裝的話，可以使用下面的命令來安裝：

```
curl https://bootstrap.pypa.io/get-pip.py | python
```

在不同的Python環境中，我們可能需要使用不同的pip，如下所示是筆者使用的Python3的pip命令pip3

```
$ pip3 install virtualenv
```

如果是Python2.7的話，對應會有:

```
$ pip install virtualenv
```

需要注意的是這將會安裝到Python所在的目錄，如我的目錄是:

```
$ /usr/local/bin/virtualenv
```

有的可能會是：

```
$ /usr/local/share/python3/virtualenv
```

在建立我們的這個虛擬環境之前，我們可以建立一個儲存所有virtualenv的目錄：

```
$ mkdir somewhere/virtualenvs
```

現在，我們就可以建立一個新的虛擬環境：

```
$ virtualenv somewhere/virtualenvs/<project-name> --no-site-packages
```

如果你想使用不同的Python版本的話，那麼需要指定Python版本的路徑

```
$ virtualenv --distribute -p /usr/local/bin/python3.3 somewhere/virtualenvs/<project-name>
```

通過到相應的目錄下執行啟用就可以使用這個虛擬環境了：

```
$ cd somewhere/virtualenvs/<project-name>/bin
$ source activate
```

停止使用只需要執行下面的命令即可：

```
$ deactivate
```

####安裝Django

準備了這麼久我們終於要開始安裝Django了，執行：

```
$ pip install django
```

開始下最新版本的Django，如下所示：

```
Collecting django
  Downloading Django-1.9.4-py2.py3-none-any.whl (6.6MB)
    94% |██████████████████████████████▎ | 6.2MB 251kB/s eta 0:00:02
```

等下載完後，就會開始安裝Django。安裝完後，我們就可以使用Django自帶的django-admin命令。django-admin是Django自帶的一個管理任務的命令列工具。

通過這個命令，我們不僅僅可以用它來建立項目、建立app、執行服務、資料庫遷移，還可以執行各種SQL工具等等。django-admin用法如下：

```
$ django-admin <command> [options]
```

下面是django-admin自帶的一些命令：

```
[django]
    check
    compilemessages
    createcachetable
    dbshell
    diffsettings
    dumpdata
    flush
    inspectdb
    loaddata
    makemessages
    makemigrations
    migrate
    runfcgi
    runserver
    shell
    sql
    sqlall
    sqlclear
    sqlcustom
    sqldropindexes
    sqlflush
    sqlindexes
    sqlinitialdata
    sqlmigrate
    sqlsequencereset
    squashmigrations
    startapp
    startproject
    syncdb
    test
    testserver
    validate
```

現在，讓我們來看看這個強大的工具。

###建立項目

在這些命令中startproject可以用於建立項目，在這裡我們的項目名是blog，那麼我們的命令如下：

$ django-admin startproject blog

這個命令將建立下面的檔案內容，而這些是Django項目的一些必須檔案。

```
.
├── blog
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

blog目錄對應的就是blog這個項目，將會放置這個項目的一些相關配置：

1. settings.py包含了這個項目的相關配置。如資料庫環境、啟用的外掛等等。
2. urls.py即URL Dispatcher的配置，指明瞭某個URL應該指向某個函數來處理。
3. wsgi.py用於部署。WSGI（Python Web Server Gateway Interface，Web伺服器閘道器介面）是為Python語言定義的Web伺服器和Web應用程式或框架之間的一種簡單而通用的介面。
4. \_\_init\_\_.py指明瞭這是一個Python模組。

manage.py 會在每個Django項目中自動生成，它可以和django-admin做類似的事。如我們可以用manage.py來啟動測試環境的伺服器：

$ python manage.py runserver

```
Performing system checks...

System check identified no issues (0 silenced).

You have unapplied migrations; your app may not work properly until they are applied.
Run 'python manage.py migrate' to apply them.

March 24, 2016 - 03:07:34
Django version 1.9.4, using settings 'blog.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
Not Found: /
[24/Mar/2016 03:07:35] "GET / HTTP/1.1" 200 1767
Not Found: /favicon.ico
[24/Mar/2016 03:07:36] "GET /favicon.ico HTTP/1.1" 404 1934
```

現在，我們只需要在瀏覽器中開啟[http://127.0.0.1:8000/](http://127.0.0.1:8000/)，便可以訪問我們的應用程式。

###Django後臺

Django很適合CMS的另外一個原因，就是它自帶了一個後臺管理系統。為了啟用這個後臺管理系統，我們需要配置我們的資料庫，並建立相應的超級使用者。如下所示的是settings.py中的預設資料庫配置：

```
# Database
# https://docs.djangoproject.com/en/1.7/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

上面的配置中我們使用的是SQLite3作為資料庫，並使用了當前目錄下的``db.sqlite3``作為資料庫檔案。Django內建支援下面的一些資料庫：

```
'django.db.backends.postgresql_psycopg2'
'django.db.backends.mysql'
'django.db.backends.sqlite3'
'django.db.backends.oracle'
```

如果我們想使用別的資料庫，可以在網上尋找相應的解決方案，如用於支援使用MongoDB的django-nonrel項目。不同的資料庫有不同的配置，如下所示的是使用PostgreSQL的配置。

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

接著，我們就可以執行資料庫遷移，只需要執行相應的指令碼即可：

$ python manage.py migrate

```
Operations to perform:
  Apply all migrations: sessions, admin, auth, contenttypes
Running migrations:
  Rendering model states... DONE
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying sessions.0001_initial... OK
(growth-django)
```

在上面的過程中，我們會建立相應的資料庫模型，並依據遷移指令碼來建立一些相應的資料，如預設的配置等等。

最後，我們可以建立一個相應的超級使用者來登陸後臺。

$ python manage.py createsuperuser

```
Username (leave blank to use 'fdhuang'): root
Email address: h@phodal.com
Password:
Password (again):
Superuser created successfully.
```

輸入相應的使用者名稱和密碼，即可完成建立。然後訪問 [http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin)，輸入上面的使用者名稱和密碼就可以來到後臺：

![Django後臺](http://growth-in-action.phodal.com/images/django-backend.jpg)

### 第一次提交

在建立完應用後，我們就可以進行第一次提交，通常初次提交的提交資訊(commit message)是``init project``。如果在那之前，你沒有執行``git init``來初始化git的話，那麼我們就需要去執行這個命令。

```bash
git init
```

它將返回類似於下面的結果

```bash
Initialized empty Git repository in /Users/fdhuang/test/helloworld/.git/
```

即初始化了一個空的Git項目，然後我們就可以執行``add``來新增上面的內容：

```bash
git add .
```

需要注意的是上面的資料庫檔案不應該新增到項目裡，所以我們應該執行reset命令來重置這個狀態：

```bash
git reset db.sqlite3
```

這時我們會將其變成下面的狀態：

![第一次提交前的reset](http://growth-in-action.phodal.com/images/first-commit.png)

上面的綠色檔案代表這幾個檔案都被新增了進去，藍色則代表未新增的檔案。為了避免手誤產生一些問題，我們需要新增一個名為``.gitignore``檔案用於將一些檔名加入忽略名單，如下是常用的python項目的``.gitignore``檔案中的內容：

```
*.pyc
*.db
*.sqlite3
```

當我們新增完這個檔案，git就會識別這個檔案，並忽略原來的那些檔案，如下圖所示：

![新增完gitignore檔案後的效果](http://growth-in-action.phodal.com/images/git-ignore.png)

我們只需要新增這個檔案即可：

```bash
git add .gitignore
```

如果你之前已經不小心新增了一些不應該新增的檔案，那麼可以執行下面的命令來重置其狀態：

```bash
git reset .
```

然後再執行新增命令。

最後，我們就可以在本地提交我們的程式碼了:

```
git commit -m "init project"
```

如果你是將程式碼託管在GitHub上的話，那麼你就可以執行``git push``來將程式碼提交到伺服器上。
