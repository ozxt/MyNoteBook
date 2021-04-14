## 通配符 wildcards

通配符
| Wildcard    | Meaning |
| --------- | -----------   |
| *   | 匹配任意多个字符 |
|   ?   |   匹配任一单个字符   |
| [characters] | 匹配characters集合里任一字符 |
| [!characters] | 匹配没出现在characters集合里的任一字符 |
| [[:class:]] | 匹配特定字符类的任一字符 |

字符类

| Character Class | Meaning          |
| --------------- | ---------------- |
| [:alnum:]       | 匹配任一字母数字 |
| [:alpha:]       | 匹配任一字母     |
| [:digit:]       | 匹配任一数字     |
| [:lower:]       | 匹配任一小写字母 |
| [:upper:]       | 匹配任一大写字母 |

实例
```shell
*       所有文件
g*        任何以’g‘开头的文件
[anj]*        任何以’a‘或’n‘或’j‘开头的文件
[[:lower:]]*       任何以小写字母开头的文件
foo.[0-9][0-9][0-9]       任何以'foo.'开头，以三个数字结尾的文件
```



## shell的Expansion

shell会在命令运行前自动把可扩展的字符进行扩展。

```shell
# 1 路径扩展 pathname expansion
# 通配符就是这类扩展

# 2 波浪号扩展 tilde expansion
# ‘~’ 会被扩展成用户的home目录路径

# 3 算术扩展 arithmetic expansion
# 形式： $((算术表达式))
# + - * / % **()
# echo $((2+2))   ---> 4
# echo $(($((5**2)) * 3)) --> 75

# 4 大括号扩展 brace expansion
# echo 0-{A,B,C}-0  ---> 0-A-0 0-B-0 0-C-0
# echo {01..11} ---> 01 02 03 04 05 06 07 08 08 09 10 11
# echo 0{A{1,2},B{3,4}}0 ---> 0A10 0A20 0B30 0B40

# 5 参数扩展 parameter expansion
# 扩展变量  $USER $HOME 这类

# 6 命令替换  command substitude
# $(命令) 或 `命令`
# ls -l $(which cp)
# ls -l `which cp`

# 7 引号
# 双引号
# 双引号会抑制掉引号内的路径扩展，波浪号扩展，大括号扩展，单词分隔（保留空白符），而$，\,``都还有效（算术扩展，命令替换，参数扩展）
# 对分词的影响：不加引号时，shell视空格，tab，换行符为分隔符，不当作文本的一部分。加双引号，抑制分词，保留这些空白符。
# echo $(cal)
# --->
# 七月 2020 日 一 二 三 四 五 六 1 2 3 4  5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31
# echo "$(cal)"
# --->
#        七月 2020         
# 日 一 二 三 四 五 六  
#           1  2  3  4  
# 5  6  7  8  9 10 11  
# 12 13 14 15 16 17 18  
# 19 20 21 22 23 24 25  
# 26 27 28 29 30 31  
# 单引号
# 单引号抑制所有类型的扩展。
# echo '$(cal)' ---> $(cal)

# 8 转义字符
\\ ---> \
\$ ---> $
\t  ---> tab字符
在echo中选项-e使能解释转义字符
```

## 变量

`set`可以输出所有环境变量和自定义变量

#### 自定义变量

```shell
# 等号两边不能有空格，是紧挨者的
# 若字符串有空格，应该用引号括住(引号不会作为内容保存在变量里)
varname=“typora$HOME”  # 双引号括住，里面的变量和命令会被解析；里面出现双引号引号，要转义。
varname='zheshi$HOME'  # 单引号括住，里面的变量/命令不会解析，保持不变。里面不能出现单引号，即使转义也不行。
varname=asddsfsg  # 也可以不用引号，但若有空格，空格后面的字符串会被作为命令/变量解析
varname=`echo $HOME`  # 反引号括住命令
varname=$(echo $HOME)  # $() 同反括号
varname=9

# 引用变量
echo $varname
echo ${varname}  # 最好用这种方式
# 变量是字符串时，可以用‘’或“”括住
echo "${varname}"
# “”
```



#### 改变IFS分隔符

查看IFS的值 `set | grep -i 'ifs'`

IFS=$'\n'  #默认分隔符有空格，若文件名内有空格，会遍历错误

> \#$'\n' 表示换行符
> 	\# https://stackoverflow.com/questions/13925879/what-does-n-mean-in-bash
> 	\# What does $'\n' mean in bash?  

IFS="
"   #这两种都可以，第一种是bash特有的

#### 特殊变量

`./test.sh v1 v2`

$0  当前执行脚本的名字，若带路径调用该脚本，则包含该路径。"./test.sh"

$n 执行脚本时输入的第n个参数

$#  参数数目（不包含脚本命令名）

$*  所有传入的参数

$@   所有传入的参数

```shell
#!/usr/bin/bash

echo '$*'  #会拆开空格
for v in $*
do
	echo "${v}"
done
echo "##############################################"

echo '$@'  #会拆开空格
for v in $@
do
	echo "${v}"
done
echo "##############################################"

echo '"$@"'  #  这可能是你想要的，传入多少参数就是多少，不会拆开空格
for v in "$@"
do
	echo "${v}"
done
echo "##############################################"

echo ''$@''  # 把所有传入参数当成一体
for v in '$@'
do
	echo "${v}"
done

```

```
ozxt@ozxt-ubuntu:~/tmp$ ~/script/test.sh QQ WW "VV   HH"
$*
QQ
WW
VV
HH
##############################################
$@
QQ
WW
VV
HH
##############################################
"$@"
QQ
WW
VV   HH
##############################################
QQ WW VV HH
$@

```



$?  上一个命令的返回结果状态码(0成功)

$$  当前执行脚本的PID

#### 字符串操作

使用模式剪裁字符串：贪婪，非贪婪。（都是将匹配到的部分删除）

```shell
${var#pattern} 从字符串的开头开始非贪婪匹配
${var##pattern} 从字符串的开头开始贪婪匹配
${var%pattern} 从字符串的末尾开始非贪婪匹配
${var%%pattern} 从字符串的末尾开始贪婪匹配
```

```shell
p="home/ozxt/tmp"
${p#*/}  ---->     ozxt/tmp
${p##*/} ---->     tmp
${p%/*}  ---->     home/ozxt
${p%%/*} ---->     home
```

使用索引截取字符串

```shell
p="abcdefghij"  
# 从左到右索引从0开始，即a字符的索引为0
#从右到左索引从-1开始，即j字符的索引为-1
echo ${p:0}  --> abcdefghij
echo ${p:1}  --> bcdefghij
echo ${p:2:3}  --> cde
echo ${p: -2}  --> ij  # 注意p:后有一空格
echo ${p: -4:2}  --> gh
```

获取字符串长度

`${#varstr}`

字符串拼接

`把字符串，变量放在一起就可以；若有空格需加双引号括住`

## 函数

函数需**先定义后使用**，需放在调用地方前面

**函数定义**：

```shell
# []内可以不带
[function] fname[()]
{
	# 在函数内部这样定义的变量是全局的，当调用函数后，可以访问到
	v="ABC"
	# 这样定义函数局部变量，出了函数就访问不到了
	local lv="LOCAL"
	
	#some code
	[return integer]  # 返回范围0-255。没有return语句的话，返回最后一条命令的结果
} 
```

**函数调用**

```shell
fname var1 var2...
```

在函数内部通过$1,$2...来引用传入的参数,分别代表第一，第二...个参数。

函数返回值通过`$?`来取得

或者 `r=$(fname v1 v2)`可以取得返回值

## 程序结构

if

```shell
if condition
then
	# do something
fi
```

for

```shell
for i in seq
do
	# do something
done
```

while

```shell
while condition
do
    # do something
done
```

```shell
循环执行指令：
while true; do netstat -ntp | grep 5000 ;sleep 0.5; done
```



条件判断


```shell
用方括号括住条件判断，两边须留空格 [ 2 -gt 1 ]
# 数字
-eq  -ne  -lt -gt -le  -ge  用于比较数字:
# 字符串
!= = 	用于比较字符串
str  	直接引用字符串，非空时为真
-z str 	字符串空时为真
# 逻辑运算
-a -o ! 与或非
#文件
-f file　　　　　文件为普通文件为真
-d file　　　　　文件为目录为真
-r file　　　　　用户可读为真
-w file　　　　　用户可写为真
-x file　　　　　用户可执行为真
-c file　　　　　文件为字符特殊文件为真
-b file　　　　　文件为块特殊文件为真
-s file　　　　　文件大小非0时为真
-t file　　　　　当文件描述符(默认为1)指定的设备为终端时为真
```



## 命令

#### awk

awk脚本基本结构
**`awk 'BEGIN{ print "start" } pattern{ commands } END{ print "end" }' file`**
一个awk脚本通常由：BEGIN语句块、能够使用模式匹配的通用语句块(可有多个pattern块)、END语句块3部分组成，这三个部分是可选的。任意一个部分都可以不出现在脚本中，脚本通常是被单引号或双引号中

awk 'BEGIN{ commands } pattern{ commands } END{ commands }'

第一步：执行BEGIN{ commands }语句块中的语句；
第二步：从文件或标准输入(stdin)读取一行，然后执行pattern{ commands }语句块。重复这一步，直到读取完毕。
第三步：当读至输入流末尾时，执行END{ commands }语句块。

BEGIN语句块在awk开始从输入流中读取行之前被执行，这是一个可选的语句块，比如变量初始化、打印输出表格的表头等语句通常可以写在BEGIN语句块中。

END语句块在awk从输入流中读取完所有的行之后即被执行，比如打印所有行的分析结果这类信息汇总都是在END语句块中完成，它也是一个可选语句块。

pattern语句块中的通用命令是最重要的部分，它也是可选的。如果没有提供pattern语句块，则默认执行{ print }，即打印每一个读取到的行，awk读取的每一行都会执行该语句块。

```shell
#特殊变量
NR：表示记录数。对应当前行号。
NF：表示字段数。对应执行当前行所含字段数。
$0:执行当前行的整行内容。
$N: N>0。当前行第N个字段内容。

getline: 读取一行，可用$0,$N去访问该行的内容。

awk默认字段定界符为空格，可用-F指定定界符
awk -F: '{ print $NF }' /etc/passwd  #-F:指定以:为字段定界符，$NF指定最后一个字段

用在pattern:
awk 'NR<5' #行号小于5的行
awk 'NR==1,NR==5' # 行号在1和5之间的行
awk '/linux/'  # 包含‘linux’字符串的行
awk '！/linux/'  # 不包含‘linux’字符串的行
```

```shell
ls -lS --time-style=long-iso | awk 'BEGIN{
    getline;
    getline;
    name1=$8;
    size1=$5;
}
{
    name2=$8;
    size2=$5
    if ( size1==size2 )
    {
        "md5sum $name1" | getline; csum1=$1;
        "md5sum $name2" | getline; csum2=$1;
        if(csum1==csum2)
        {
            print name1;
            print name2;
        }
    };
    size1=size2;
    name1=name2;
}' 
```

#### basename和 dirname

`basename` 从路径中提取文件名

```shell
basename /home/ozxt/tmp.txt  -->  tmp.txt
# 指定一个后缀，可以提取不带后缀的文件名
basename /home/ozxt/tmp.txt .txt  -->  tmp
```

`dirname` 从路径中提取目录名

```shell
# 提取文件所在目录
dirname /home/ozxt/tmp.txt  --> /home/ozxt
# 提取目录所在目录
dirname /home/ozxt  --> /home
```

#### md5sum

计算文件md5值

`md5sum filename`

#### cut

按列提取行信息

```shell
-d 指定字段的分隔符，默认的字段分隔符为“TAB”；(必须是单个字符)
-f 提取指定字段的内容； -f1,3 第一，三个字段
-c 按字符分段；
-b 按字节分段；
```

#### grep


#### exit

`exit status_code  #退出脚本，返回一个0-255的数字 `

#### eval

构造命令

举例：`eval find $opt`

#### find
`find [path] [option] [action]`

```shell
# 找出
find /home -name "*.txt"  # /home目录下以“.txt”结尾的文件
find /home -iname "*.txt"  # 同上，但忽略大小写
```

#### uname [-asrmpi]

查看系统与内核相关信息

`uname -a`  # 所有相关信息

#### uptime

系统启动时间与工作负载

#### netstat

网络跟踪

```shell
-t 列出tcp数据包
-u 列出udp数据包
-l 列出正在监听的服务
-p 列出服务的PID
-n 不显示进程服务名，而是以端口号来显示
-a 列出所有连接，监听，Socket数据

netstat -lntp #查看打开的端口 
netstat -ltun #查看监听的端口
netstat -nat  #查看TCP各个状态的数量

查看连接某服务端口最多的的IP地址：
netstat -ntu | grep :80 | awk '{print $5}' | cut -d: -f1 | awk '{++ip[$1]} END {for(i in ip) print ip[i],"\t",i}' | sort -nr

```

#### date
日期时间
```shell
date +%Y-%m-%d  # 当日时间 如2020-02-26
```

#### xargs
传递参数
```shell
-n 指定一次传递几个参数
-p 每一次构造运行命令都提示
-d 指定分隔符
-a 从文件读入参数

ls | xargs rm  # 删除当前目录下的文件
ls | xargs rm -rf # 删除当前目录下的文件和目录
ls | xargs -n1 -d "\n" -p unar # 解压目录下所有文件
```

#### ln
创建链接（软/硬）
```shell
# 创建硬链接
ln file link
# 创建软链接(符号链接)
ln -s file link
```

硬链接：默认下，每个文件都有一个硬链接。当我们创建硬链接时，我们是**create an addtional directory entry for a file** （硬链接和被引用文件是同一个inode），它有两个特点：1，硬链接不能链接非同一文件系统的文件，就是不能在A分区创建硬链接引用B分区的文件。2，硬链接不能引用目录。**修改硬链接等同于修改被引用文件,删除被引用文件，链接依然有效**

软链接：软链接使用了一个特殊类型的新文件，该文件包含指针指向被链接的文件或目录。（有点像win的桌面快捷方式）。**修改软链接就是修改被引用文件，删除被引用文件，链接就会失效***

#### gzip
解压缩

```shell
gzip file    # 压缩, 得到file.gz, file消失
gunzip file.gz # 解压, 得到file, file.gz消失
gzip -d file.gz # 解压,同gunzip
-f # 强制压缩,即便已有一个压缩版本存在
-l # 列出压缩包里的文件
-t # 测试压缩包的完整性
-v # 显示压缩信息
```

#### bzip
解压缩

```shell
	bzip2 file  # 压缩, 得到file.bz2,file消失
	bunzip2 file.bz2 # 解压
```

#### tar

打包

```shell

```

#### tr

对输入流的字符进行替换，压缩，删除。

```shell
-s # 把连续重复的字符以单独一个字符表示

例子
echo dd0d0ddd | tr -s d  # 输出：d0d0d

```


#### sed
流编辑器

```shell
-i 将处理后的文件写入原文件
-n 或--quiet或--silent  仅显示处理后的结果
-f 指定一个script文件读入模式

命令
s/regex/repl/ 替换（行的第一个匹配）
s/regex/repl/g 替换全部（行的全部匹配）
g  表示行内全面替换 sed 's/a/A/g' file
p  打印行    sed 'p' file
w  写入一个文件   's/a/A/w wfile'

例子
sed -n 1p  # 打印输入流第一行
sed -n 2,4p  # 打印输入流第2到4行


```

#### du
显示目录大小
```shell
-m    以MB为单位
-k    以GB为单位
--max-depth  表示只深入到第n层目录。设置为0时表示不深入到子目录。


du -h --max-depth 1    显示当前目录下的目录大小
```

#### sort
排序
```shell
-n 按数值的大小排序
-r 以相反的顺序输出（默认增序）
-u 排序并去重
```

#### uniq
去重（相邻行去重，所以去重前须排序）
```shell
-n 按数值的大小排序
-r 以相反的顺序输出（默认增序）
-u 排序并去重
-w 指定一个数字N，比较不超过N个字符

echo "aab\naaa" | uniq -w 2  # output:aab 
```

#### comm
比较两个文件
```shell
comm fileA fileB #输出N行3列，从左到右，第一列为只在fileA出现的行，第二列为只在fileB出现的行，第三列为交集
comm fileA fileB -1 -2 -3 #调整输出，-N为不输出第N行
```

#### rsync
备份
```shell
rsync -av source destination  # 把source目录(目录本身)递归地复制到destination。
rsync -av source/ destination  # 把./source目录下的内容递归地复制到destination。
rsync -av source/ username@remote_host:destination # 远程同步
--delete  # 删除只存在于目标目录、不存在于源目录的文件。
--exclude "*.txt"  # 添加该选项指定通配符以排除备份。该例不对以.txt结尾的文件备份。
--exclude-from pattern_file  #或者指定一个列表文件，内里指定要排除的文件# 
-r # 表示递归子目录
-a # 可替代-r，除了递归同步外，还会同步元信息（比如修改时间，权限）。
```

## 常用命令

```shell
# 从ps aux中提取pid
ps aux | grep process_name | tr -s ' '| cut -d ' ' -f 2
```