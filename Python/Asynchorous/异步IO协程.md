  Python 3.4新增了一种更适合IO密集型应用的特性“异步IO”（Asynchronous I/O），在不开多进程或多线程的情况下也能实现多任务并发。简单来说，当程序发起一个普通IO操作时，它会“阻塞”（Block）当前任务的执行直到操作结束，而所谓异步IO就是不等待IO操作结束就继续执行，这需要创建一个内部事件循环来轮番处理所有任务的状态。异步IO调度任务的基本单元称为“协程”（Coroutine）——函数之类的程序构件可统称为子程序或“例程”（Routine），一般都是从起点进入从终点退出；而协程是一种特殊例程，在进入后可以多次中断转往其他操作再返回（其实就是之前介绍过的生成器），这样就能有任意多个任务在事件循环中被切换执行了。

通过异步IO实现多任务并发需要引入标准库的asyncio模块并使用async/await语句：

1. async def 定义协程函数用来返回协程对象/async with 指定异步上下文管理器用来生成可等待对象。
2. asyncio.create_task()/.gather() 将一个/多个协程打包为任务排入计划日程。
3. await 指定任务或其他可等待对象等待其完成并返回执行结果。
4. 在主程序中获取事件循环来运行作为顶层入口的协程函数。



```python
"""xwork_webcrawler.py 协程版百度图片搜索并批量下载
https://www.jianshu.com/p/3faf1c3c198e
"""
import asyncio
import aiohttp
from urllib.parse import quote
import os
import re
import time
url = "https://image.baidu.com/search/flip?tn=baiduimage&word="
keyword = "CG原画"
folder = "img"
path = os.path.abspath(".")
headers = {
    'User-Agent':
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:61.0) \
            Gecko/20100101 Firefox/61.0'
}


async def fetch(session, url):
    async with session.get(url, timeout=20) as res:
        return await res.text()


async def store(session, url):
    t1 = time.perf_counter()
    async with session.get(url, timeout=20) as res:
        with open(os.path.join(path, folder, url.split("/")[-1]), "wb") as f:
            while True:
                chunk = await res.content.read(512)
                if not chunk:
                    break
                f.write(chunk)
    print(f"保存图片{url}耗时{time.perf_counter() - t1}秒。")


async def main():
    if not os.path.exists(folder):
        os.mkdir(folder)
    async with aiohttp.ClientSession(headers=headers) as session:
        t1 = time.perf_counter()
        html = await fetch(session, url + quote(keyword))
        links = re.findall(r'"objURL":"(.+?)"', html)
        print(f"提取文本耗时{time.perf_counter() - t1}秒。")
        for i in links:
            await asyncio.create_task(store(session, i))


if __name__ == "__main__":
    t1 = time.perf_counter()
    try:
        loop = asyncio.get_event_loop()
        loop.run_until_complete(main())
    except Exception as e:
        print(repr(e))
    print(f"主程序耗时{time.perf_counter() - t1}秒。")
```

