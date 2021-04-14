# python 文件操作
## open
```python
open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)
打开 file 并返回对应的 file object。如果该文件不能打开，则触发 OSError。
r    读取（默认）
w    写入，并截断文件（覆盖）
x    排它性创建，如果文件已存在则失败
a    写入，如果文件存在则在末尾追加
b    二进制模式
t    文本模式（默认）
+    打开用于更新（读取和写入）
以二进制模式打开文件，读取时返回bytes对象，不进行解码。以文本模式打开，会以指定编码encoding或系统默认编码进行解码。
encoding参数用于指定解码或编码文件的编码的名称。只应于文本模式下使用。
newline 参数控制 universal newlines 模式如何生效（它仅适用于文本模式）。它可以是 None，''，'\n'，'\r' 和 '\r\n'。它的工作原理:
    从流中读取输入时，如果 newline 为 None，则启用通用换行模式。输入中的行可以以 '\n'，'\r' 或 '\r\n' 结尾，在返回给调用者之前这些行被翻译成 '\n' 。如果它是 ''，则启用通用换行模式，但行结尾将按原样返回给调用者。
    将输出写入流时，如果 newline 为 None，则写入的任何 '\n' 字符都将转换为系统默认行分隔符 os.linesep。如果 newline 是 '' 或 '\n'，则不进行翻译。
```


### 文件/文件夹是否存在 
```python
os.path.exists(filename/dirname)  # 存在True，不在False
```
### 文件复制
```python
import shutil
shutil.copy(source_name, target_name)
```
### 文件名后缀
```python
os.path.splitext(path)  # 得到(root,ext)    D:\A\B\cc.txt --> ("D:\A\B\cc", ".txt")
```
### 拼接路径
```python
os.path.join(path, *paths)
```
### 通配文件
```python
glob.glob("*.py") #列出目录下所有文件名以.py结尾的文件
```
### 创建目录
```python
os.mkdir("./a/b/c") #在./a/b目录下创建c目录，如果./a/b路径不存在，会引发FileNotFoundError.
os.makedirs("./a/b/c/d") # 创建目录d，如果中间目录不存在会递归创建。有个参数exist_ok,默认值False，当目标目录存在会引发FileExistsError。
```