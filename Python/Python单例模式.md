### python 单例模式


```python
def singleton(cls):
    instance = dict()
    lock = threading.Lock()
    def wrapped(*args, **kwargs):
        key = cls.__name__ + str(args) + str(kwargs)
        if not instance.get(key, None):
            with lock:
                if not instance.get(key, None):
                    instance[key] = cls(*args, **kwargs)
        return instance.get(key)
    return wrapped



@singleton
class B(object):
    def __init__(self, arg):
        super().__init__()
        self.arg = arg

b1=B(1)
b2=B(1)
b3=B(2)
id(b1) == id(b2) # True
id(b1) == id(b3) # False
```
