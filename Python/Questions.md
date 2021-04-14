## Q1: What does the if __name__ == "__main__": do ?

https://stackoverflow.com/questions/419163/what-does-if-name-main-do
python解释器读取源文件时，它会做两件事：
1，它会设置一些特殊的变量,像`__name__`,然后
2，它执行文件里的代码

1.特殊变量
当我们运行 python  so.py时，解释器会给`__name__`设为`"__main__"`。就像在文件顶部写
`__name__ = "__main__"`
当so.py被作为模块被so.py导入（import so）时，解释器就会像在被导入的模块文件的顶部给`__name__`设为模块名
`__name__ = "so"`
2.执行代码
当特殊变量设置好后，解释器开始执行文件里的代码，一次一条语句。

---








