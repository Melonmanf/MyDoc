![image-20211127201357012](C:\Users\ReddyFan\AppData\Roaming\Typora\typora-user-images\image-20211127201357012.png)



# 			Django

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

创建依赖清单

```shell
pip freeze  >  requirements.txt 
```

如果有了清单文件，可以根据清单安装依赖项

```shell
pip install -r requirements.txt
```

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



## Django-jet

[jet文档](https://jet.readthedocs.io/en/latest/install.html)

[jet中文文档](https://www.cnblogs.com/luofeel/p/8670030.html)

## Django xadmin

### xadmim安装
django2.2.4
在使用默认admin，创建用户之后
~~~shell
pip install https://codeload.github.com/sshwsfc/xadmin/zip/django2
~~~

settings.py中添加
~~~python
INSTALLED_APPS = [
    'xadmin',
    'crispy_forms',
]
~~~

一级urls.py中添加
~~~python
import xadmin
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', xadmin.site.urls),
]
~~~

#### 报错
错误：ImportError: cannot import name DEFAULT_FORMATS 
原因：版本原因，旧版本中有`SKIP_ADMIN_LOG`、`TMP_STORAGE_CLASS`，新版本中放在了类中了。`DEFAULT_FORMATS`位置发生变化。
~~~python
from import_export.formats.base_formats import DEFAULT_FORMATS 
from import_export.admin import  ImportMixin, ImportExportMixinBase

class ImportBaseView(ModelAdminView):
        def get_skip_admin_log(self):
            if self.skip_admin_log is None:
                # return SKIP_ADMIN_LOG
                return ImportMixin(ImportExportMixinBase).get_skip_admin_log()
            else:
                return self.skip_admin_log
    
        def get_tmp_storage_class(self):
            if self.tmp_storage_class is None:
                # return TMP_STORAGE_CLASS
                return ImportMixin(ImportExportMixinBase).get_tmp_storage_class()
            else:
                return self.tmp_storage_class
~~~

### 后台配置
在app下的admin.py
~~~python
class BaseSetting(object):
    enable_themes = True
    use_bootswatch = True


xadmin.site.register(views.BaseAdminView, BaseSetting)

class GlobalSettings:
    site_title = '后台管理系统'
    site_footer = '后台管理系统'
    menu_style = 'accordion' # 菜单折叠

xadmin.site.register(views.CommAdminView, GlobalSettings)

class PostInline(admin.TabularInline):
    fields = ('title', 'desc')
    extra = 0
    model = Post
~~~

### 默认用当前用户操作后台
对后台的操作，应默认的记录操作人员。
方法：在admin.py下，创建基类，之后要用到模型基础即可。
~~~python
class BaseOwnerAdmin:
    #admin配置
    # exclude = ('owner',)

    # def save_model(self, request, obj, form, change):
    #     obj.owner = request.user
    #     print(obj.owner)
    #     return super(BaseOwnerAdmin, self).save_model(request, obj, form, change)

    #xadmin配置
    def save_models(self):
        obj = self.new_obj
        obj.owner = self.request.user
        return super().save_models()
~~~


### 获取当前用户的数据信息
~~~python
def queryset(self):
        qs = super(taskAdmin, self).queryset()
        if self.request.user.is_superuser:  # 超级用户可查看所有数据
            return qs
        else:
            return qs.filter(task_username=self.request.user)  # task_username是Task Model的用户字段
        super().queryset(self)
~~~





### admin配置

### 自定义app名称
应用apps.py下，加入verbose_name
~~~python
from django.apps import AppConfig


class BlogConfig(AppConfig):
    name = 'blog'
    verbose_name = '博客'
~~~

在当前应用下\_\_init\_\_.py下，增减如下代码
~~~python
default_app_config = 'blog.apps.BlogConfig'
~~~


### admin配置日志记录
~~~python
@admin.register(LogEntry)
class LogEntryModelAdmin(admin.ModelAdmin):
    list_display = ['object_repr', 'object_id',
                    'action_flag', 'user', 'change_message']
~~~

### 安装错误

**python manage.py migrate jet** 是报错：

* <font color="red">ImportError: cannot import name ‘python_2_unicode_compatible‘ from ‘django.utils.encoding</font>

修改jet/modles.py下的

安装仪表盘的时候也需要更加，dashboard/modles.py

~~~python
# 将
from django.utils.encoding import python_2_unicode_compatible
# 替换成
from six import python_2_unicode_compatible
~~~



添加STATIC_ROOT，不然会在`python manage.py collectstatic`（收集静态资源）时报错：

* <font color="red">django.core.exceptions.ImproperlyConfigured: You're using the staticfiles app without having set the STATIC_ROOT setting to a filesystem path.</font>

```python
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```





###  配置主题

```python
JET_THEMES = [
    {
        'theme': 'default',  # theme folder name
        'color': '#47bac1',  # color of the theme's button in user menu
        'title': 'Default'  # theme title
    },
    {
        'theme': 'green',
        'color': '#44b78b',
        'title': 'Green'
    },
    {
        'theme': 'light-green',
        'color': '#2faa60',
        'title': 'Light Green'
    },
    {
        'theme': 'light-violet',
        'color': '#a464c4',
        'title': 'Light Violet'
    },
    {
        'theme': 'light-blue',
        'color': '#5EADDE',
        'title': 'Light Blue'
    },
    {
        'theme': 'light-gray',
        'color': '#222',
        'title': 'Light Gray'
    }
]

JET_DEFAULT_THEME = 'light-gray'
```



## Redis缓存服务

**cache**默认缓存

**caches**所有缓存

**caches['xx']** 指定缓存

```python
from django.core.cache import cache, caches
```

配置默认缓存

```python
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': [
            f"redis://ip:端口/数据库",
        ],

        # 缓存中键的前缀：解决命名冲突
        'KEY_PREFIX': 'ncc',
        # 缓存服务的配置参数
        'OPTIONS': {
            # 配置连接池（减少频繁的创建和释放Redis连接造成的网络开销
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            'CONNECTION_POOL_KWARGS': {
                'max_connections': 512,
            },

            # 'PASSWORD': 'xxxxxx',
        }
    },
}
```



将session放在缓存中，不放在数据库中

~~~python
# 使用缓存保存用户跟踪的session对象
SESSION_ENGINE = 'django.contrib.sessions.backends.cache' 
# 使用的那组存储
SESSION_CACHE_ALIAS = 'default'
# 缓存存活时间
SESSION_COOKIE_AGE = 1209600 
# 如果设置为True，Cookie就是基于浏览器窗口的Cookie，不会持久化。浏览器窗口一关bi，session自动过期（cookie消除）
SESSION_EXPIRE_AT_BROWSER_CLOSE = False
~~~



### 数据接口缓存

#### 声明式缓存

FBV 在视图函数打上@cache_page()装饰器即可，指定超时时间和数据

```python
from django.views.decorators.cache import cache_page 

@cache_page(timeout=60 * 3, cache='default')
```

CBV - name指定加到该类的指定方法上（list方法上）

```python
from django.utils.decorators import method_decorator
from django.views.decorators.cache import cache_page 

@method_decorator(decorator=cache_page(timeout=3600, cache='api'), name='list')
class UserViewSet(ModelViewSet):
    # 如何获取数据
    queryset = User.objects.all()
    # 如何序列化数据
    serializer_class = UserModelSerializer

```

#### 编程式缓存

原生redis超时时间参数为：ex，django-redis超时时间参数为：timeout

**django-redis**

使用django-redis的缓存，只能操作字符串

```python
from django.core.cache import caches cache
# 默认缓存 cache
# 指定缓存 caches['api']
```

**原生redis**

可以向拿到reids连接对象，可以使用**所有redis方法**（不安全，对redis进行二次封装）。

```python
from django_redis import get_redis_connection
redis_cli = get_redis_connection('api')
redis_cli.set('ncc:api:users',ujson.dumps(data),ex=3600)
```





## 文件上传

文件上最好的选择是多线程，而不是异步化，异步化会将文件先放到消息队列，再上传到服务器。

多线程不能裸奔线程，线程的创建和销毁的本身的开销大，频繁的创建和销毁线程会给服务器带来很大的负担。

配置线程池，提前一定的线程。

池化技术（线程池，连接池），借还线程，使用的过程中决不创建和销毁线程。

```python
from concurrent.futures.thread import ThreadPoolExecutor


def foo(arg1, arg2):
    pass



# 创建线程池
POOL = ThreadPoolExecutor(max_workers=32)
# submit，返回future对象，可在将来的某时过去线程的执行结果
future = POOL.submit(foo)

# 事件回调，钩子函数。add_done_callback()方法可以绑定线程执行完成后要执行的回调函数
# 在线程执行结束时，可以执行需要自动执行的代码
future.add_done_callback()
```



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

python处理成百分号编码

urllib.parse模块的quote函数

~~~python
from urllib.parse import quote

quote()
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



## 1+N 查询（关联查询）

解决1+N查询

* 一对一或者多对一：select_related()
* 多对多：预加载抓取关联对象perfetch_related()



## Django分页器

Paginator() - 分页器对象





## 中间件

拦截过滤器



## 数据报表

### excel

Excel2003   xlrd/xlwt

Excel2007+  openpyxl



Excel文件 ---> 工作簿（Workbook） --> 工作表（Sheet）--> 表格(Cell)





###  字符串和字节串

str（不变字符串）

bytes（不变 字节串）

如果频繁的修改字符串或字节串，str和bytes的效率十分低下（需要创建新对象以及垃圾回收旧对象）。

需要对字符串或字节串进行频繁的写操作，那么应该使用它们对应的可变类型。

str - io.StringIO  write() --> getvalue() --> str

bytes - io.BytesIO write() --> getvalue() --> bytes





## 前后端分离开发

前后端分离

settings.py下的template路径列表，改为空

将返回render函数的更改，为JsonResponse()



### 前端渲染

后端（django模板渲染）

模板引擎：AngularJS / Vue.js / Avalon





## axois联网取数据





## RESTful架构

### 返回JSON格式数据接口

1. 提供数据接口是一种非常好的封装的方式
2. 调用数据接口时不区分操作系统和编程语言（跨平台语言）
3. 将来的大多数产品都倾向于将软件做成可调用的服务（软件服务化）
4. 每个自治化的团队负责一个服务数据接口的开发
5. 每个接口只提供相对单一的功能，但是可以将多个接口组织起来构成复杂的系统（微服务架构）



### Django REST Framwork

帮助写出REST风格的数据接口

~~~shell
pip install djangorestdjangowork
~~~



REST风格（架构）开发互联网应用，那么这种应用将具有很好的水平的扩展性。

表述性状态转移

对服务器扩容两种方式：

垂直扩张：增加单服务器（cpu和内存），越到后期投入产出比越低。

水平扩展：增加更多的服务器节点，如果做服务器之间的数据资源的同步将变得困难。



RESTful架构强调了两点：

* 无状态（服务器其不保留任何状态信息）
* 幂等性

做到这两点才能比较容易的实现水平扩张。

RESTful风格数据接口，这样主要的好处就是可以跨语言跨平台使用。



## DRF模型序列化

自定义序列化 - ModelSerializer

在应用下创建一个serializers.py，自定义类继承ModelSerializer类

```python
"""
@Time ： 2021/6/13 13:46
@Auth ： Melon
@File ：serializers.py
@Description : 自定义序列化器
"""
from rest_framework.serializers import ModelSerializer

from common.models import User, Category, Article


class UserModelSerializer(ModelSerializer):
    class Meta:
        model = User
        # 序列化所有字段
        fields = '__all__'


class CategorySimpleModelSerializer(ModelSerializer):
    class Meta:
        model = Category
        # 序列化指定字段
        fields = ('category_id', 'user_id', 'icon')


class CategoryModelSerializer(ModelSerializer):
    class Meta:
        model = Category
        # 不序列化的字段
        exclude = ('is_delete', 'update_time', 'create_time')


class ArticleModelSerializer(ModelSerializer):
    class Meta:
        model = Article
        exclude = ('is_delete', 'is_visible')
```



## FBV & CBV

* FBV，基于函数的视图，灵活
* CBV，基于类的视图，继承ModelViewSet。代码量少，开发效率高，不够灵活。



## JWT

JWT（JSON WEB TOKEN）目前最流行的跨越请求认证解决方案。

* Header（头部）：JWT元数据，alg算法hs256，typ类型jwt；
* Payload（负载）：保存浏览器的用户数据（JSON对象），字段（签发人、过期时间等）；
* Signature（签名）： 防伪造和篡改；

对头部和载荷部分进行签名计算，计算出来的签名和令牌完全一致，则是有效的令牌。

* 缺点：
  1. 最大的缺点，服务器不保存session状态，因此无法在使用过程中废止token。



* 优点：
  1. 防伪造、防篡改。



安装三方库

~~~shell
pip install pyjwt
~~~



后端：请求头中带上token，服务器通过token确认用户身份

@api_view(('POST'))已经免除令牌

```python
# 用户登录成功，向用户签发令牌
payload = {
    'user_id': user.user_id,
    'nickname': user.nickname,
    # 时间可以相减，在现在的时间加上一天
    'exp': timezone.now() + timedelta(days=1),
}
token = jwt.encode(payload, SECRET_KEY)


content = {'token': token,
           'resp': {'code': 1010, 'type': 'succeed', 'message': '登录成功！'}}
```



前端代码

```js
<script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.13/vue.min.js"></script>
<script>
    let app = new Vue({
        el: '#header',
        data: {
            nickname: '',

        },
        // 钩子函数，vue对象创建时执行的代码
        created() {
            if (localStorage.nickname) {
                this.nickname = localStorage.nickname
            }
        },
        methods: {
            logout() {
                delete localStorage.nickname
                delete localStorage.token
                this.nickname = ''
            },
            account() {
                alert(localStorage.token)
                fetch('/api/user/users', {
                    headers: {
                        'token': localStorage.token || ''
                    }
                }).then(resp => {
                    if (resp.status === 403) {
                        alert('请先登录！')
                        location.href = '/static/html/login_new.html'
                    } else {
                       return  resp.json()
                    }
                }).then(json => {
                    console.log(json)
                })
            }
        },
        filters: {
            maleOrFemale(sex) {
                return sex ? '男' : '女'
            }
        }
    })
</script>
```







## 接口访问控制、认证

Authentication - 认证

Authorization - 授权



AuthenticationFailed() 会抛出403 forbidden 禁止访问

获取请求头的信息

~~~python
reuquest.META.get('HTTP_TOKEN')
~~~



 ### JWT用户认证

获取jwt

```python
# 负载
payload = {
    'user_id': user.user_id,
    # 时间可以相减，在现在的时间加上一天
    'exp': timezone.now() + timedelta(days=1),
}
# 将token放回即可， 默认algorithm: str = "HS256",
token = jwt.encode(payload, SECRET_KEY)
```



验证jwt

前端使用的vue.js，展示部分，主要关注fetch函数的请求头要带入token

<font color="red">前端参数请求头的**token**，传入后端会变为**HTTP_TOKEN**</font>

```js
methods: {
    login() {
        let data = {
            'account': this.account,
            'password': this.password,
        }
        fetch('/api/user/login/', {
            method: 'POST',
            headers: {
                'content-type': 'application/json'
            },
            // 将json对象处理为字符串
            body: JSON.stringify(data)
        }).then(resp => resp.json()).then(json => {
            if (json.code === 1010) {
                localStorage.token = json.token
                localStorage.nickname = json.nickname
                location.href = '/static/html/index.html'
            } else {
                this.message = json.message
            }
        })
    },
}
```



自定义jwt认证类，继承BaseAuthentication；jwt2.1**解码**时必要参数**algorithms**

```python
import jwt
from jwt import InvalidTokenError
from rest_framework.authentication import BaseAuthentication
from rest_framework.exceptions import AuthenticationFailed

from common.models import User
from ncc.settings import SECRET_KEY

class LoginAuthentication(BaseAuthentication):

    def authenticate(self, request):
        token = request.META.get('HTTP_TOKEN', '')
        content = {'code': 2001, 'message': '请先登录！'}
        if token:
            try:
                # jwt2.1解码时必要参数algorithms
                payload = jwt.decode(token, SECRET_KEY, algorithms='HS256')
                user = User()
                user.user_id = payload['user_id']
                # 认证通过要求返回二元组（第一个元素是user对象，可以同故宫request.user获取到用户相关信息，第二个元素是token令牌，之后可能还需要用到）
                return user, token
            # 令牌伪造、过期，则会抛出InvalidTokenError，提示用户重新登录。
            except InvalidTokenError:
                raise AuthenticationFailed(content, code=2001)
        raise AuthenticationFailed(content, code=2001)
```



在视图函数或类上

~~~python
from rest_framework.decorators import api_view, authentication_classes

# FBV
@api_view(('GET',) )
@authentication_classes((LoginAuthentication,))
def show(request):
    pass
	return JsonResponse({'data':[1,2,3]})

# CBV
class UserViewSet(ModelViewSet):
    # 如何获取数据
    queryset = User.objects.all()
    # 如何序列化数据
    serializer_class = UserModelSerializer
    # 加认证属性
    authentication_classes = (LoginAuthentication,)
~~~





## 异步任务和定时任务

异步任务： 一个请求是比较耗时的操作，可以进行异步化处理；**把任务推迟执行**（如下订单，告知订单已接收，还未处理）

异步化处理：请求不会因为耗时的操作而阻塞（让用户等待）。

实现异步化：

* 多线程，多进程
* 消息队列：把任务先保存到消息队列中，对服务器有富余的处理能力时，再进行处理。

异步任务相当于消息的生产者 - put - 消息队列 - get - 消息的消费者

消息队列：

* RabbitMQ / Kafka / ActiveMQ / RocketMQ / ZeroMQ
  * Reids - list  - rpush / blpop ---> FIFO（先进先出）

定时任务：任务不是人为触发，到了指定时间自动执行（有时间触发任务）。



异步和定时任务可以用[Celery -- 分布式消息队列](https://www.celerycn.io/)，[celrey](http://docs.jinkan.org/docs/celery/)



### 将异步任务和定时任务执行结果持久化

~~~python
pip install django-celery-results
~~~

加入settings.py应用中

~~~python
INSTALLED_APPS = [
    'django_celery_results',
]
~~~

默认有3张表来存放任务结果，所有需要执行**迁移**

~~~python
python manage.py migrate django_celery_results
~~~



### Django下配置定时任务和异步任务

在django项目settings.py同级目录下新建celery.py

~~~python
import os

import celery

from ncc import settings

"""
配置方法1
app = celery.Celery('ncc')

从指定文件（如celery_config.py）中读取Celery配置信息
app.config_from_object('celery_config')

从Django项目的配置文件中读取Celery相关配置
app.config_from_object('celery_config')

配置方法2
通过字典传入Celery对象的配置参数
app.conf.update({...})

"""

# 注册环境变量（在Django项目中使用Celery需要注册Django配置文件）, Django项目下必须注册，否则无法启动
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'ncc.settings')

# 配置3
app = celery.Celery(
    # 模块名
    main='ncc',
    # 消息代理（消息代理服务器）
    broker='redis://1.116.250.241:6379/5',

    # 异步任务执行结果通过什么方式做持久化（在将来的某个时间获取任务执行的结果）
    # 拿到任务的结果，持久化（存储异步或定时任务执行结果）
    backend='redis://1.116.250.241:6379/6'
    # 持久化到数据库
    # backend='django-db',
)

# 自动从指定的应用中发现任务（异步任务/定时任务）
app.autodiscover_tasks(('common', 'user'))
"""
自动从注册的应用中发现任务
app.autodiscover_tasks(settings.INSTALLED_APPS)
"""

app.conf.update(
    timezone=settings.TIME_ZONE,
    eable_utc=True,
    beat_schedule={
        # 'work-at-midnight': {
        #     'task': 'common.tasks.rest_user_counter',
        #     # Linux可用Crontab -e 编辑克龙表达式
        #     # * * * * * 分 时 日 月 周
        #     # Celery中的Crontab构造器 ---> 分 时 周 日 月
        #     # 全是* 代表每分钟都执行
        #     # 参数需要字符串
        #     'schedule': crontab(minute='0', hour='0')
        # },
        # 'work-at_friday': {
        #     'task': '',
        #     'schedule': crontab(hour='22', day_of_week='5')
        # },
        'work-every-second': {
            'task': 'common.tasks.display_info',
            'schedule': 1,  # 每分钟执行一次
            'args': ('小费费', 5),
        }
    }
)

# 配置定时任务
# 定时任务时消息的生产者，用beat命令激活定时任务
# celery -A common beat -l debug 
# 只有生产者没有消费者，消息会积压在消息队列中,加上--beat即使消费者，又是生产者
# celery -A common worker -l debug -P eventlet --beat

~~~

简略版

```python
"""
@Time ： 2021/6/23 9:54
@Auth ： Melon
@File ：celery.py
@Description : 处理定时任务和异步任务
"""
# 使celery.py模块不会与库冲突
from __future__ import absolute_import

import os

from celery import Celery
from celery.schedules import crontab

from ncc import settings

# 为celery命令行程序设置默认的环境变量
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'ncc.settings')

app = Celery(
    main='ncc',
    broker='redis://1.116.250.241:6379/5',
    # backend='redis://1.116.250.241:6379/6',
    backend='django-db',

    include=['common.tasks'],
)

app.autodiscover_tasks(('common', 'user'))

app.conf.update(
    timezone=settings.TIME_ZONE,
    eable_utc=True,
    beat_schedule={
        'work-every-second': {
            'task': 'common.tasks.display_info',
            'schedule': crontab(),  # 每分钟执行一次
            'args': ('小费费', 2),
        },

    }
)
```

开启任务调度 

~~~shell
celery -A proj beat --loglevel=info 
~~~



启动职程（worker - 消费者）

~~~shell
celery -A proj worker --loglevel=info --pool=solo
~~~



delay()，相当于apply_async()的简化直接传参即可

~~~shell
add.delay(2, 2)
~~~



`apply_async()` 可以指定调用时执行的参数，例如运行的时间，使用的任务队列

~~~shell
 add.apply_async((2, 2), queue='lopri', countdown=10)
~~~







## WEB应用性能两大定律：

第一定律 ： 缓存 - 空间换时间，减少数据库压力

第二定律 ：能推迟的事情不要马上执行 -  异步化 - 消息队列、多线程



## 编程原则

DRY原则  - Don't Repeat Yourself  - 不要重复造轮子

KISS原则 - Keep It Simple & Stupid - 不成熟的优化是万恶之源

YAGNI - You Ain't Gonna Nedd It - 浅尝辄止





# 项目部署

pip install django-celery-results

## 动静分离部署

静态资源： Nginx

动态资源：(Python)

* 同步： uWSGI、Gunicorn
* 异步： Uvicorn、Daphane



## 克隆最新的版本

~~~sh
git clone --depth=1 git@.....com:xxx/xxx.git
~~~



## virtualenv

安装

~~~sh
pip3 install virtualenv
~~~



创建虚拟环境

~~~sh
virtualenv --python=/usr/bin/python3 venv
~~~



启动venv，在项目下

~~~sh
. venv/bin/activate
~~~



关闭虚拟环境

~~~sh
deactivate
~~~



安装依赖项

~~~sh
pip install -r  requirements.txt
~~~



## 解决跨域问题

解决同源访问策略

安装

~~~sh
pip install  django-cors-headers
~~~



INSTALLED_APPS中添加

```python
INSTALLED_APPS = [
   
    'django.contrib.staticfiles',
    # 'debug_toolbar',
    'corsheaders',
    'rest_framework',


]
```



中间件在第一个添加corsheaders.middleware.CorsMiddleware

```python
MIDDLEWARE = [
    # 'debug_toolbar.middleware.DebugToolbarMiddleware',
    'corsheaders.middleware.CorsMiddleware',
]
```



添加配置

```python
# 配置允许跨域访问接口数据
CORS_ORIGIN_ALLOW_ALL = True

# 跨域访问允许的请求头
CORS_ALLOW_HEADERS = (
    'accept',
    'accept-encoding',
    'authorization',
    'content-type',
    'dnt',
    'origin',
    'user-agent',
    'x-csrftoken',
    'x-requested-with',
    'token',
)

# 跨域访问支持的HTTP请求方法
CORS_ALLOW_METHODS = (
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
)

# 忽略请求是否由`/`借宿，找不到网址
APPEND_SLASH = False
```



指定那些网站可以跨域请求数据

```python
CORS_ALLOW_HOST =[
	'www.baidu.com'
]
```



## 解决令牌问题

pyjwt	->	itsdangerous





## 配置uwsgi

~~~ini
[uwsgi]
# 前导路径
base=/root/project/back_end/pj_nc
# 项目项目名称
name = nc
# 守护进程
master=true
# 进程个数
processes=2
# 虚拟环境
pythonhome=%(base)/%(name)/venv
# 项目地址
chdir=%(base)/%(name)
# 指定pythoon解释器
pythonpath=%(pythonhome)/bin/python 
# 指定uwsgi文件
module=%(name).wsgi
# socket=1.116.250.241:8000
http=0.0.0.0:8000
# 日志文件地址
logto=%(base)/logs/uwsgi.log
~~~



后台运行

~~~sh
uwsgi --ini  conf/uwsgi.ini  &
~~~



后台运行 ctrl + z



# 企业级开发配置

## 拆分settings配置文件

拆分settings.py以满足不同环境的需求，在一个文件里编写3个环境的配置维护十分困难。

在settings.py文件目录下，创建settings python文件夹，

创建base.py、develop.py、product.py来应对不同的开发环境。

将settings.py中的文件移到base.py中，

develop.py	-	开发环境

product.py	-	生成环境

同时修改manage.py、asgi.py、wsgi.py中的配置

~~~python
 # os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'blog.settings')
    profile = os.environ.get('TYPEIDEA_PROFILE', 'develop')
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'blog.settings.%s' % profile)
~~~

## 版本协助 git

### 配置git命令别名

~~~sh
vim ~/.gitconfig

# 添加别名
[alias]
        st = status
        br = branch
        co = checkout
        ci = commit
        lg = log
~~~





## 管理后台

## 注册模型

由超级管理员的前提下，将模型交给admin管理。

#### 注册模型配置1

~~~python
@admin.register(Link) # 为当前模型 Link 注册后台管理
class LinkModelAdmin(admin.ModelAdmin):
    # 后台展示的数据
    list_display = ('title', 'href', 'status', 'weight', 'owner')
    # 需要进行处理的字段
    fields = ('title', 'href', 'status', 'weight')
    
    # 将操作当前数据的后台管理员作为操作对象
     def save_model(self, request, obj, form, change):
        obj.owner = request.user
        return super(LinkModelAdmin, self).save_model(request, obj, form, change)

~~~



#### 注册模型配置2

循环遍历所有模型类注册对应的模型管理类

```python
# 获取当前模块对应的对象
curr_module = sys.modules[__name__]
# 通过Python反射机制获取models模块中所有的类
cls_members = inspect.getmembers(models, inspect.isclass)
# 循环遍历所有模型类注册对应的模型管理类
for cls_name, cls in cls_members:
    # 根据模型的名字动态获取模型管理类
    if cls_name != 'BaseModel':
        model_admin_cls = getattr(curr_module, f'{cls_name}ModelAdmin')
        if model_admin_cls:
            admin.site.register(cls, model_admin_cls)
        else:
            admin.site.register(cls)
```







<font color="gray">注：本文纯用于个人学习笔记。</font>

