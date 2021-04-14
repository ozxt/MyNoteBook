## python 线程
threading.RLock 可重入锁
可以被同一进程获取acquire多次，锁的获取和释放需要成对。

```python
import threading

#继承threading.Thread来使用多线程
class Client(threading.Thread):
    # 要重写threading.Thread的__init__和run（必须）方法，其余的不能重写。可以添加自己的方法
    def __init__(self,a):
        super(Client, self).__init__()
        self.a = a

    def run(self):
        print(self.a)


c = Client(2)
c.start()
c.join() #主线程会等待到子线程（此处为子线程c）结束再往下运行

# 或

def func(a):
    print(a)

t = threading.Thread(target=func, args=(9,)) # args若只有一个参数需要加一个逗号,以示tuple
t.start()
t.join()
```