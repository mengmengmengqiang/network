# 网络配置
> #### 1.静态IP
> #### 2.桥接网络

## 配置
### 1.静态IP

>> **待添加...**

### 2.桥接网络(*ubuntu16.04*)

> [参考来源](http://fp-moon.iteye.com/blog/1468650)

要使Linux工作在网桥模式,就必须要安装网桥工具**bridge-utils**,运行命令:

    sudo apt-get install -y bridge-utils

或者自行编译安装**bridge-utils**源码包。

安装**net-utils**包使命令**ifconfig**生效:

    sudo apt-get install -y net-utils

#### **配置网桥**
1.我们需要让Linux知道网桥，首先告诉它，我们想要一个虚拟的以太网桥接口。运行下面命令新建一个虚拟的以太网桥接口:

    sudo brctl addbr br0

2.其次，网桥所在的网络里只有一台路由器，所以不需要生成树协议(STP)，不会生成一个环，我们可以选择关闭这个功能以减少网络环境的数据包污染。

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

#### **网桥相关命令**:
>> 关闭网桥

    sudo brctl delif br0 eth0
    ifconfig br0 down

>> 删除网桥

    sudo brctl delbr br0


>> This repository is free software disturibute under the GPL.
