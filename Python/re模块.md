#### re.search("模式串"，“目标串”， *flags=0*)   

- 没匹配到返回None，匹配到话返回一个Match Object，可以用group()拿到匹配串，group()或group(0)返回整个匹配串；group(1)返回第一个匹配分组，依此类推。
- 匹配串可以出现在任意位置，仅返回从左往右扫描到的第一个匹配。
- 模式串为""（0长）时，返回 “”

#### re.match("模式串"，“目标串”， *flags=0*)

- 从目标串的开始位置匹配，没有的话返回None， 有的话返回Match Object，可以用group()拿到匹配串，group()或group(0)返回整个匹配串，group(1)返回第一个匹配分组，依此类推。
- 模式串为""（0长）时，返回 “”

#### re.findall("模式串"，"目标串"， *flags=0*)

- 以字符串列表形式返回所有匹配结果，没有则返回空列表。
- 若有分组，有一个分组的话，返回分组匹配字符串的列表；多个分组的话，返回以所有分组的字符串组成的tuple的list

```python
>>> re.findall("a(s)d","asdssssssasdasdassd")
['s', 's', 's']
>>> re.findall("asd","asdssssssasdasdassd")
['asd', 'asd', 'asd']
>>> re.findall("k","asdssssssasdasdassd")
[]
>>> re.findall("a(s)(d)","asdssssssasdasdassd")
[('s', 'd'), ('s', 'd'), ('s', 'd')]
```

#### re.finditer("模式串"，"目标串"， *flags=0*)

返回一个iterator迭代器对象，yield 所有匹配到的Match Objects，调用next（）或for迭代，yield完会抛出StopIteration异常。

#### re.compile("模式串",flag=0)

- 把模式串编译成一个Regular Expression Objects（re.Pattern）对象，方便重复使用。该对象可以使用match，search，findall，finditer等等方法，行为和re.match类似，只是不需要在传入模式串，返回也相同。

```pyt
prog = re.compile(“模式串”)
result = prog.match(“目标串”)
```

- 通过flag可以改变匹配的行为细节。多种flag可以用  |  连接，来使多种flag生效。 re.S（re.DOTALL）可以让"."匹配换行符。

#### re.sub(模式串，替换串，目标串,,count=0,flags=0)

返回替换后的字符串，count不指示或为0时替换全部匹配。