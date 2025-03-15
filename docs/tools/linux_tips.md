# Linux Tips

Linux系统中的一些小工具、软件配置以及其他一些小玩意儿

* linux中最简单生成一个uuid的方法

```shell
$ cat /proc/sys/kernel/random/uuid
```

* 将sd卡/U盘格式化为无分区的样子，其实是覆盖了MBR

```shell
$ sudo mkfs.vfat -F 32 -I /d
```

* 检测网线插拔

```shell
$ /sys/class/net/eth0/carrier
```

* 32位运行库

```shell
$ apt install lib32z1
```

* 字符转艺术字

```shell
$ figlet
```

* linux查看运行时长

```shell
$ cat /proc/uptime
```

* 反向查询命令行历史记录，超实用

```shell
$ Ctrl+r
```

* linux串口工具

```shell
$ picocom
$ minicom
$ cutecom
```

* 串口插拔默认无通知，可以增加一些个udev通知来实现

```
ACTION=="add", KERNEL=="ttyUSB*[0-9]", SUBSYSTEM=="tty", RUN+="/sbin/runuser -l $user -c 'notify-send %k 串口已插入'"
ACTION=="remove", KERNEL=="ttyUSB*[0-9]", SUBSYSTEM=="tty", RUN+="/sbin/runuser -l $user -c 'notify-send %k 串口已拔出'"
```

* 文本转数组，命令行一键生成头文件

```shell
$ xxd -i file
```

* kde网络设置中导入openvpn配置

系统设置>连接>添加连接>导入vpn连接

如果.ovpn配置文件中包含tls-crypt字段，还需要在高级设置中对TLS进行设置

* nfs服务配置文件

```shell
$ /etc/exports
```

* mount绑定目录

```shell
$ sudo mount --bind src dest
```

* kvm启动失败,提示`启动域时出错: 所需操作无效：网络 'default' 未激活`

```shell
$ sudo virsh net-start default
```

* getopt命令的使用

```shell
set -- `getopt -q -l append:port: a:p: --append test -p /dev/ttyUSB0
while [ -n "$1" ]
do
	case "$1" in
	-a | --append)
		append = $2
		shift ;;
	-p | --port)
		port = $2
		shift ;;
	--)
		shift
		break;;
	esac
	shift
done
```

* USB共享网络RNDIS

```shell
# host
$ modprobe rndis_hos
# board
$ modprobe g_ether.ko
# on host
$ ifconfig usb0 192.168.2.1
# on board
$ ifconfig usb0 192.168.2.100
# enable forwarding on host
$ echo 1 > /proc/sys/net/ipv4/ip_forward
$ iptables -P FORWARD ACCEPT
$ iptables -A POSTROUTING -t nat -j MASQUERADE -s 192.168.2.0/24
```

* input设备测试工具

`evtest`命令用于测试input设备的输入

* PRIME Render Offload

双显卡笔记本安装Nvidia闭源驱动后可以通过修改环境变量启用独显加速？
做法就是在执行程序时添加如下的环境变量，或者直接修改`/usr/share/applications`目录下各程序描述文件中的`Exec`字段，将如下的环境变量添加到命令中。

```shell
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia
```

* 挂载img文件的方法

```shell
$ # 方法1
$ mount -t iso9660 -o loop xxx.img /media/imgfile
$ # 方法2
$ mount -o loop,offset=$((offset*sector_size)) xxx.img /media/imgfile
```

* 网络发生改变后自动运行命令

可以在`/etc/NetworkManager/dispatcher.d/`目录下添加自定义脚本，这样当网络接口发生变化时NetworkManager便会自动执行这些脚本。例如可以添加一个路由表优先级调整脚本，当未连接wifi时流量从有线网络经过，当wifi网络连接后，自动修改路由表，将流量路由至wifi,这样可以很方便的实现流量路线切换。


* debootstrap方式制作rootfs

可以安装`qemu-user-static`之后直接执行`debootstrap --arch armhf`命令来制作rootfs, qemu-user-static使用binfmt-misc技术来实现不同架构程序的本地运行，因此不需要使用--foreign参数，直接执行即可。
