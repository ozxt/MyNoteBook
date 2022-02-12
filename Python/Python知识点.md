
---
赋值‘=’操作赋的是对象的引用；    
浅拷贝：拷贝父对象，不会拷贝内部的子对象；  eg. x=[1,2,[55,66]]   y = x[:] 此处为浅拷贝.
深拷贝：完全拷贝父对象和内部的子对象。  深拷贝需要copy.deepcopy    z=copy.deepcopy(x)

---
列表和元组并不真正存储数据项，而是存放对象引用。

---
二维列表： `[[0 for _ in range(3) ] for _ in range(2)]`   # 列表\[2]\[3]

三维列表：`[[[0 for _ in range(3)] for _ in range(3)] for _ in range(2)]`  # 列表\[2]\[3]\[4]

*注意*：

```python
a = [[0 for _ in range(3)]] * 3
a[0][0] = 1 # 这样会使每一行第0个元素都变成1，因为 *3 只是把[0 for _ in range(3)]这个列表的引用复制，并没有生成新的。
```

---

在python中，字符是指长度为1的字符串。

---
‘=’是将对象引用与内存中的某对象进行绑定。

---
def 是一条与赋值操作符工作方式类似的语句。执行def语句，会创建一个函数对象，同时创建一个带有指定名的对象引用。

---
sys.exit()  可以实现立即的、干净的的终止操作，关闭任何打开的文件。

---
释放内存：调用 del 变量名，会让对象的引用减一，当对象引用变为0时，会给GC回收，但是什么时候被回收是不确定的；可以调用 gc.collect() 立刻回收  （import gc）

---
list.sort()  原地排序，无返回值 
sorted(l) 不改变l，返回排好序的新的list

---
is和==的区别
is 本质上比较对象id（调用id()），即比较对象地址
== 是调用对象的`__eq__()`方法，a==b  即 `a.__eq__(b)`

---
字符串拼接：
+： 效率低，因为字符串在Python中是不可变的，所以每次执行+都会重新分配一块内存，将连接好的字符放入新内存。对于N个字符串的+运算，会分配N-1次内存和复制。
''.join([一些字符串])：join会一次申请完所需的空间。

---
write将数据写在缓冲区，并不是直接写进实际文件里，缓冲区满时操作系统会自动将缓冲区的内容写到实际文件里。调用flush可以强制刷新缓冲区，写入文件。

---
在同一行上输出   print(f"\rprocess:{percentage}\%",end="")

---
字符串前加’r‘,表示这是个raw字符串，编译器不会转义其中的 ’\‘
len('\n') == 1
len(r'\n') == 2
用在正则中可简化代码
p=’\n‘
re.search('\\n',p).group(0)  # 没加r，需要额外一个\来转义
re.search(r'\n',p).group(0)

---
f字符串是在运行时进行渲染的，因此可以将任何有效的Python表达式放入其中，比如可以放函数调用

---
搭建简单服务器共享文件
python2：`python -m SimpleHTTPServer 9999`
python3：`python -m http.server 9999`

---

str(object)、print(object)、'{}'.format(object)这些尝试将对象转换为字符串的调用，会调用object的`__str__`方法。

repr(object)或在解释器交互界面查看object时（直接输入object名称），会调用object的`__repr__`方法。

`__str__`应侧重于可读性，返回简洁的文本表示；`__repr__`应侧重于无歧义的结果，明确这是个什么模块的什么类的对象，以便开发调试。应总是添加`__repr__`方法。`__str__`方法默认下会调用`__repr__`方法。

格式化字符串支持的转换flag：`'!s'` 会对值调用 `str()`，`'!r'` 调用 `repr()`，`'!a'` 则调用 `ascii()`。

```python
class A:
    def __init__(self):
        pass
    
    def __str__(self):
        return "__str__ for A"
    
    def __repr__(self):
        return "__repr__ for A"
    
#解释器：
"{!s}".format(A())     ->   '__str__ for A'
"{!r}".format(A())     ->   '__repr__ for A'
"{}".format(A())     ->   '__str__ for A'
f'{A()!r}'      ->   '__repr__ for A'
```

---

Python是一门**强类型动态**编程语言。

强类型：比如 1+'a'会报错：`TypeError: unsupported operand type(s) for +: 'int' and 'str'`

动态：可以动态修改函数，对象类型结构，变量类型等，甚至可以动态构造代码执行,比如

```python
cmd='''
def say(name):
	print(f'hello {name}')
'''

exec(cmd)

say("DD")   #output:  hello DD

```

---

Python变量作用域LEGB：local,enclosing,global,built-in。搜索变量的优先级：L->E->G->B。

**global**声明该变量是全局变量，即模块级别定义的变量。

**nonlocal** 声明该变量是嵌套的父级函数的局部作用域（闭包Enclosing）内的变量。

---

