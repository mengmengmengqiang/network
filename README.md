* [网络配置](#1.静态IP)
    * [1.静态IP](#1.1)
    * [2.桥接网络](#2.桥接网络(ubuntu16.04))

网络配置

### 1.静态IP

> 在**RedHat**系环境下

    #备份
    sudo cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0.bak
    #写入配置
    sudo cat >/etc/sysconfig/network-scripts/ifcfg-eth0<<-'EOF'                                                                                                                                 
    TYPE=Ethernet           #网络类型为以太网
    BOOTPROTO=static        #静态网络
    NAME=eth0               #网络名称eth0
    DEVICE=eth0             #网络接口设备eth0 
    IPADDR=172.16.0.5       #IP地址
    NETMASK=255.255.255.0   #子网掩码
    GATEWAY=172.16.0.1      #网关地址
    ONBOOT=yes              #开机自动启动
    EOF

> 在**debian**系环境下

    #备份
    sudo cp /etc/network/interfaces /etc/network/interfaces.bak
    #写入配置
    sudo cat >>/etc/network/interfaces<<-'EOF'
    # interfaces(5) file used by ifup(8) and ifdown(8)

    source /etc/network/interfaces.d/*

    auto lo
    iface lo inet loopback

    auto eth0                             #开机自动启动网卡
    iface eth0 inet static                #静态IP
          address 192.168.9.13            #所配置的IP地址
          netmask 255.255.255.0           #子网掩码
          gateway 192.168.9.108           #网关IP
          dns-nameservers 114.114.114.114 #DNS服务器地址
    EOF

> 重启网络

    #redhat系发行版
    sudo systemctl restart network.service
    #debian发行版
    sudo systemctl restart networking.service
    #debian里面重启网络可能报错，不要管，先ping 114.114.114.114看看网络是不是联通

> 配置DNS服务器地址

    #debian系在上面已经配置了DNS地址,而/etc/resolv.conf文件在开机时会被修改，所以不要在这个文件里配置
    #redhat系发行版则可以通过/etc/resolv.conf文件来配置
    sudo cat "nameservers 114.114.114.114" >> /etc/resolv.conf


<h3 id="1.2">2.桥接网络(ubuntu16.04)</h3>

> [参考来源](http://fp-moon.iteye.com/blog/1468650)

要使Linux工作在网桥模式,就必须要安装网桥工具**bridge-utils**,运行命令:

    sudo apt-get install -y bridge-utils

或者自行编译安装**bridge-utils**源码包。

安装**net-utils**包使命令**ifconfig**生效:

    sudo apt-get install -y net-utils

#### **配置网桥**
1.我们需要让Linux知道网桥，首先告诉它，我们想要一个虚拟的以太网桥接口。运行下面命令新建一个虚拟的以太网桥接口:

    sudo brctl addbr br0

2.其次，网桥所在的网络里只有一台路由器，不会生成一个环，所以不需要生成树协议(STP)，我们可以选择关闭这个功能以减少网络环境的数据包污染。

    sudo brctl stp br0 off

3.将机器里的以太网接口附加到刚生成的逻辑(虚拟)网桥接口br0上。

    sudo brctl addif br0 eth0

4.现在原来的以太网物理接口变成了网桥上的逻辑端口，变成了逻辑网桥的一部分，不再需要IP地址，将这些IP地址释放掉。

    sudo ifconfig eth0 down
    sudo ifconfig eth0 0.0.0.0 up

5.启用网桥。

    sudo ifconfig br0 up

6.给网桥接口分配一个IP地址(**IP需要和eth0的IP在同一网段**)

    sudo ifconfig br0 x.x.x.x

7.查看已有网桥

    sudo brctl show

我们会看到配置的网桥信息如下

    bridge name  bridge id             STP enabled      interfaces
    br0          xxxx.xxxxxxxxxxxxxx   no               eth0

8.配置网桥随开机启动
>**这个挺烦的，以后再弄吧,可以把配置的命令加入rc.local里面随开机运行**

#### **网桥相关命令**：
> 从网桥删除eth0接口

    sudo brctl delif br0 eth0

> 关闭网桥

    sudo ifconfig br0 down

> 删除网桥

    sudo brctl delbr br0

> 查看其他网桥命令

    sudo brctl --help
