烧录好镜像后，记得把ssh的公钥放到sd卡里的`.ssh/authorized_keys末尾。`

---

**配置无线**

新增/etc/netplan/51-customwifi-init.yaml

可用命令 netplan try来检测语法错误

```YAML
network:
    version: 2
    renderer: networkd
    wifis:
        wlan0:
            dhcp4: no
            dhcp6: no
            addresses: [192.168.0.150/24]
            gateway4: 192.168.0.1
            nameservers:
                addresses: [192.168.0.1, 8.8.8.8]
            access-points:
                "TP-LINK_ASV4":
                    password: "124134324232"

```

---

rsync同步（rsync 默认使用 SSH 进行远程登录和数据传输。）
客户端到服务端  `rsync -av source/ username@remote_host:destination`
服务端到客户端  `rsync -av username@remote_host:source/ destination`

---

修改时区 

tzselect

---

ubuntu for raspberrypi的config.txt在/boot/firmware

---

wire1需要加载模块w1-gpio,w1-therm
可将模块写在/etc/modules末尾

---

