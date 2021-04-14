```shell
安装
1, sudo apt install mysql-server
2, sudo mysql_secure_installation
3， sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf #找到 bind-address 修改值为 0.0.0.0(如果需要远程访问)
4， sudo /etc/init.d/mysql restart #重启mysql
```

```mysql
sudo systemctl start mysql 启动mysql
mysql -u root -p 连接登陆到mysql
创建数据库：create database 数据库名;
显示数据库： show databases;
删除数据库：drop database 数据库名;
连接数据库： use 数据库名;
显示数据库内的表：show tables;
查看表内有哪些列：show full columns from 表明;
查看用户：select user,host from mysql.user;
创建用户：create user '用户名'@'localhost' identified with mysql_native_password by '密码';
修改用户密码：alter user '用户名'@'%' identified  by '密码';
授权：grant select,insert,update,delete on 数据库名.* to '用户名'@'localhost';
全部授权：grant all privileges on 数据库名.* to '用户名'@'localhost';
查看授权：show grants for "用户名"@'localhost';
取消用户所有数据库（表）的所有权限：revoke all on *.* from '用户名'@'localhost';
删除用户：delete from mysql.user where user='用户名';
立即启用修改：flush privileges;
实时查看连接情况： show processlist; 
```

