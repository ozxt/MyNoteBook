**安装pip**
`curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`
`sudo python get-pip.py`



**临时换源**

`pip install -i https://pypi.tuna.tsinghua.edu.cn/simple  scrapy`



**pip换源**
`sudo pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple`
有sudo的会修改/root/.config/pip/pip.conf（`sudo pip install xxx` 读取的配置路径）
`pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple`
不带sudo的，会修改/home/<username>/.config/pip/pip.conf（`pip install xxx` 读取的配置路径）
`pip install XXX`会把包安装到用户目录下

`sudo pip instal`l 会安装成全局性的

pip安装包:pip install 模块名

查看已安装的包的详细信息: `pip show --files 模块名`
检查哪些包需要更新: `pip list --outdated`
pip升级包: `pip install --upgrade 模块名`

pip卸载包: `pip uninstall SomePackage`

`pip install -U pip`

安装制定包 `pip install "SomeProject==1.4"`