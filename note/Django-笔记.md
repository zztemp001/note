## 利用继承自身来实现递归无线级分类
```python
parent=models.ForeignKey('self',null=True,blank=True,related_name="child",verbose_name="父类标识")
# blank=Trun 是关键，否则第一个数据填不进去
```

## 一个Product的model，每个Product有数张相关的图片
```python
class Product(models.Model): 
    name = models.CharField(max_length = 50) 

class Photo(models.Model): 
    product = models.ForeignKey(Product) 
    image = models.models.ImageField(upload_to='photos') 

#这样两个model建立起了一对多的关系，一个Product有无数个Photo都行。
```

## settings.py 中的 TIME_ZONE设置
```
TIME_ZONE = 'Asia/Shanghai'
```

## 安装Django_Debug_Toolbar
```python
#1、下载django_debug_toolbar：https://github.com/django-debug-toolbar/django-debug-toolbar
#2、将压缩包中的debug_toolbar文件夹拷贝至项目中
#3、修改项目的settings.py文件
MIDDLEWARE_CLASSES = (
    'debug_toolbar.middleware.DebugToolbarMiddleware',
)

INSTALLED_APPS = (
    'yeekaa.debug_toolbar',
)

INTERNAL_IPS = ('127.0.0.1',)

DEBUG_TOOLBAR_PANELS = (
    'debug_toolbar.panels.version.VersionDebugPanel',
    'debug_toolbar.panels.timer.TimerDebugPanel',
    'debug_toolbar.panels.settings_vars.SettingsVarsDebugPanel',
    'debug_toolbar.panels.headers.HeaderDebugPanel',
    'debug_toolbar.panels.request_vars.RequestVarsDebugPanel',
    'debug_toolbar.panels.template.TemplateDebugPanel',
    'debug_toolbar.panels.sql.SQLDebugPanel',
    'debug_toolbar.panels.signals.SignalDebugPanel',
    'debug_toolbar.panels.logger.LoggingPanel',
)

#4、根据需要安装第三方的面板插件：https://github.com/django-debug-toolbar/django-debug-toolbar/wiki/3rd-Party-Panels
```