把控制台的信息保存到文件方便查看 scrapy crawl spder_name -s LOG_FILE=scrapy.log

---

scrapy 有两类命令行命令：全局命令（global commands）（不需spider项目），项目特定命令（Project specific commands）（需要一个项目）

global commands：  `scrapy startproject [project_dir]`

`scrapy genspider spidername [-t template]` # scrapy genspider spidername example example.com

`scrapy fetch` #按照爬虫的实现来下载页面，并输出到标准输出

spider=SPIDER: bypass spider autodetection and force use of specific  spider –headers: print the response’s HTTP headers instead of the  response’s body –no-redirect: do not follow HTTP 3xx redirects (default  is to follow them) 

Project specific commands： `scrapy crawl` #启动爬虫

JOBDIR=dir 指定一个目录 可以断点续爬