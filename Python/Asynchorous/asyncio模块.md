`import asyncio`

异步单线程;
调用await/async with才会发生切换协程，需要协程自己主动让出CPU。如果协程函数没有这类出让动作（await），一旦它开始它将一直执行直到执行完。

**事件循环会运行异步任务和回调，执行网络 IO 操作，以及运行子进程。**

**asyncio.get_running_loop()：**
返回当前 OS 线程中正在运行的事件循环。**此函数只能由协程或回调来调用。**
**asyncio.get_event_loop()**
获取当前事件循环。 如果当前 OS 线程没有设置当前事件循环并且 set_event_loop() 还没有被调用，asyncio 将创建一个新的事件循环并将其设置为当前循环。
**asyncio.set_event_loop(loop)**
将 loop 设置为当前 OS 线程的当前事件循环。
**asyncio.new_event_loop()**
创建一个新的事件循环。

```python
#协程怎么创建
#先用async def定义协程函数
async def coroutine_fun(args):
	#do some thing

#调用coroutine_fun()得到一个协程（对象），还没开始运行
coroutine_fun  --> 协程函数
coroutine_fun() --> 协程/协程对象

如何运行：
顶层API：
	asyncio.run(coroutine_fun())  # 注意coroutine_fun调用
或在 协程函数中：
 asyncio.create_task(coro)
    将 coro 协程 打包为一个 Task 排入日程准备执行。返回 Task 对象。
    该任务会在 get_running_loop() 返回的循环中执行，如果当前线程没有在运行的循环则会引发 RuntimeError。

async def main():
    tasks = []
    for _ in range(2):
        t = asyncio.create_task(coroutine_fun(arg))
        tasks.append(t)
    for t in tasks:
        await t
asyncio.run(main())
或
awaitable asyncio.gather(*aws, loop=None, return_exceptions=False)
	并发 运行 aws 序列中的 可等待对象。如果 aws 中的某个可等待对象为协程，它将自动作为一个任务加入日程。

async def main():
    # Schedule three calls *concurrently*:
    await asyncio.gather(
        coroutine_fun(2),
        coroutine_fun(3),
        coroutine_fun(4),
    )
asyncio.run(main())
```



