配置管理
===

local settings
---

作為一個開源項目，我們在這方面做得並不是特別好——當然是有意如此的。不過，這裡我們還是做一些簡單的介紹。對於我們的項目來說，我們需要一些額外的配置，如我們的資料庫中的``DATABASES``、``DEFAULT_AUTHENTICATION_CLASSES``、``CORS_ORIGIN_ALLOW_ALL``、``SECRET_KEY``應該在不同的環境中都有不同的配置。

我們可以一個建立``local_settings.py``，在裡面放置一些關鍵的伺服器相關的配置，如:

```python

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = 'hpi!zb8!(j%40)r55@+_5k*^9qcjf9sx0o_it*jlp3=x9^2ak@'

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True

TEMPLATE_DEBUG = True

# Database
# https://docs.djangoproject.com/en/1.7/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'db.sqlite3',
    }
}

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': (

    ),
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
    ),
}

CORS_ORIGIN_ALLOW_ALL = True
```

接著，我們只需要在我們的主``settiings.py``中引用即可。
