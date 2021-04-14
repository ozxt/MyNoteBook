#### 简介

virtualenv is a tool to create isolated Python environments.Since Python 3.3, a subset of it has been integrated into the standard library under the venv module. 

#### 用法

`virtualenv venv`

阶段1:  discovers a python interpreter to create a virtual environment from (by default this is the same python as the one virtualenv is running from, however we can change this via the `p` option).寻找python解析器来创建虚拟环境。默认从运行virtualenv的地方找。

阶段2:  creates a virtual environment at the specified destination (dest), this can be broken down into four further sub-steps：
   - create a python that matches the target python interpreter from phase 1用阶段1中找到的python解析器版本创建pytohn。
   - install (bootstrap) seed packages (one or more of pip, setuptools, wheel) in the 	created virtual environment,安装seed packages，如对应的pip，setuptools，wheel。
   - install activation scripts into the binary directory of the virtual environment 	(these will allow end user to activate the virtual environment from various shells).安装用来激活虚拟环境的脚本。
- create files that mark the virtual environment as to be ignored by version control systems .创建文件，标识该虚拟环境被版本控制工具所忽略。

-p 指定python版本

格式   {python implementation name}{version}{architecture}

-p 3 .7.2 指定大版本号

-p python3.7.2

-p cpython 指定某一实现的python

-p python3.7.2-64 指定架构