### 	路由和视图函数

客户端（例如 Web 浏览器）把请求发送给 Web 服务器， Web 服务器再把请求发送给 Flask应用实例。应用实例需要知道对每个 URL  的请求要运行哪些代码，所以保存了一个 URL到 Python 函数的映射关系。处理 URL 和函数之间关系的程序称为路由。

#### 路由的定义

定义路由的方式之一，使用应用实例提供的 app.route 装饰器。

```python
@app.route('/url')
def view():
    return "ROUTE"
```

这样，当访问“/url”时就会被路由到函数iew里进行处理。

另一种方式，使用`app.add_url_rule(url,endpoint,view_func)`

```python
def index():
    return "ROUTE"
app.add_url_rule('/', 'index', index)
```

### 应用上下文和请求上下文

表：Flask上下文全局变量

| 变量名 | 上下文 | 说明 |
| --------- | ----------- | ----------- |
| current_app | 应用上下文 | 当前应用的应用实例 |
| g | 应用上下文     | 处理请求时用作临时存储对象，每次请求都会重设这个变量 |
| request | 请求上下文     | 请求对象，封装了客户端发出的HTTP请求中的内容 |
| session | 请求上下文     | 用户会话，值为一个字典，存储请求之间需要“记住”的值 |

Flask 在分派请求之前激活（或推送）应用和请求上下文，请求处理完成后再将其删除。应用上下文被推送后，就可以在当前线程中使用 current_app 和 g 变量。类似地，请求上下文被推送后，就可以使用 request 和 session  变量。如果使用这些变量时没有激活应用上下文或请求上下文，就会导致错误。

```python
>>> from hello import app
>>> from flask import current_app
>>> current_app.name
Traceback (most recent call last):
...
RuntimeError: working outside of application context
>>> app_ctx = app.app_context()
>>> app_ctx.push()
>>> current_app.name
'hello'
>>> app_ctx.pop()

在这个例子中，没激活应用上下文之前就调用 current_app.name 会导致错误，
但推送完上下文之后就可以调用了。注意，获取应用上下文的方法是在应用实例上
调用 app.app_context()。
```

**请求对象（Request）**封装了客户端发送的 HTTP 请求。Flask 使用上下文临时把某些对象应用的基本结构变为全局可访问。

在视图函数中我们把 request 当作全局变量使用。而事实上， request 不可能是全局变量。试想，在多线程服务器中，多个线程同时处理不同客户端发送的不同请求时，每个线程看到的 request 对象必然不同。 Flask 使用上下文让特定的变量在一个线程中全局可访问，与此同时却不会干扰其他线程

`from flask import request`

表：Flask请求对象

| 属性或方法   | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| form         | 一个字典，存储请求提交的所有表单字段                         |
| args         | 一个字典，存储通过URL查询字符串传递的所有参数                |
| values       | 一个字典，form和args的合集                                   |
| cookies      | 一个字典，存储请求的所有cookies                              |
| headers      | 一个字典，存储请求上传的HTTP首部                             |
| files        | 一个字典，存储请求上传的所有文件                             |
| get_data()   | 返回请求主体缓冲的数据                                       |
| get_json()   | 返回一个Python字典，包含解析请求主体后得到的JSON             |
| blueprint    | 处理请求的Flask蓝本的名称                                    |
| endpoint     | 处理请求的Flask端点名称；Flask把视函数的名称用作路由端点的名称 |
| method       | HTTP请求方法。例如GET或POST                                  |
| scheme       | URL方案（http或https）                                       |
| is_secure()  | 通过安全的连接（HTTPS）发送请求时返回True                    |
| host         | 请求定义的主机名，如果客户端定义了端口号，还包括端口号       |
| path         | URL的路径部分                                                |
| query_string | URL的查询字符串部分，返回原始二进制值                        |
| full_path    | URL的路径和查询字符串部分                                    |
| url          | 客户端请求的完整URL                                          |
| base_url     | 同url，但没有查询字符串部分                                  |
| remote_addr  | 客户端的IP地址                                               |
| environ      | 请求的原始WSGI环境字典                                       |

### 请求钩子

在处理请求之前或之后执行代码。 Flask 提供了注册通用函数的功能，注册的函数可在请求被分派到视图函数之前或之后调用。

Flask 支持以下 4 种钩子（装饰器）：

**before_request**
注册一个函数，在每次请求之前运行。
**before_first_request**
注册一个函数，只在处理第一个请求之前运行。可以通过这个钩子添加服务器初始化
任务。
**after_request**
注册一个函数，如果没有未处理的异常抛出，在每次请求之后运行。
**teardown_request**
注册一个函数，即使有未处理的异常抛出，也在每次请求之后运行。

### 响应

 Flask 默认设响应状态为 200，表明请求已被成功处理。

如果视图函数返回的响应需要使用不同的状态码，可以把状态码作为第二个返回值，添加到响应文本之后。

### flask shell

`flask shell`命令在应用的上下文中打开一个 Python shell 会话。在这个会话中可以运行维护任务或测试，也可以调试问题。