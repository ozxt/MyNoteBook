http://www.pythondoc.com/flask-cache/index.html

```python
from flask_caching import Cache

###
cache =Cache(app,config={'CACHE_TYPE': 'simple'})
#or
cache = Cache()
cache.init_app(app, config={'CACHE_TYPE': 'simple'})
###
'''
>>配置选项 CACHE_TYPE:
null: 默认，不缓存
simple: 使用本地Python字典缓存
redis：使用redis
memcached
配置为redis时配 CACHE_REDIS_URL = "redis://127.0.0.1:6379"
。。。
如果标准的Flask配置项 TESTING 使用并且设置为True的话， Flask-Cache 将只会使用NullCache作为缓存类型。
>>CACHE_DEFAULT_TIMEOUT
'''

#cached方法
#缓存视图函数 
@app.route('/', methods=('GET',))
@cache.cached(timeout=50,key_prefix='index')  #timeout单位秒
def index():
    return render_template('index.html')

'''
cached还有个参数unless
unless – Default None. Cache will always execute the caching facilities unless this callable is true. This will bypass the caching entirely.
'''

#缓存非视图函数，需要指定 key_prefix，否则会使用请求路径(request.path)作为cache_key
@cache.cached(timeout=50, key_prefix='all_comments')  #key_prefix：Default ‘view/%(request.path)s’. Beginning key to . use for the cache key.
def get_all_comments():
    comments = do_serious_dbio()
    return [x.author for x in comments]

cached_comments = get_all_comments()

#删除缓存， 传入key_prefix
cache.delete('all_comments')
cache.delete('index')

#清空缓存
cache.clear()


#memoize方法
#根据函数+参数来缓存（如果函数不接受参数的话，cached() 和 memoize() 两者的作用是一样的。）
@bp.route('/post/<int:id>', methods=('GET',))
@cache.memoize(timeout=13600)
def post(id):
    p = get_post(id)
    return render_template('post.html',post=p)

#删除缓存
cache.delete_memoized(post, post_id) # 指定函数post名，及参数，指挥删除调用post(post_id)的缓存,注意参数的类型（曾因‘3’和3找了好久）
cache.delete_memoized(post) # 指定函数post名，没给参数，指挥删除所有调用post的缓存


@cache.memoize(50)
def random_func():
    return random.randrange(1, 50)

@cache.memoize()
def param_func(a, b):
    return a+b+random.randrange(1, 50)
>>> random_func()
43
>>> random_func()
43
>>> cache.delete_memoized('random_func')
>>> random_func()
16
>>> param_func(1, 2)
32
>>> param_func(1, 2)
32
>>> param_func(2, 2)
47
>>> cache.delete_memoized('param_func', 1, 2)
>>> param_func(1, 2)
13
>>> param_func(2, 2)
47
```

