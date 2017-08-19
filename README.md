# 网络配置
> #### 1.静态IP
> #### 2.桥接网络

## 配置
### 1.静态IP

### 2.桥接网络(*ubuntu下设置*)
要使Linux工作在网桥模式,就必须要安装网桥工具**bridge-utils**,运行命令:
    apt-get install -y bridge-utils
或者自行编译安装bridge-utils源码包。
还要安装**net-utils**包使命令**ifconfig**生效
#### 配置网桥
##### 1.我们需要让linux知道网桥，首先告诉它，我们想要一个虚拟的以太网桥接口。运行下面命令你新建一个虚拟的以太网接口:
    brctl addbr br0

>> This repository is free software disturibute under the GPL.
