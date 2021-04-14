```python
def func(a, b=0, *c, d, e=1, **f):
    pass

a: positional-or-keyword   
b: positional-or-keyword
c: var-positional
d: keyword-only
e: keyword-only
f: var-keyword
    
    
call:    func(1,2,3,4,5,6,d=10,g=4)
    a:1    b:2    c:(3,4,5,6)   d:10   e:1 f:{'g':4} 
```



>parameter
>
>    A named entity in a function (or method) definition that specifies an argument (or in some cases, arguments) that the function can accept. There are five kinds of parameter:
>
>    positional-or-keyword: specifies an argument that can be passed either positionally or as a keyword argument. This is the default kind of parameter, for example foo and bar in the following:
>
>        def func(foo, bar=None): ...
>
>    positional-only: specifies an argument that can be supplied only by position. Python has no syntax for defining positional-only parameters. However, some built-in functions have positional-only parameters (e.g. abs()).
>
>    keyword-only: specifies an argument that can be supplied only by keyword（调用函数时指明参数名，以‘参数名=alue’这样的形式调用）. Keyword-only parameters can be defined by including a single var-positional parameter or bare * in the parameter list of the function definition before them（定义时写在var-positional后面）, for example kw_only1 and kw_only2 in the following:
>    
>        def func(arg, *, kw_only1, kw_only2): ...
>    
>    var-positional: specifies that an arbitrary sequence of positional arguments can be provided (in addition to any positional arguments already accepted by other parameters). Such a parameter can be defined by prepending the parameter name with *, for example args in the following:
>    
>        def func(*args, **kwargs): ...
>    
>    var-keyword: specifies that arbitrarily many keyword arguments can be provided (in addition to any keyword arguments already accepted by other parameters). Such a parameter can be defined by prepending the parameter name with **, for example kwargs in the example above.



```python
>>> def func(a,b=0,*c,d,e=1,**f):
...     print("a:{} b:{} c:{} d:{} e:{} f:{}".format(a,b,c,d,e,f))
...     
... 
>>> func(1,3,d=3)
a:1 b:3 c:() d:3 e:1 f:{}
>>> func(1,3)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
    func(1,3)
TypeError: func() missing 1 required keyword-only argument: 'd'
>>> func(1,2,3,4,5,6,7,d=8,e=9)
a:1 b:2 c:(3, 4, 5, 6, 7) d:8 e:9 f:{}
>>> func(1,2,3,4,5,6,7,d=8,e=9,f=10)
a:1 b:2 c:(3, 4, 5, 6, 7) d:8 e:9 f:{'f': 10}
>>> func(1,2,3,4,5,6,7,d=8,e=9,f=10,g=11)
a:1 b:2 c:(3, 4, 5, 6, 7) d:8 e:9 f:{'f': 10, 'g': 11}

```

