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

echo '"$@"'  #  传入多少参数就是多少，不会拆开空格
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

v=abcdabcdefg
echo ${v#*d}  # abcdefg
echo ${v##*d}  # efg
echo ${v%d*}  # abcdabc
echo ${v%%d*}  # abc
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

## 数组

```shell
一，普通数组（索引-值。索引为整数，从0开始）
定义：
array=(1 2 3 4 5)
或
array[0]=1
array[1]=1
array[2]="qq"

使用：
${array[1]}
打印数组中所有的值： 
echo ${array[*]}
或 echo ${array[@]}
数组长度：
${#array[*]}

二，关联数组（索引可以是任意文本）
定义：
先声明
declare -A sarray
后
sarray=([key1]=val1 [key2]=val2 [key3]=val3)
或
sarray[key1]=val1
sarray[key2]=val2
sarray[key3]=val3

使用：
echo ${sarray[key1]}
获取所有索引：
${!sarray[*]}
或  ${!sarray[@]}
获取所有值：
${sarray[*]}
或  ${sarray[@]}
数组长度：
${#sarray[*]}
```



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
if condition;
then
	# do something
fi
```

for

```shell
for i in seq;
do
	# do something
done
```

while

```shell
while condition;
do
    # do something
done
```

```shell
循环执行指令：
while :; do netstat -ntp | grep 5000 ;sleep 0.5; done
```



条件判断


```shell
用方括号括住条件判断，两边须留空格 [ 2 -gt 1 ]
比较字符串时最好用双中括号[[ $str1 = $str2 ]]
比较还可以用test命令  test $var -eq 1
# 数字
-eq  -ne  -lt -gt -le  -ge  用于比较数字:
# 字符串
!= = 	用于比较字符串
str  	直接引用字符串，非空时为真
-n str  字符串非空时为真
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

#### basename

`basename` 从路径中提取文件名

```shell
basename /home/ozxt/tmp.txt  -->  tmp.txt
# 指定一个后缀，可以提取不带后缀的文件名
basename /home/ozxt/tmp.txt .txt  -->  tmp
```


#### bzip
解压缩

```shell
	bzip2 file  # 压缩, 得到file.bz2,file消失
	bunzip2 file.bz2 # 解压
```


#### comm
比较两个文件
```shell
comm fileA fileB #输出N行3列，从左到右，第一列为只在fileA出现的行，第二列为只在fileB出现的行，第三列为交集
comm fileA fileB -1 -2 -3 #调整输出，-N为不输出第N行
```


#### cut

按列提取行信息

```shell
-d 指定字段的分隔符，默认的字段分隔符为“TAB”；(必须是单个字符)
-f 提取指定字段的内容； -f1,3 第一，三个字段
-c 按字符分段；
-b 按字节分段；
```

#### date

在类Unix系统中，日期被存储成一个整数，其大小是自时间标准时间（UTC）1970年1月1日0时0分0秒起所流逝的秒数。这个值称为纪元时或Unix时间。

```shell
date   # 日期 2021年 05月 12日 星期三 15:02:55 CST
date +%s    # 打印纪元时  1620803172
date --date "Thu Nov 18 08:07:21 CST 2010" +%s    # 将日期转换成纪元时
格式化日期：
date "+%d %B %Y"
```

| 日期内容               | 格式                            |
| ---------------------- | ------------------------------- |
| 星期                   | %a（例如Sat）%A(例如Saturday)   |
| 月                     | %b（例如Nov）%B（例如November） |
| 日                     | %d（例如31）                    |
| 固定格式日期(mm/dd/yy) | %D（例如10/18/21）              |
| 年                     | %Y（例如21）%y（例如2021）      |
| 小时                   | %H或%I（例如08）                |
| 分钟                   | %M（例如34）                    |
| 秒                     | %S（例如45）                    |
| 纳秒                   | %N                              |
| Unix纪元时（秒）       | %s                              |


#### dirname

`dirname` 从路径中提取目录名

```shell
# 提取文件所在目录
dirname /home/ozxt/tmp.txt  --> /home/ozxt
# 提取目录所在目录
dirname /home/ozxt  --> /home
```


#### du
显示目录大小
```shell
-m    以MB为单位
-k    以GB为单位
--max-depth  表示只深入到第n层目录。设置为0时表示不深入到子目录。


du -h --max-depth 1    显示当前目录下的目录大小
```


#### exit

`exit status_code  #退出脚本，返回一个0-255的数字 `


#### eval

构造命令

举例：`eval find $opt`


#### find
`find [path] [option] [action]`

```shell
find . -name *.txt -print # -print指明打印出匹配的文件，用'\n'进行分隔
find . -name *.txt -print0 # 用'\0'进行分隔
find /home -name "*.txt"  # /home目录下以“.txt”结尾的文件
find /home -iname "*.txt"  # 同上，但忽略大小写
find . \( -name "*.txt" -o -name "*.py" \) # or
find /home ! -name "*.txt"  # /home目录下不是以“.txt”结尾的文件
-maxdepth n # 指定最大搜索深度 1是当前目录，3是从1搜索到3级目录
-mindepth n  # 指定开始的最小深度 3是从第三级开始搜索
-type f # 文件类型：普通文件
-type l # 文件类型：符号链接
-type d # 文件类型：目录
-type c # 文件类型：字符设备
-type b # 文件类型：块设备
-type s # 文件类型：套接字
-type p # 文件类型：FIFO
-atime # 用户最近一次访问文件的时间，单位：天
-mtime # 用户最近一次修改文件内容的时间，单位：天
-ctime # 文件元数据（权限或所有权）最近一次改变的时间，单位：天
-atime 8  # 用户恰好在8天前访问过文件
-atime +8 # 用户访问文件的时间超过8天
-atime -8 # 用户访问文件的时间在最近8天内
-amin,-mmin,-cmin 同上但是单位是分钟
-szie 1k # 大小等于1k的文件
-size +1G # 大小超过1G的文件
-size -200M # 大小小于200M的文件
文件大小单元： -b 块（512字节） -c 字节 -w 字（2字节） -k 1024字节 -M 1024K字节 -G 1024M字节
-delete 删除匹配的文件
find . \( -name "env" -prune \) -o \( -name "*.py" -print \) #-prune 跳过env目录
```


#### grep


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


#### md5sum

计算文件md5值

```shell
md5sum filename
md5sum -c file.md5sum  # 检查是否一致
```


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


#### rsync
备份
```shell
rsync -av source destination  # 把source目录(目录本身)递归地复制到destination。
rsync -av source/ destination  # 把./source目录下的内容递归地复制到destination。
rsync -av source/ username@remote_host:destination # 远程同步
--delete  # 删除只存在于目标目录、不存在于源目录的文件。
--exclude "*.txt"  # 添加该选项指定通配符以排除备份。该例不对以.txt结尾的文件备份。
--exclude {"*.txt","*.cache","tmp"}
--exclude-from pattern_file  #或者指定一个列表文件，内里指定要排除的文件# 
-r # 表示递归子目录
-a # 可替代-r，除了递归同步外，还会同步元信息（比如修改时间，权限）。
```


#### sed

流编辑器stream editor

可以对输入流（文件或管道）逐行处理

```shell
'sed' operates by performing the following cycle on each line of input: first, 'sed' reads one line from the input stream, removes any trailing newline, and places it in the pattern space.  Then commands are executed; each command can have an address associated to it: addresses are a kind of condition code, and a command is only executed if the condition is verified before the command is to be executed.When the end of the script is reached, unless the '-n' option is in use, the contents of pattern space are printed out to the output stream, adding back the trailing newline if it was removed.(1)  Then the next
cycle starts for the next input line.

sed的工作过程：sed维护了两个数据缓冲区pattern_space和hold_space,两个缓冲区初始为空。对于输入流，sed会循环执行: 从输入流里读取一行数据，去掉数据末尾的换行符，放到pattern_space里；然后运行SCRIPT处理pattern_space；当所有SCRIPT运行完，（若没有指定-n）会把pattern_space里的数据输出（默认是标准输出）。

语法格式
stdout | sed [OPTIONS] SCRIPT
或
sed [OPTIONS] SCRIPT file

SCRIPT的格式  [addr]X[options]
	X是命令。
	addr是行地址，用于选择某些行用X命令进行处理，若无指定则处理所有行。
	options在某些命令中使用。
	在一个SCRIPT中可以使用多个command,commad之间有分号;隔开。有时command的顺序会影响输出，需要注意。

sed -n '/var/s/mail/MAIL/gp' passwd  # /var/是addr，s/mail/MAIL/gp是command。对于passwd文件中包含var字符串的行，使用commad处理。

sed '/^foo/d ; s/hello/world/' input.txt # 删除所有以foo开头的行，并把所有行中第一个hello换成world

OPTIONS:
-n, --quiet, --silent 默认情况下sed会在SCRIPT运行完后，输出当前pattern_space。若使用了这些选项，则会抑制这种输出。该选项常和p命令合用。
--debug  调试信息，显示sed运行过程
-e 增加script,默认为一个script不用写-e
-f 指定一个文件读取script,通常这个文件为.sed文件
-i 将处理后的输入流写入原文件，而不是输出到标准输出
-E, -r, --regexp-extended 使用扩展正则，默认是基本正则

[addr]：
单个数字选择某一行  
	sed -n '2p' file  # 打印file的第二行
	sed -n '$p' file  # 打印输入流（文件）最后一行
数字范围选择的某些行  sed
	sed -n '2,4p' file # 打印输入流（文件）第2到4行
	sed -n '1,10d' file # 删除输入流第1到10行
正则表达式选择匹配的行
	sed -n '/^root/p' passwd # 打印以root开头的行
	sed -n '/^root/,/^sync/ p' passwd # 打印从root开头的行到sync开头的行之间所有行

[X] command:
s/REGEXP/REPLACEMENT/[FLAGS]  正则替换，REPLACEMENT替换REGEXP匹配的内容
c  [options]  用 [options]替换整行
p  打印当前pattern_space   		 sed 'p' file
d  删除整行
q[EXIT_CODE]  退出sed不再继续处理，返回EXIT_CODE
=  输出行号			sed -n '/root/=' file


关于更多的command 's':
格式 s/REGEXP/REPLACEMENT/[FLAGS]

Its basic concept is simple: the 's' command attempts to match the pattern space against the supplied regular expression REGEXP; if the match is successful, then that portion of the pattern space which was matched is replaced with REPLACEMENT.

REGEXP：正则表达式，匹配的内容会被REPLACEMENT替换
REPLACEMENT：
	字符串：直接替换
	\n：n的取值1~9，引用匹配分组的内容
		echo "python" | sed -r 's/(py)/ww\1\1/'
	&: 引用整个匹配内容
		echo "python" | sed -r 's/.*/&3/'
	\L: 将后面的内容转为小写，直到遇到\U或\E结束
	\l: 将后面的一个字符转为小写 
	\U: 将后面的内容转为大写，直到遇到\L或\E结束 
	\u: 将后面的一个字符转为大写 
	\E: 结束\L,\U的转换
		echo "python" | sed -r 's/.*/\u&3/'
		echo "python asyncio" | sed -r 's/(python) (asyncio)/\U\1\E\2/'
		echo "python asyncio" | sed -r 's/(\b[^\s])/\u\1/g'  # 将所有单词首字母大写

FLAGS：
无		s/regex/repl/ 替换行的第一个匹配
g		s/regex/repl/g 替换行的全部匹配
NUMBER		s/regex/repl/NUMBER 替换行的第NUMBER个匹配
p		s/regex/repl/p  打印替换后的行
w FILENAME     s/regex/repl/w FILENAME  把替换后的行写入FILENMAE文件
i/I		s/regex/repl/i   忽略大小写来进行匹配
```


#### sort
排序
```shell
-n 按数值的大小排序
-r 以相反的顺序输出（默认增序）
-u 排序并去重
```


#### systemctl

Control the systemd system and service manager

```shell
语法
systemctl [OPTIONS...] COMMAND [UNIT...]

systemctl list-units  # 列出服务
systemctl status mysql.service  # 查看服务状态 
systemctl start mysql.service  # 启动服务 
systemctl stop mysql.service  # 关闭服务 
systemctl restart mysql.service  # 重启服务 
systemctl disable mysql.service  # 禁止自启 
systemctl enable mysql.service  # 恢复自启 

```


#### tar

打包

```shell

```


#### tee

读取stdin流，重定向到文件，并提供一份给后续命令作为stdin

```shell
-n # 追加写

ls | tee output | cat -n
```


#### tr

对输入流的字符进行替换，压缩，删除。

```shell
tr [options] set1 set2
将来自stdin的输入字符从set1映射到set2，然后写入stdout。
set1和set2是字符类或字符集。如果set2长度小于set1，那么set2会不断重复其最后一个字符直到和set1等长。如果set2
的长度大于set1，那么超出的部分无效。

echo "ABCD" | tr "A-Z" "a-z"   # abcd
echo "ABCD" | tr [:upper:] [:lower:]  # abcd



-s # 把连续重复的字符以单独一个字符表示
echo dd0d0ddd | tr -s d  # 输出：d0d0d

-d [set] # 删除在set中存在的字符
echo "HEllo 123" | tr -d "0-9"  # HEllo

-c [set] # 补集
echo "hello123" | tr -d -c "0-9 \n"   # 将不在补集里的字符删掉

```


#### uname [-asrmpi]

查看系统与内核相关信息

`uname -a`  # 所有相关信息


#### uniq

去重（相邻行去重，所以去重前须排序）
```shell
-n 按数值的大小排序
-r 以相反的顺序输出（默认增序）
-u 排序并去重
-w 指定一个数字N，比较不超过N个字符

echo "aab\naaa" | uniq -w 2  # output:aab 
```


#### uptime

系统启动时间与工作负载


#### xargs

xargs把从stdin接收到的数据重新格式化，再将其作为参数提供给其它命令

```shell
-n 指定一次传递几个参数
-p 每一次构造运行命令都提示
-d 指定分隔符
-a 从文件读入参数
-I 指定替换字符

ls | xargs rm  # 删除当前目录下的文件
ls | xargs rm -rf # 删除当前目录下的文件和目录
ls | xargs -n1 -d "\n" -p unar # 解压目录下所有文件
cat files.txt | xargs -I {} cat {}
```



## 常用命令

```shell
# 从ps aux中提取pid
ps aux | grep process_name | tr -s ' '| cut -d ' ' -f 2
```

```shell
# 查看进程运行时的环境变量；由于cat命令输出的各环境变量之间有\0分割，不便查看，用tr替换。
cat /proc/$PID/environ | tr '\0' '\n'
```

```powershell
# 打印彩色输出
\e[1;{颜色码}m   将颜色设置成指定颜色。\e[0m重置颜色
比如： echo -e "\e[1;31m hello" 将输出红色颜色的hello
```

```shell
# 统计源代码目录中所有C程序文件的行数
find source_dir -type f -name "*.c" -print0 | xargs -0 wc -l
```

```shell
# 批量重命名

# 把文件名的前三个字母替换成一个v
# 对于文件名中有空格的情况，先 IFS=$'\n' 
for i in `ls`; do mv -f $i `echo $i | sed 's/^.../v/'`; done
或
for i in `ls`
do
    newfile = `echo $i | sed 's/^./a/'`
    mv -f $i $newfile
done

#在文件名前面加一串字符
for i in `ls`; do mv -f $i `echo abc_$i`;done

#文件名所有小写变大写
for i in `ls`;do mv -f $i `echo $i | tr 'a-z' 'A-Z'`;done


```

```shell
# 字符串补零

printf "%03d\n" 1  # 001

s=1_13243
echo ${s%_*} | xargs printf "%02d\n"       # 01
```



```shell
# 重复执行命令

# 重复运行10次test_QuickSort.py
for i in {1..10}; do python3 ./test_QuickSort.py ; done;
或
for i in `seq 1 10`; do python3 ./test_QuickSort.py ; done;
```



```shell
# 硬件检测

# 列出硬件信息
lshw

# CPU
lscpu

# 硬盘
# 查看硬盘信息
hdparm -I /dev/sda
# 测试硬盘传输速度
hdparm -tT /dev/sda
# GUI工具 磁盘/disk 也可以查看测试硬盘
```



