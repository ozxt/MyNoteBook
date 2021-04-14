```
# nginx配置文件

server {
    listen 80;
    listen [::]:80;
    server_name 192.168.43.98;
    charset utf-8;
    client_max_body_size 75M;
    location / {
        include uwsgi_params;
        uwsgi_pass 127.0.0.1:7777;
        uwsgi_read_timeout 20;
    }
}
```

```python
[uwsgi]
# uwsgi 启动时所使用的地址与端口,也可以使用.sock文件的方式
socket = 127.0.0.1:7777
# 指向网站目录
chdir = /home/duke/STUDY/flask/flask-tutorial/flaskr
#虚拟环境
venv = /home/duke/STUDY/flask/flask-tutorial/venv
# python 启动程序文件
wsgi-file = /home/duke/STUDY/flask/flask-tutorial/flaskr/manage.py
# python 程序内用以启动的 application 变量名
callable = app
#允许主线程存在
master = true
# 处理器数
processes = 2
# 线程数
threads = 4
chmod-socket = 664
#clear environment on exit
vacuum = true
#状态检测地址
stats = 127.0.0.1:7776
#项目flask日志文件
logoto = /home/duke/STUDY/flask/flask-tutorial/flaskr.log
#daemonize = /home/duke/STUDY/flask/flask-tutorial/flaskr.log

pidfile = /home/duke/STUDY/flask/flask-tutorial/flaskr.pid
```

uwsgi配置文件放在项目目录即可， 启动uwigi： uwsgi --ini uwsgi文件路径
nginx配置文件放在/etc/nginx/site-enables/下面。如果nginx配置文件有更改要重启nginx。
nginx -t    修改完运行这个检查有没有语法错误
systemctl reload nginx.service  重启nginx

---

 Why（注意区分大小写）
**WSGI协议（通信协议）**：Python用于Web开发的协议(用于处理Web服务器和应用程序（APP）的交互信息)
WSGI沟通的双方是wsgi server (比如uWSGI） 要和 wsgi application（比如django，flask ）
wsgi server (比如uWSGI）实现WSGI协议规范的服务器我们叫做wsgi服务器，比如uWSGI服务器，
wsgi application（比如django ）实现WSGI协议的应用，我们叫做wsgi应用，比如Django，Flask
**uWSGI is both a protocol and an application server; the application server can serve uWSGI, FastCGI, and HTTP protocols.**
The most popular uWSGI server is uwsgi.
uwsgi是一种线路协议而不是通信协议，在此常用于在uWSGI服务器与其他网络服务器的数据通信。
nginx和uWSGI之间的沟通就要用到uwsgi协议
Web框架一般都自带wsgi服务器 但是性能不好，只作测试用途。
Flask’s built-in server is not suitable for production as it doesn’t scale well and by default serves only one request at a time.

---
 Nginx
1 安全（Nginx 作为专业服务器，暴露在公网相对比较安全）
2 能更好地处理静态资源（一些http request header）
3 Nginx也可以缓存一些动态内容Nginx可以更好地配合CDN
4 可以进行多台机器的负载均衡
