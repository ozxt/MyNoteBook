查看硬件 sudo lshw
查看分区的UUID
ls -l /dev/disk/by-uuid

---

python开发环境：
apt install build-essential
apt install python3-devel
sudo pip3 install virtualenv

---
apt换源（Ubuntu）
sed -i 's/ports.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

---

锁定内核（不更新）
sudo apt-mark hold linux-image-generic linux-headers-generic 
解锁内核更新
sudo apt-mark unhold linux-image-generic linux-headers-generic 

---

查看网卡流量情况 sar -n DEV 1 10    (-n DEV 指定查看网络接口数据，1 10表示1秒取一次值，取10次)
rxpck/s：每秒钟接收的数据包
txpck/s：每秒钟发送的数据包
rxbyt/s：每秒钟接收的字节数
txbyt/s：每秒钟发送的字节数
rxcmp/s：每秒钟接收的压缩数据包
txcmp/s：每秒钟发送的压缩数据包
rxmcst/s：每秒钟接收的多播数据包
rxerr/s：每秒钟接收的坏数据包
txerr/s：每秒钟发送的坏数据包



监测eth0网卡的下载速度 : while true; do X=$Y; sleep 1; Y=$(ifconfig eth0|grep RX\ bytes|awk '{ print $2 }'|cut -d : -f 2); echo "$(( Y-X )) bps"; done 

---

Ubuntu gnome 自带录屏 Shift+Ctrl+Alt+R
画图 pencil
截图 screenshot、shutter

---

systemctl:管理服务

查看系统运行的服务 ps aux | grep 'd$'
查看服务状态 systemctl status mysql.service
启动服务 systemctl start mysql.service
关闭服务 systemctl stop mysql.service
重启服务 systemctl restart mysql.service
禁止自启 sudo systemctl disable mysql.service
恢复自启 sudo systemctl enable mysql.service

---

复制文件夹到另一个文件，如果更新了就覆盖，有的就不复制
cp -auxv /home/dd/dataForstu/* 目标目录

---

在Vim中直接进行转换文件编码,比如将一个文件转换成utf-8格式
:set fileencoding=utf-8

vim忘记加sudo无法保存： :w !sudo tee %

---

iconv 转换，iconv的命令格式如下：
iconv -f encoding -t encoding inputfile
比如将一个GBK 编码的文件转换成UTF-8编码
iconv -f GBK -t UTF-8 file1 -o file2
查看当前文件编码 　　enca -L zh_CN ip.tx
转换命令格式如下 　　
$enca -L 当前语言 -x 目标目录编码 文件名 　　
例如要把当前目录下的所有文件都转成utf-8 　　
enca -L zh_CN -x utf-8 *   
检查文件的编码　enca -L zh_CN file  　　
 将文件编码转换为"UTF-8"编码　 enca -L zh_CN -x UTF-8 file
如果不想覆盖原文件可以这样     enca -L zh_CN -x UTF-8 < file1 > file2 

---

zip -q -r xxxx.zip 目录
解压rar:　　unrar　x XXX.rar 
解压中文乱码：unzip -O CP936 xxx.zip (用GBK, GB18030也可以)
压　缩：tar -jcv -f filename.tar.bz2 要被压缩的文件或目录名称 
查　询：tar -jtv -f filename.tar.bz2 
解压缩：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录

---

挂载windows分区：
sudo fdisk -l 查看win分区
sudo mount -t ntfs /dev/sda3 /mnt/win -ro iocharset=utf8,umask=0
sudo umount /dev/sda3

---

查看所有目录大小: du -h /path | sort -h 

---

把目录下文件的名字中的ｅ’替换成‘ｒ’: ls | rename 's/e/r/'

---

在/home目录下查找以.txt结尾的文件名  find /home -name "\*.txt"  (iname:忽略大小写)
删除目录dir下的匹配文件 find dir -name "\*.txt" -delete
寻找文件并执行处理 find /dir -name re\*.jpg -exec ls -l {}\;  
对找到的文件执行ls，{}是占位符，会被不断的替换为find找到的文件，\;表示exec的结束，加\是将;转义。把-exec换成-ok可询问操作。

---

ubuntu文件管理器(nautilus)中按cfrl+s可以用正则匹配锁定文件

nautilus命令用于图形文件管理
 nautilus可打开文件管理系统，指定目录打开到相应目录

---

反向删除文件 rm \`ls | grep -v 'ad'\`

---

校验下载文件
echo "48167067d65c5192ffe041c9cc4958cb7fcdfd74fa15e1937a47430ed7b9de99  *ubuntu-20.04-preinstalled-server-arm64+raspi.img.xz" | shasum -a 256  --check

---

apt-cache search packagename 搜索包
apt-cache show packagename 获取包的相关信息，如说明、大小、版本等
apt-get install packagename 安装包
apt-get install packagename --reinstall 重新安装包
apt-get -f install 修复安装”-f = –fix-missing”
apt-get remove packagename 删除包
apt-get remove packagename --purge 删除包，包括删除配置文件等
apt-get update 更新源
apt-get upgrade 更新已安装的包
apt-get dist-upgrade 升级系统
apt-get dselect-upgrade 使用 dselect 升级
apt-cache depends packagename 了解使用依赖
apt-cache rdepends packagename 是查看该包被哪些包依赖
apt-get build-dep packagename 安装相关的编译环境
apt-get source packagename 下载该包的源代码
apt-get clean 清理无用的包

apt-get autoclean 清理无用的包
apt-get check 检查是否有损坏的依赖

apt-cache madison package 查询版本