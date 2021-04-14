## 四大组件

Android系统有四大组件：活动（Activity），服务（Service），广播接收器（Broadcast Receiver），内容提供器（Content Provider）。





## 问题

- no permissions (user in plugdev group; are your udev rules wrong?)

> 1.手机usb调试要打开
>
> 2.sudo vim /etc/udev/rules.d/51-android.rules
>
> 写下：
>
> SUBSYSTEM=="usb", ATTR{idVendor}=="04E8", MODE="0666", GROUP="plugdev"
>
> > idVendor从lsusb中得到
> >
> > Bus 003 Device 010: ID 12d1:0005 Huawei Technologies Co., Ltd. Lenovo S750
> >
> > 其中12d1:0005的12d1就是idVendor  
> >
> > 如有需要，不同厂商设备各写一条进去
>
> 3.sudo chmod a+r /etc/udev/rules.d/51-android.rules
>
> 4.sudo adb kill-server
>  sudo adb start-server
>  adb devices

- Android Studio卡
> vim [AS安装位置]/bin/studio.vmoptions
>
> 更改其中-Xmx 把数字调大