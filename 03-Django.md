# Django

Web应用：在浏览器中可以直接使用的应用程序

Python：为浏览器网页提供动态内容

* 为什么要用Python写Web应用？

  Python简单优雅，开发效率极高。（可以用快速产品占领市场）

* Django框架

  * 功能最为完整的Web框架
  * 源于真实商业的框架
  * 生态圈最繁荣，不用重复造轮子



## MVC架构模式

Model - 数据

View - 视图（数据的展示） T

Controller - 控制器 （连接数据和视图的桥梁） 业务逻辑处理 V



* 数据和显示的解耦合（分离）
  * 同一组数据可以呈现位不同的视图；
  * 同一个视图可以加载不同的数据；



## django安装配置



### 安装django-admin工具

```shell
pip install django==2.2.14

django-admin --version
```

  

### 运行djagno项目

~~~shell
python manage.py runserver
~~~



###  更改Django语言

在settings.py中，修改

~~~python
LANGUAGE_CODE = 'zh-hans'

TIME_ZONE = 'Asia/Shanghai'
~~~



### 创建应用

~~~shell
python manage.py startapp demo

django-admin startapp demo
~~~



### 视图函数

* HTTP,请求响应式协议
  * 请求：请求行，请求头、空行、消息体；
  * 响应：响应行，响应头、空行、消息体；
* 视图函数的参数是HTTPRequset对象，该对象封装了和HTTP请求相关的所有信息。
* 视图函数的返回值是HTTPResponse对象，该对象封装了和HTTP响应相关的所有信息。

~~~python
def index(request: HttpRequest) -> HttpResponse:
    return HttpResponse('hi')
~~~







### template配置

| 指令   | 释义       |
| :----- | ---------- |
| {{}}   | 模板占位符 |
| {%  %} | 模板指令   |
|        |            |

* 模板页配置  settings.py
  * [os.path.JOIN(BASE_DIR, templates)]
  * [BASE_DIR / 'templates']

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates']
        ,
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
```



### render()

> 渲染模板页的函数

返回参数request、模板页的名字、字典（页面需要的数据）

```python
return render(request, 'index.html', {})
```



###  redirect()

重定向

~~~python
return redirect('/')
~~~



### 修改运行端口

~~~shell
python manage.py runserver 127.0.0.1:8888
~~~





## 数据库配置



### MySQL

安装mysqlclient

~~~shell
pip install mysqlclient
~~~





### pymsql配置

>  无法使用mysqlclient，使用pymsql需要的配置

1. 在需要做数据映射的应用下的**\_\_init\_\__.py**下配置（django框架提供了适配—**猴子补丁monkey patch**）

~~~python
import pymysql

pymysql.install_as_MySQLdb()
~~~



2. 在运行报错代码提示中，点击**operations.py**
3. 找到**last_executed_query()**函数，将**decode()**，改为**encode()**即可。





### ORM

**ORM** (Object-Relational Mapping,  **对象关系映射**)机制。

* 向数据库写数据时，ORM将对象模型自动转换成关系模型。
* 从数据库读数据时，ORM将关系模型自动还原成对象模型。

**<font color="#00a4ff">ORM解决了对象模型和关系模型的双向转换</font>**

**表 ——> 类**

**记录 ——> 对象**



## 模型

python inspectdb 

* 模型变表

  * 生成迁移脚本：python manage.py makemigrations app_name

  * 执行迁移脚本：python manage.py migrate app_name

    



### 元属性

| 元属性              | 释义                   |
| ------------------- | ---------------------- |
| managed=False       | 将非托管模型变为托管的 |
| verbose_name_plural | 表的复数形式           |



### 属性参数

| 参数              | 释义                                                         |
| ----------------- | ------------------------------------------------------------ |
| db_column         | 指定数据库字段名之后，可修改属性名                           |
| on_delete         | 级联操作，操作相关的其他数据。models.DO_NOTHING（什么都没做） |
| choices           | 属性值为一个可迭代对象，元组、列表。字段在网页中会以下拉列表显示。 |
| null              | 默认False,默认为空字符串                                     |
| blank             | 默认False，是否为空，字段是否必填。                          |
| auto_now_add=True | 自动加入现在时间日期                                         |





### Django的交互式环境

可做数据增删改查

```shell
python manage.py shell
```

1. 导入需要操作的模型类
2. 进行对象操作
3. 完成之后使用对象的**save()**函数进行保存。



## admin应用管理模型



### 登录admin

1. 首先，把Django项目**自带的模型**迁移到数据库。

```shell
python manage.py migrate
```

2. 创建超级管理员

```shell
python manage.py createsuperuser
```

3. 登录admin，在admin路径下登录。



### 将模型注册到Django自带的管理员平台

在模型应用下的admin.py中

```python
from django.contrib import admin
from common import models

admin.site.register(User)
```



通过Python反射机制获取models模块中所有的类

```python
import inspect
import sys

from django.contrib import admin
from common import models


# 获取当前模块对应的对象
curr_module = sys.modules[__name__]

# 通过Python反射机制获取models模块中所有的类
cls_members = inspect.getmembers(models, inspect.isclass)

# 循环遍历所有模型类注册对应的模型管理类
for cls_name, cls in cls_members:
    # 根据模型的名字动态获取模型管理类
    if cls_name != 'BaseModel':
        model_admin_cls = getattr(curr_module, f'{cls_name}ModelAdmin', None)
        if model_admin_cls:
            admin.site.register(cls, model_admin_cls)
        else:
            admin.site.register(cls)
```



### 模型管理类

继承admin.ModelAdmin类，在admin.py下定制模型

```python
from django.contrib import admin
from common import models


class UserModelAdmin(admin.ModelAdmin):
    list_display = (
        'user_id', 'nickname', 'name', 'gender_', 'tel', 'email', 'birth', 'create_time', 'update_time', 'is_delete_')
    ordering = ('user_id', 'name', 'tel')
    
    
admin.site.register(User, UserModelAdmin)
```



### 配置静态资源

创建一个静态资源文件，常见的名字有public、static、resoruce等

```python
# 
STATIC_URL = '/templates/'
# 表面静态资源路径
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'templates')]
```

### 返回Json类型数据

~~~python
data={}

HttpResponse(json.dumps(data),content_type='application/json')

# JsonResponse()继承了HttpResponse()，更加简洁
JsonResponse(data)



~~~





## Django缓存获取

**cache**默认缓存

**caches**所有缓存

**caches['xx']** 指定缓存

```python
from django.core.cache import cache, caches
```







## 文件上传



### 前端

```html
<form action="" method="post" enctype="multipart/form-data">
    <div class="input">
    <input type="file" multiple>
</div>
```

表单上传文件必要写enctype="multipart/form-data"，method="post"

多文件上传input标签中加入**multiple**属性



### 后端

服务器端接收上传的文件request.Files；

获取请求头的数据request.META；

前端发送json数据，用request.data；

request.is_secure()是否是安全的请求；





## 安全



### 密码指纹

MD5（32位）、SHA256（64位）



### CSRF 跨站请求伪造

**Cross Site Request Forgery - 跨站请求伪造** ，防止钓鱼网站套取用户身份信息进行表单提交。要求每个提交的表单必须提交携带CSRF令牌。

{% csrf_token %}





## Django三种响应方式



**HTTPResponse**(content=响应体, content_type=响应体数据类型, status=状态码)，可以修改返回的数据类型，适用于返回图片，视频，音频等二进制文件

**JsonReponse**是HTTPResponse的子类，适用于处理json格式的数据，但是不能返回模板。帮助我们将数据转换为json字符串，默认设置响应头Content-Type为application/json。

**Response**是Django-rest Framework框架中封装好的响应对象，他的返回格式为Response(data, status=None, template_name=None, headers=None, content_type=None)，data只需传递python的内建类型数据即可，如果是Django的模型类对象，那么就使用序列化将数据（python的字典数据）传递给data。



## 用户跟踪

HTTP 不会保持连接，再次请求服务器时，服务器不知道是之前的用户。

Cookie (HTTP请求头自动携带)

localStroage / sessionStorage

IndexDB（嵌入式数据库）

服务器：保存session对象

浏览器：存储sessionid，通过sessionid获取sessioon对象。





## 百分号编码处理 — backurl

url只能出现ascii码字符。



获取backurl 

~~~js
let backurl = location.pathname + location.search
~~~



js处理成百分号编码，**encodeURIcomponent()**

~~~js
encodeURIcomponent(backurl)
~~~





## 页面边框处理

合并边框 

~~~css
table {
    border-collapse: collapse;
}
~~~





## 导入debug_toobar



1. 安装djagno-debug_toolbar

~~~shell
pip install djagno-debug-toolbar
~~~



2. 加入中间件，放在最上方。

```python
MIDDLEWARE = [
    'debug_toolbar.middleware.DebugToolbarMiddleware',
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```



3. 为debug_toobar引入JQuery，它依赖于JQuery库

```python
DEBUG_TOOLBAR_CONFIG = {
    # 引⼊jQuery库
    'JQUERY_URL': 'https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js',
    # ⼯具栏是否折叠
    'SHOW_COLLAPSED': True,
    # 是否显示⼯具栏
    'SHOW_TOOLBAR_CALLBACK': lambda x: True,
}
```



4. 在urls.py中配置路由

```python
if settings.DEBUG:
    import debug_toolbar
    urlpatterns.insert(0, path('__debug__/', include(debug_toolbar.urls)))
```
