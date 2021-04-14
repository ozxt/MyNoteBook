# markdown 笔记

[TOC]

## 基本语法

### Heading标题

#### 放置‘#’在文字开头来表示一个标题，有6个level，对应‘#’的数目，越多字号越小

### Level2

### Level3

#### Level5

###### level6

>Heading level 1  可以在文字下方使用‘=’表示level 1的标题
>=====================

>或者用‘-’表示level 2
---------------------------------------

>---

### 段落

#### 段落之间用空行隔开，不要使用空格或tabs去缩进段落。段内换行使用两个空格加Enter键。

一个段落
I really like using Markdown.  
I think I'll use it to format all of my documents from now on. 

vs
两个段落
I really like using Markdown.

I think I'll use it to format all of my documents from now on. 

---

### 强调
#### 斜体：在要加粗的文字两端各加一个‘\*’或‘\_’。建议用‘\*’。
飞流直下*三千尺*，疑是银河落 _九天_ 。
#### 加粗：在要加粗的文字两端各加两个‘\*’或‘\_’。建议用‘\*’。
飞流直下**三千尺**，疑是银河落 __九天__ 。
#### 斜体+加粗：在要加粗的文字两端各加三个‘\*’或‘\_’。建议用‘\*’。
飞流直 ***三千尺***，疑是银河落 ___九天___ 。

---

### 引用

#### 在句子前加一个‘\>’
> Dorothy followed her through many of the beautiful rooms in her castle.
#### 可以一起引用多个段落，需要在段落间的空行加'\>'
> Dorothy followed her through many of the beautiful rooms in her castle.
>
> The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.
#### 嵌套引用： 增加'\>'
> Dorothy followed her through many of the beautiful rooms in her castle.
>
> > The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.
> > > The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.
> > >
> > > > The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.
>
> The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.



### 列表
#### 有序列表：用数字加'\.'表示，必须从1开始
按顺序写编号
1. First item
2. Second item
3. Third item
4. Fourth item   

不按顺序写编号
1. First item
3. Second item
3. Third item
  1. DF
  2. ADS
4. Fourth item 

#### 无序列表：使用‘\-’ '\*' '\+'
- First item
+ Second item
* Second item
  - AA
  - BB
- Third item
  使用缩进可以添加其他元素
  > 缩进添加 引用
    <html>
  
          <head>
            <title>Test</title>
        </head>
  
- Third item

  ```
  ![Tux, the Linux mascot](/assets/images/tux.png)
  ```

---

### 代码

#### 使用反引号‘\`’来括住代码（如果代码本来含有反括号，就用两个反括号括住）
`print（“markdown”）`

``re.sub(pattern, rep, string)``

```s.strip()```

#### 使用三引号括住整段代码

```python
def getji():
    r = []
    with open("ji.dat", 'r') as f:
      for line in f.readlines():
        line = line.strip()
        if line and not line.startswith('#'):
          r.append(line)
    return r[::]
```
```C
int main(){
  pritnf("hello world!");
  return 0;
}
```

---

### 水平标尺线

#### 使用三个‘\-’或‘\*’或‘\_’。建议用'\-'

---

### 链接

#### 用方括号[]括住链接名，紧随其后接圆括号()括住链接url

[dogedoge](https://dogedoge.com)

#### 可以为链接添加小提示，当用户鼠标悬浮其上显示

[dogedoge](https://dogedoge.com "sousuo")

#### 也可以直接加角括号<>直接显示链接
<https://www.markdownguide.org>
<fake@example.com>

---

### 图片

#### 添加图片格式： ![图片的替换文字](图片的存储路径或url “鼠标悬浮时的提示”)

#### 可以给图片添加点击跳转的链接
[![An old rock in the desert](/assets/images/shiprock.jpg "Shiprock, New Mexico by Beau Rogers")](url)

---

### 字符转义

#### 可用'\\'转义以下特殊字符 

> \ ` *  _  {}  []  ()  # + - . ! |

---

## 扩展语法

### 表格

单元格用'\|'括住，表头下方使用'|---|---|....'('\-'需要三个以上，'|'不需要对齐)
| Syntax    | Description |
| --------- | -----------   |
| Header    | Title             |
|     Paragraph   |   Text   |

对齐调整，通过添加':'实现

| Syntax    | Description | Test Text |
| :--- | :---: | ---: |
| 左对齐    |   中对齐    |  右对齐 |
| Paragraph |    Text     |  And more |

---

### 脚注

一只麻雀[^1]从我眼前飞[^word]过。

[^1]: 麻雀：一种常见的鸟类。
[^word]: 飞，动作。
