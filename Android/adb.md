## 命令

```shell
adb devices

adb kill-server
	关闭adb服务

adb pull <手机路径>   <本机路径>  
	从手机中拉取信息到本地电脑上
	adb pull /sdcard/ImInPhone.txt /home/oo/
	
adb push <本机路径>  <手机路径>  
	从本地电脑推送信息到手机上
	adb push /home/oo/test.txt /sdcard/

截图：
保存在手机：
adb shell screencap /sdcard/1.png
保存到电脑：
adb exec-out screencap -p > 1.png
或
adb shell screencap -p > 1.png


```



```shell
# 无线调试
手机和电脑需要在用以局域网内
1，手机先用USB线连接电脑
2，adb devices  测试下是否连接上
3，adb tcpip [port]  让手机的某个端口处于监听状态（端口后的范围为5555-5585的奇数端口，默认从5555开始）
返回restarting in TCP mode port : 5555代表端口已经处于了监听状态。这个时候就可以断开手机连接的数据线了。
4，adb connect [ip-address]：[port]  其中ip-address为手机的ip地址，port就是上面设置的端口号。
返回connected to [ip-address]：[port-num]表示成功连接了手机，现在可以通过wifi调试了。
5，adb disconnect [ip-address]：[port]  断开连接
```

