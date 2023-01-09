## 常用的 Django 命令


### 新建一个 django 项目

```bash
django-admin startproject djangoproject
```

### 新建一个 django 模型
```bash
django-admin startapp djangoapp
```


### 运行 django 项目

```bash
python manage.py runserver
```

### 数据库迁移

```bash
python manage.py makemigrations
python manage.py migrate
```

### 创建超级管理员

```bash
python manage.py create superuser
```