## 常用的 Django 配置


### 语言&时区设置

> https://docs.djangoproject.com/en/4.1/topics/i18n/

#### 外国

```python
LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'
USE_I18N = True
USE_TZ = True
```

#### 中国

```python
LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
USE_I18N = True
USE_TZ = True
```


### 数据库配置

#### sqlite3

> https://docs.djangoproject.com/en/4.1/ref/settings/#databases

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

#### mysql

```python
DATABASES = { 
    'default': 
    { 
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'db_name',
        'HOST': '127.0.0.1', 
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': '123456',
    }  
}
```


### 模板和静态文件路径设置

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR, 'template'],
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

import os
STATIC_URL = 'static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]
```

### 允许所有 IP 访问本网站

```python
ALLOWED_HOSTS = ['*']
```

### 允许网页被 iframe 引用

```python
X_FRAME_OPTIONS = 'ALLOWALL url'
```