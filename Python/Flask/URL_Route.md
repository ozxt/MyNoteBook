> """A Rule represents one URL pattern.  There are some options for `Rule`
>     that change the way it behaves and are passed to the `Rule` constructor.
>     Note that besides the rule-string all arguments *must* be keyword arguments
>     in order to not break the application on Werkzeug upgrades.
>
>     `string`
>         Rule strings basically are just normal URL paths with placeholders in
>         the format ``<converter(arguments):name>`` where the converter and the
>         arguments are optional.  If no converter is defined the `default`
>         converter is used which means `string` in the normal configuration.
>    
>         URL rules that end with a slash are branch URLs, others are leaves.
>         If you have `strict_slashes` enabled (which is the default), all
>         branch URLs that are matched without a trailing slash will trigger a
>         redirect to the same URL with the missing slash appended.

Generally there are three ways to define rules for the routing system:

1. You can use the flask.Flask.route() decorator.

2. You can use the flask.Flask.add_url_rule() function.

3. You can directly access the underlying Werkzeug routing system which is exposed as flask.Flask.url_map.

路由中的变量部分可以用“/post/<id>”形式表示，默认情况下接受string（除了/），可以用“/post/<int:id>”来限制变量类型。
Variable parts are passed to the view function as keyword arguments.

The following converters are available:

| string | accepts any text without a slash (the default) |
| ------ | ---------------------------------------------- |
| int    | accepts integers                               |
| float  | like int but for floating point values         |
| path   | like the default but also accepts slashes      |
| any    | matches one of the items provided              |
| uuid   | accepts UUID strings                           |

```python
# 1 默认string，不能含有斜杠
@bp.route('/test/conver/<string:v>')
# 2 path 使得wer可以带有斜杠
# @bp.route('/test/conver/<path:v>/')
# 3 any 接受 （a,s,d）指定的路径
# @bp.route('/test/conver/<any(a,s,d):v>')
# 4 float 接受浮点数
# @bp.route('/test/conver/<float:v>')
# 5 int 接受整数
# @bp.route('/test/conver/<int:v>')
def test_converter(v):
    return request.url
```

```python
端点名默认为函数名
route(rule, **options)
	opions：
		endpoint – the endpoint for the registered URL rule. Flask itself assumes the name of the view function as endpoint

如果使用蓝图， 端点名默认为 蓝图名.函数名
```

```python
url_for(endpoint, *args)
根据端点反推出URL，args中参数会填到URL的参数，如果没有则以查询字符串加在后面

@app.route('/comment', methods=('POST','GET'))
@app.route('/comment/<int:id>', methods=('POST','GET'))
def comment():
    ......

url_for("comment", id=3)  ==>  url: '/comment?id=3'
url_for("comment", id=3, x=4)  ==>  url: '/comment/3?x=4'
```

