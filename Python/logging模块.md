logging模块可以指定日志记录器的日志级别，**只有级别大于或等于该指定日志级别的日志记录才会被输出，小于该等级的日志记录将会被丢弃**。

以下是logging模块默认的级别定义，每个级别都有一个数值对应，用户可以通过不同的数值自定义日志级别。和默认级别数值相同时会覆盖默认级别定义。

| Level    | Numeric value |
| --------- | ----------- |
| CRITICAL    | 50       |
| ERROR | 40        |
| WARNING | 30        |
| INFO | 20        |
| DEBUG | 10        |
| NOTSET | 0        |



```python
import logging
dstlogger = logging.getLogger("dst_logger")   #获取logger对象
dstlogger.setLevel(logging.DEBUG)  #设置该logger的日志级别
formatter_dstlog = logging.Formatter("%(asctime)s 【%(levelname)s】 %(message)s")
fh = logging.FileHandler("dst.log", encoding='utf-8', mode='w')
fh.setLevel(logging.DEBUG) #handler可以再设置日志级别，以这个级别为准
fh.setFormatter(formatter_dstlog)
dstlogger.addHandler(fh)
sh = logging.StreamHandler()  # 输出到控制终端
dstlogger.addHandler(sh)
```



```python
logging.getLogger(name=None)  若name为None，则返回root logger
All calls to this function with a given name return the same logger instance. This means that logger instances never need to be passed between different parts of an application.
对于同样name，调用getLogger返回同一个logger object

logging.debug/info/waring/error/critical/exception 这些使用的是root logger
```



```python
fh1 = logging.FileHandler("fh1log", encoding='utf-8',mode='w')
formatter = logging.Formatter("%(asctime)s 【%(levelname)s】 %(message)s")
fh1.setLevel(logging.INFO)
fh1.setFormatter(formatter)

fh2 = logging.FileHandler("fh2log", encoding='utf-8',mode='w')
fh2.setLevel(logging.WARNING)
fh2.setFormatter(formatter)

#handlers参数必须是iterable 
logging.basicConfig(level = logging.INFO,handlers=[fh1,fh2])


logging.info("INfO 测试 ")
logging.debug("DEBUG 测试 ")
logging.warning("WARNING 测试")
logging.error("ERROR 测试")
```

