自動化測試與持續整合
===

在上一章最後，我們寫的測試可以算得上是單元測試，接著我們可以寫一些自動化測試。

編寫自動化測試
---

接著我們就可以用Selenium來做自動化測試。這是ThoughtWorks出品的一個強大的基於瀏覽器的開源自動化測試工具，它通常用來編寫Web 應用的自動化測試。

### Selenium與第一個UI測試

先讓我們來看一個自動化測試的例子：

```python
from django.test import LiveServerTestCase
from selenium import webdriver

class HomepageTestCase(LiveServerTestCase):
    def setUp(self):
        self.selenium = webdriver.Firefox()
        self.selenium.maximize_window()
        super(HomepageTestCase, self).setUp()

    def tearDown(self):
        self.selenium.quit()
        super(HomepageTestCase, self).tearDown()

    def test_visit_homepage(self):
        self.selenium.get(
            '%s%s' % (self.live_server_url,  "/")
        )

        self.assertIn("Welcome to my blog", self.selenium.title)
```

在setUp——即開始的時候，我們會用selenium啟動一個Firefox瀏覽器的程序，並執行maximize_window來將視窗最大化。在tearDown——即結束的時候，我們就會關閉這個瀏覽器的程序。我們的主要測試程式碼就在``test_visit_homepage``這個方法裡，我們在裡面訪問首頁，並判斷標題是不是``Welcome to my blog``。

執行上面的測試就會啟動一個瀏覽器，並且會在瀏覽器上進行相應的操作。如下圖所示：

![Selenium Demo](http://growth-in-action.phodal.com/images/selenium-demo.jpg)

這時你可能會產生一些疑惑，這些內容我們不是已經測試過了麼？兩者從測試看是差不多的，但是從流程上看來說並不是如些。下圖是頁面渲染的時間線：

![頁面渲染時間線](http://growth-in-action.phodal.com/images/page-timing-overview.png)

請求從瀏覽器傳到伺服器要有一系列的過程，如重定向、快取、DNS等等，最後直至返回對應的Response。我們用Django的測試框架只能實現到這一步，隨後頁面請請求對應的靜態資料，再對頁面進行渲染，在這個過程中頁面的內容會發生一些變化。

為了避免頁面的內容被替換掉，那麼我們就需要對這部分內容進行測試。

如下的程式碼也是可以用於測試頁面內容的程式碼：

```python
class BlogpostDetailCase(LiveServerTestCase):
    def setUp(self):
        Blogpost.objects.create(
            title='hello',
            author='admin',
            slug='this_is_a_test',
            body='This is a blog',
            posted=datetime.now
        )

        self.selenium = webdriver.Firefox()
        self.selenium.maximize_window()
        super(BlogpostDetailCase, self).setUp()

    def tearDown(self):
        self.selenium.quit()
        super(BlogpostDetailCase, self).tearDown()

    def test_visit_blog_post(self):
        self.selenium.get(
            '%s%s' % (self.live_server_url,  "/blog/this_is_a_test.html")
        )

        self.assertIn("hello", self.selenium.title)
```

雖然在這裡我們要測試的只是頁面的標題，而實際上我們要測試的是頁面的元素是否存在。

同樣的，我們也可以對部落格的內容進行測試。這些稍有不同的是，我們更多地是要測試使用者的行為，如我們在首頁點選某個連結，那麼我應該中轉到對應的部落格詳情頁，如下程式碼所示：

```python
class BlogpostFromHomepageCase(LiveServerTestCase):
    def setUp(self):
        Blogpost.objects.create(
            title='hello',
            author='admin',
            slug='this_is_a_test',
            body='This is a blog',
            posted=datetime.now
        )

        self.selenium = webdriver.Firefox()
        self.selenium.maximize_window()
        super(BlogpostFromHomepageCase, self).setUp()

    def tearDown(self):
        self.selenium.quit()
        super(BlogpostFromHomepageCase, self).tearDown()

    def test_visit_blog_post(self):
        self.selenium.get(
            '%s%s' % (self.live_server_url,  "/")
        )

        self.selenium.find_element_by_link_text("hello").click()
        self.assertIn("hello", self.selenium.title)
```

需要注意的是，如果我們的單元測試如果可以測試到頁面的內容——即沒有使用JavaScript對頁面的內容進行修改，那麼我們應該使用單元測試即可。如測試金字塔所說，越底層的測試越快。

在我們編寫完這些測試後，我們就可以搭建好相應的持續整合來執行這些測試了。

搭建持續整合
---

這裡我們將使用Jenkins來完成這部分的工作，它是一個用Java編寫的開源的持續整合工具。

> 它提供了軟體開發的持續整合服務。它執行在Servlet容器中（例如Apache Tomcat）。它支援軟體配置管理（SCM）工具（包括AccuRev SCM、CVS、Subversion、Git、Perforce、Clearcase和和RTC），可以執行基於Apache Ant和Apache Maven的項目，以及任意的Shell指令碼和Windows批處理命令。

要使用Jenkins，只需要從Jenkins的主頁上([https://jenkins.io/](https://jenkins.io/))下載最新的 jenkins.war檔案。然後執行

```bash
java -jar jenkins.war
```

便可以啟動:

```bash
Running from: /Users/fdhuang/repractise/growth-ci/jenkins.war
webroot: $user.home/.jenkins
May 12, 2016 10:55:18 PM org.eclipse.jetty.util.log.JavaUtilLog info
INFO: Logging initialized @489ms
May 12, 2016 10:55:18 PM winstone.Logger logInternal
INFO: Beginning extraction from war file
May 12, 2016 10:55:20 PM org.eclipse.jetty.util.log.JavaUtilLog warn
WARNING: Empty contextPath
May 12, 2016 10:55:20 PM org.eclipse.jetty.util.log.JavaUtilLog info
INFO: jetty-9.2.z-SNAPSHOT
May 12, 2016 10:55:20 PM org.eclipse.jetty.util.log.JavaUtilLog info
INFO: NO JSP Support for /, did not find org.eclipse.jetty.jsp.JettyJspServlet
Jenkins home directory: /Users/fdhuang/.jenkins found at: $user.home/.jenkins
May 12, 2016 10:55:21 PM org.eclipse.jetty.util.log.JavaUtilLog info
INFO: Started w.@68c34b0{/,file:/Users/fdhuang/.jenkins/war/,AVAILABLE}{/Users/fdhuang/.jenkins/war}
May 12, 2016 10:55:21 PM org.eclipse.jetty.util.log.JavaUtilLog info
INFO: Started ServerConnector@733a9ac6{HTTP/1.1}{0.0.0.0:8080}
```

接著，開啟[http://0.0.0.0:8080/](http://0.0.0.0:8080/)就可以進行後續的安裝，如下圖所示：

![Jenkins安裝過程](http://growth-in-action.phodal.com/images/jenkins-install.jpg)

慢慢等其安裝完成：

![Jenkins安裝完成](http://growth-in-action.phodal.com/images/jenkins-getting-started.jpg)

等安裝完成後，我們就可以開始使用Jenkins來建立我們的任務了。

### Jenkins建立任務

在首頁，我們會看到“開始建立一個新任務”的提示，點選它。

源碼管理中選擇Git，並填入我們程式碼的地址：

```
[https://github.com/phodal/growth-in-action-python-code](https://github.com/phodal/growth-in-action-python-code)
```

如下圖所示:

![Jenkins設計Repo](http://growth-in-action.phodal.com/images/jenkins-repo-setup.jpg)

然後就是構建觸發器，一共有五種類型的觸發器，意思也很容易理解：

 - 觸發遠端構建 (例如,使用指令碼)
 - Build after other projects are built
 - Build periodically
 - Build when a change is pushed to GitHub
 - Poll SCM

在這裡，我們要使用的是GitHub這個，它的原理是:

> This job will be triggered if jenkins will receive PUSH GitHub hook from repo defined in scm section

即Jenkins在監聽GitHub上對應的PUSH hook，當發生程式碼提交時，就會執行我們的測試。

由於，我們暫時不需要一些特殊的``構建環境``配置，我們就可以將這個放空。接著，我們就可以配置``構建``了。

### 建立shell

在這裡我們需要新增的構建步驟是：``execute shell``，先讓我們寫一個簡單的安裝依賴的shell

```bash
virtualenv --distribute -p /usr/local/bin/python3.5 growth-django
source growth-django/bin/activate
pip install -r requirements.txt
```

然後在儲存後，我們可以嘗試立即構建這個項目：

![控制檯輸出](http://growth-in-action.phodal.com/images/build-console-ouput.jpg)

在編寫shell的過程中，我們要經過一些嘗試，在這其中會經歷一些失敗的情形——即使是大部分有相關經驗的程式設計師。如下圖就是一次編寫構建指令碼引起的構建失敗的例子：

![Jenkins失敗的構建](http://growth-in-action.phodal.com/images/jenkins-failure-setup.jpg)

最後，我們就得到下面的一個shell指令碼，我們就可以將其變成相應的執行CI的指令碼。以便於它可以在其他環境中使用：

```bash
#!/usr/bin/env bash
virtualenv --distribute -p /usr/local/bin/python3.5 growth-django
source growth-django/bin/activate
pip install -r requirements.txt
python manage.py test
python manage.py test test
```

記得給你的shell檔案，加上執行的標誌：

```
chmod u+x ./scripts/ci.sh
```

最後，我們就可以修改CI上相應的構建環境的配置。
