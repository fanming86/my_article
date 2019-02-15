

### django-crontab安装：
```bash
pip install django-crontab
```

### 将django-crontab加入setting.py 

只需要将django-crontab加入到settings.py的INSTALLED_APPS即可。如下代码：
```python
INSTALLED_APPS = (
'django_crontab',
...
)
```

### settings.py中加入了django-crontab的命令：

== 计划任务，每天爬取图片，只能在Linux中使用 ==
```python
CRONJOBS = [
# 表示每天1：01执行
('01 1 * * *', 'myApp1.bingPhoto.main')
]
```

- 应用.python程序.类
- bingPhoto为myApp1应用下的一个py文件
- main为bingPhoto文件中的函数名

### 命令

查看一下系统中已有的计划任务
```bash
python manage.py crontab show 
```

如果要移除所有的任务，则运行
```bash
python manage.py crontab remove
```
当你修改了任务，需要再次运行
```bash
python manage.py crontab add
```
