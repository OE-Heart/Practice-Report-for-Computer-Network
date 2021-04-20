

<img src="picture/1.png" alt="1" style="zoom:80%;" />



<center><font face="黑体" size="20">数据通信与计算机网络课后实践</font>


<img src="picture/2.png" alt="2" style="zoom:90%;" />



<center>
    <font face="楷体" size="5">姓名：</font>
</center>

<center>
    <font face="楷体" size="5">专业：</font>
</center>

<center>
    <font face="楷体" size="5">学号：3190104783</font>
</center>

<center>
    <font face="楷体" size="5">课程名称：数据通信与计算机网络</font>
</center>

<center>
    <font face="楷体" size="5">指导老师：胡骏</font>
</center>


​    

<center>
    </font><font face="黑体" size="5">2020~2021春夏学期 2021 年 4 月 5 日</font>
</center>


---

# 1  基础组网

## 1.1 实验环境

GNS3 是一款具有图形化界面的、可以运行在多平台（包括Windows, Linux, and MacOS等）的网络虚拟仿真软件。通过 Cisco 官方提供的路由器和交换机的镜像文件，GNS3 能够完整地模拟整个校园网络或企业网络。GNS3 由多个组件集合而成，包含了 Dynamips、Qemu、Wireshark 等程序。Dynamips 是一个基于虚拟化技术的模拟器（emulator），本身就能够模拟路由器和交换机。GNS3 在 Dynamips 的基础上，加入一个友好的图形化操作界面 Qemu，目前 GNS3 可以模拟防火墙、入侵检测系统、Juniper 路由器等；Wireshark 组件用于进行数据报文分析。

本部分实验使用一台操作系统为 Ubuntu 20.04.2 LTS x86_64 的个人计算机，通过版本为2.2.19的 GNS3 网络虚拟仿真软件完成。

本部分实验路由器采用的镜像为 c7200-a3jk9s-mz.124-25g，路由交换机采用的镜像为 c3640-a3js-mz.124-25d，交换机采用的镜像为 c3640-a3js-mz.124-25d，所有实验操作和命令都以此为基础。



## 1.2 虚拟网络组网

### 1.2.1 实验原理

#### 虚拟局域网

虚拟局域网(Virtual Local Area Network 或简写 VLAN)是一种建构于局域网交换技术(LAN Switch)的网络管理的技术，网管人员可以借此透过控制交换机有效分派出入局域网的分组到正确的出入端口，达到对不同实体局域网中的设备进行逻辑分群管理，并降低局域网内大量资料流通时，因无用分组过多导致拥塞的问题，以及提升局域网的信息安全保障。 

#### 三层交换机

三层交换机就是具有部分路由器功能的交换机，三层交换机的最重要目的是加快大型局域网内部的数据交换，所具有的路由功能也是为这目的服务的，能够做到一次路由，多次转发。对于数据包转发等规律性的过程由硬件高速实现，而像路由信息更新、路由表维护、路由计算、路由确定等功能，由软件实现。三层交换技术就是二层交换技术 +三层转发技术。传统交换技术是在 OSI网络标准模型第二层数据链路层进行操作的，而三层交换技术是在网络模型中的第三层实现了数据包的高速转发，既可实现网络路由功能，又可根据不同网络状况做到最优网络性能。

局域网按功能或地域等因素划成一个个小的局域网，这就使 VLAN 技术在网络中得以大量应用，而各个不同 VLAN 间的通信都要经过路由器来完成转发，随着网间互访的不断增加。单纯使用路由器来实现网间访问，不但由于端口数量有限，而且路由速度较慢，从而限制了网络的规模和访问速度。基于这种情况三层交换机便应运而生，三层交换机是为 IP 设计的，接口类型简单，拥有很强二层包处理能力，非常适用于大型局域网内的数据路由与交换，它既可以工作在协议第三层替代或部分完成传统路由器的功能，同时又具有几乎第二层交换的速度，且价格相对便宜些。

例如模型：使用IP的设备 A←→三层交换机←→使用 IP 的设备 B。

比如 A 要给 B 发送数据，已知目的 IP，那么 A 就用子网掩码取得网络地址，判断目的 IP 是否与自己在同一网段。如果在同一网段，但不知道转发数据所需的 MAC 地址，A 就发送一个 ARP 请求，B 返回其 MAC 地址，A 用此 MAC 封装数据包并发送给交换机，交换机起用二层交换模块，查找 MAC 地址表，将数据包转发到相应的端口。

如果目的 IP 地址显示不是同一网段的，那么 A要实现和 B的通讯，在流缓存条目中没有对应 MAC 地址条目，就将第一个正常数据包发送向一个缺省网关，这个缺省网关一般在操作系统中已经设好，对应第三层路由模块，所以可见对于不是同一子网的数据，最先在  MAC 表中放的是缺省网关的 MAC 地址；然后就由三层模块接收到此数据包，查询路由表以确定到达 B 的路由，将构造一个新的帧头，其中以缺省网关的 MAC 地址为源 MAC 地址，以主机 B 的 MAC 地址为目的 MAC 地址。通过一定的识别触发机制，确立主机 A 与 B 的 MAC 地址及转发端口的对应关系，并记录进流缓存条目表，以后的 A 到 B 的数据，就直接交由二层交换模块完成。这就通常所说的一次路由多次转发。

表面上看，第三层交换机是第二层交换器与路由器的合二为一，然而这种结合并非简单的物理结合，而是各取所长的逻辑结合。其重要表现是，当某一信息源的第一个数据流进行第三层交换后，其中的路由系统将会产生一个 MAC 地址与 IP 地址的映射表，并将该表存储起来，当同一信息源的后续数据流再次进入交换环境时，交换机将根据第一次产生并保存的地址映射表，直接从第二层由源地址传输到目的地址，不再经过第三路由系统处理，从而消除了路由选择时造成的网络延迟，提高了数据包的转发效率，解决了网间传输信息时路由产生的速率瓶颈。所以说，第三层交换机既可完成第二层交换机的端口交换功能，又可完成部分路由器的路由功能。

#### 交换机虚拟接口

交换机虚拟接口（Switch Virtual Interface，简称SVI），多用于路由交换机（三层），是一种三层逻辑接口，其实就是指通常所说的VLAN接口，只不过它是虚拟的，用于连接整个VLAN，所以通常也把这种接口称为逻辑三层接口。

一个 SVI 接口对应一个 VLAN， 一个 VLAN 仅可以有一个 SVI。当需要在 VLAN 之间进行路由通信，或者提供 IP 主机到交换机的连接的时候，就需要起用交换机上相关 VLAN 的 SVI 接口，即为相关 VLAN 的 SVI 接口配置 IP 地址、子网掩码，因此，SVI 是联系 VLAN 的 IP 接口。

### 1.2.2 实验内容及其步骤

#### 网络规划与设计

##### 网络拓扑设计

此步骤中的局域网由1台路由交换机（R1）、2台交换机（SW1 和 SW2）与6台 PC 机（Host1~Host6）组成，网络拓扑结构如下图所示。

![topo3](picture/topo3.png)

##### IP 地址与 VLAN 划分设计

PC 机 IP 地址设计如下表所示。

| 设备  |    IP 地址    |   子网掩码    |     网关     |
| :---: | :-----------: | :-----------: | :----------: |
| Host1 | 10.0.101.1/24 | 255.255.255.0 | 10.0.101.254 |
| Host2 | 10.0.101.2/24 | 255.255.255.0 | 10.0.101.254 |
| Host3 | 10.0.102.3/24 | 255.255.255.0 | 10.0.102.254 |
| Host4 | 10.0.101.4/24 | 255.255.255.0 | 10.0.101.254 |
| Host5 | 10.0.101.5/24 | 255.255.255.0 | 10.0.101.254 |
| Host6 | 10.0.102.6/24 | 255.255.255.0 | 10.0.102.254 |

路由器端口 IP 设计如下表所示。

| 设备 |     IP 地址     |            说明            |
| :--: | :-------------: | :------------------------: |
|  R1  | 10.0.101.254/24 | 配置给虚拟交换接口 VLAN 10 |
|  R1  | 10.0.102.254/24 | 配置给虚拟交换接口 VLAN 20 |

交换机 VLAN 划分设计如下表所示。

| 设备 | 端口 | VLAN ID | VLAN Name |  端口性质   |
| :--: | :--: | :-----: | :-------: | :---------: |
| SW1  | F0/0 |    /    |     /     | Trunk Port  |
| SW1  | F0/1 | VLAN 10 | VLAN0010  | access Port |
| SW1  | F0/2 | VLAN 10 | VLAN0010  | access Port |
| SW1  | F0/3 | VLAN 20 | VLAN0020  | access Port |
| SW2  | F0/0 |    /    |     /     | Trunk Port  |
| SW2  | F0/1 | VLAN 10 | VLAN0020  | access Port |
| SW2  | F0/2 | VLAN 10 | VLAN0020  | access Port |
| SW2  | F0/3 | VLAN 20 | VLAN0020  | access Port |
|  R1  | F0/1 |    /    |     /     | Trunk Port  |
|  R1  | F0/2 |    /    |     /     | Trunk Port  |

#### 部署网络并配置 PC 机 IP 地址

##### 在 GNS3 中部署网络设备

此步骤中的路由交换机使用c3600路由器来模拟，使用时通过”ip routing”命令开启三层路由功能。二层交换机使用c3600路由器来模拟，使用时通过”no ip routing”命令关闭路由功能。主机采用 GNS3 中自带的 VPCS 虚拟主机实现。 

按照前述网络拓扑设计，完成各个网络设备的部署、网线的连接，并启动整个网络。

整个网络在 GNS3 中的拓扑结构以及接口连接如下图所示。

![topo4](picture/topo4.png)

##### 配置 Host1~Host6 的 IP 地址

打开 Host1 的命令控制台窗口，配置并保存 IP 地址(10.0.101.1/24)、子网掩码(255.255.255.0)和默认网关(10.0.101.254)地址，配置命令如下。

```
ip 10.0.101.1/24 255.255.255.0 10.0.101.254
save
```

![image-20210417092714043](picture/image-20210417092714043.png)

同理，按照前述 IP 地址设计，配置并保存 Host2~Host6 的 IP 地址、子网掩码、默认网关。

#### 对二层交换机 SW1 进行配置并测试网络连通性

开启交换机 SW1，打开 SW1 的命令控制台窗口，按照规划在交换机 SW1 上配置 VLAN，并将相关端口设置成 access 端口或 trunk 端口。 配置命令如下所示 。

```
vlan database
vlan 10
vlan 20
exit

config terminal
interface f0/1
switchport mode access
switchport access vlan 10
exit

interface f0/2
switchport mode access
switchport access vlan 10
exit

interface f0/3
switchport mode access
switchport access vlan 20
exit

exit
```

此时查看该交换机 vlan 的简要情况如下。

![image-20210417094231158](picture/image-20210417094231158.png)

```
config terminal
interface f0/0
switchport trunk encapsulation dot1q
switchport mode trunk
exit
exit
```

查看交换机上 trunk 端口的信息如下。

![image-20210417094435322](picture/image-20210417094435322.png)

使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机 |          `ping`测试结果           |
| :--: | :------: | :------: | :-------------------------------: |
|  1   |  Host1   |  Host2   |              success              |
|  2   |  Host1   |  Host3   | host (10.0.101.254) not reachable |
|  3   |  Host1   |  Host4   |  host (10.0.101.4) not reachable  |
|  4   |  Host1   |  Host5   |  host (10.0.101.5) not reachable  |
|  5   |  Host1   |  Host6   | host (10.0.101.254) not reachable |

分析

#### 对二层交换机 SW2 进行配置并测试网络连通性

开启交换机 SW2，打开 SW2 的命令控制台窗口，按照规划在交换机 SW2 上配置 VLAN，并将相关端口设置成 access 端口或 trunk 端口。 配置过程同 SW1。

配置完成后分别查看交换机 vlan 的简要情况和交换机上 trunk 端口的信息如下。

![image-20210417095240478](picture/image-20210417095240478.png)

![image-20210417095515758](picture/image-20210417095515758.png)

使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机 |          `ping`测试结果           |
| :--: | :------: | :------: | :-------------------------------: |
|  1   |  Host1   |  Host2   |              success              |
|  2   |  Host1   |  Host3   | host (10.0.101.254) not reachable |
|  3   |  Host1   |  Host4   |  host (10.0.101.4) not reachable  |
|  4   |  Host1   |  Host5   |  host (10.0.101.5) not reachable  |
|  5   |  Host1   |  Host6   | host (10.0.101.254) not reachable |

分析

#### 在三层交换机 R1 中创建 VLAN、设置 trunk 接口并测试网络连通性

此步骤中，在三层交换机 R1上创建 VLAN10 和 VLAN20，但是不开启三层交换机 R1的路由功能（no ip routing），不起用交换机虚拟接口 SVI），只是根据网络拓扑的设计将三层交换机 R1上与二层交换机 SW1 和 SW2 相连的端口设置为 trunk 模式，然后测试跨交换机相同 VLAN 之间的通信和不同 VLAN之间的通信。

开启三层交换机 R1，打开其命令控制台窗口，配置命令如下所示 。

```
vlan database
vlan 10
vlan 20
exit

config termianl
interface range f0/1 -2
switchport trunk encapsulation dot1q
switchport mode trunk
exit

no ip routing
exit
```

查看交换机上的 trunk 端口如下。

![image-20210417100746790](picture/image-20210417100746790.png)

使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机 |          `ping`测试结果           |
| :--: | :------: | :------: | :-------------------------------: |
|  1   |  Host1   |  Host2   |              success              |
|  2   |  Host1   |  Host3   | host (10.0.101.254) not reachable |
|  3   |  Host1   |  Host4   |              success              |
|  4   |  Host1   |  Host5   |              success              |
|  5   |  Host1   |  Host6   | host (10.0.101.254) not reachable |

分析

#### 配置三层交换机 R1 的 SVI 接口、开启路由功能并测试网络连通性

此步骤中， 启用三层交换机 R1上 VLAN10 和 VLAN20的 SVI 接口，给他们配置 IP 地址。 开启三层交换机 R1的路由功能（ip routing），然后测试跨交换机相同 VLAN 之间的通信和不同 VLAN 之间的通信。

打开 R1 的命令控制台窗口，配置命令如下所示 。

```
config terminal
interface vlan 10
ip address 10.0.101.254 255.255.255.0
exit

interface vlan 20
ip address 10.0.102.254 255.255.255.0
exit

ip routing
exit
```

此时查看路由表信息如下。

![image-20210417101552726](picture/image-20210417101552726.png)

使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机 | `ping`测试结果 |
| :--: | :------: | :------: | :------------: |
|  1   |  Host1   |  Host2   |    success     |
|  2   |  Host1   |  Host3   |    success     |
|  3   |  Host1   |  Host4   |    success     |
|  4   |  Host1   |  Host5   |    success     |
|  5   |  Host1   |  Host6   |    success     |

分析



## 1.3 路由配置

### 1.3.1 实验原理

路由器是互联网的主要节点设备。路由器通过路由表决定数据的转发。转发策略称为路由选择（routing），这也是路由器名称的由来 router，转发者）。作为不同网络之间互相连接的枢纽，路由器系统构成了基于 TCP/IP 的国际互联网络 Internet 的主体脉络，也可以说，路由器构成了 Internet 的骨架。在局域网中，为了实现不同网络之间的互联，仍然需要路由器，路由器是用来连接不同网段或网络的。

在一个局域网中，如果不需与外界网络进行通信的话，内部网络的各主机都能识别其它主机 ，通过交换机就可以实现目的发送，根本用不上路由器来记忆局域网的各主机的 MAC 地址。

路由器的主要作用是识别不同网络 ，然后进行转发。路由器识别不同网络的方法是通过识别不同网络的网络 ID 号进行的，所以为了保证路由转发成功，每个网络都必须有一个唯一的网络编号。路由器要识别另一个网络，首先要识别的就是对方网络的路由器 IP 地址的网络 ID，看是不是与目的 主机 地址中的网络 ID 号相一致。如果是就向这个网络的路由器发送，接收网络的路由器在接收到源 网络发来的报文后，根据报文中所包括的目的主机 IP 地址中的主机，ID 号来识别是发给哪一个 主机的，然后再直接发送。

静态路由是由网络规划者根据网络拓扑，使用命令在路由器上配置的路由信息。这些静态路由信息指导报文发送，静态路由方式也不需要路由器进行计算，它完全依赖于网络规划者，当网络规模较大或网络拓扑经常发生改变时，网络管理员需要做的工作将会非常复杂并
且容易产生错误。

### 1.3.2 实验内容及其步骤

#### 网络规划与设计

##### 网络拓扑设计

本实验使用了2台路由器（R1和 R2）、4台交换机（SW1~SW4）和8台 PC 机（Host1~Host8），网络拓扑结构如下图所示。

![topo1](picture/topo1.png)

##### IP 地址设计

PC 机 IP 地址设计如下表所示。

| 设备  |   IP 地址    |   子网掩码    |    网关     |
| :---: | :----------: | :-----------: | :---------: |
| Host1 | 192.168.0.10 | 255.255.255.0 | 192.168.0.1 |
| Host2 | 192.168.0.20 | 255.255.255.0 | 192.168.0.1 |
| Host3 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| Host4 | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| Host5 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |
| Host6 | 192.168.2.20 | 255.255.255.0 | 192.168.2.1 |
| Host7 | 192.168.3.10 | 255.255.255.0 | 192.168.3.1 |
| Host8 | 192.168.3.20 | 255.255.255.0 | 192.168.3.1 |

路由器端口 IP 设计如下表所示。

| 设备 | 端口 |   IP 地址   |   子网掩码    |
| :--: | :--: | :---------: | :-----------: |
|  R1  | F0/0 |  10.0.0.1   | 255.255.255.0 |
|  R1  | F1/0 | 192.168.0.1 | 255.255.255.0 |
|  R1  | F1/1 | 192.168.1.1 | 255.255.255.0 |
|  R2  | F0/0 |  10.0.0.2   | 255.255.255.0 |
|  R2  | F1/0 | 192.168.2.1 | 255.255.255.0 |
|  R2  | F1/1 | 192.168.3.1 | 255.255.255.0 |

路由器 R1 和 R2 之间通过 F0/0 端口相连。路由器 R1 和 R2 的 F1/0 端口和  F1/1 端口的 IP 地址分别是其所连接网络的默认网关地址。

静态路由规划设计如下表所示。

| 所在设备 | 目的网络(目的网络号/子网掩码) |        下一跳地址         |
| :------: | :---------------------------: | :-----------------------: |
|    R1    | 192.168.2.0    255.255.255.0  | 10.0.0.2(R2 的 F0/0 接口) |
|    R1    | 192.168.3.0    255.255.255.0  | 10.0.0.2(R2 的 F0/0 接口) |
|    R2    | 192.168.0.0    255.255.255.0  | 10.0.0.1(R1 的 F0/0 接口) |
|    R2    | 192.168.1.0    255.255.255.0  | 10.0.0.1(R1 的 F0/0 接口) |

#### 部署网络并配置 PC 机 IP 地址

##### 在 GNS3 中部署网络设备

此步骤中所用的路由器使用 c7200 路由器来模拟；所用的二层交换机使用 c3600 路由器来模拟(使用时通过“no ip routing”命令关闭路由功能)；所用的主机采用 GNS3 中自带的 VPCS 虚拟主机实现。

按照前述网络拓扑设计，完成各个网络设备的部署、网线的连接，并启动整个网络。

整个网络在 GNS3 中的拓扑结构以及接口连接如下图所示。

![topo2](picture/topo2.png)

##### 配置 Host1~Host8 的 IP 地址

打开 Host1 的命令控制台窗口，配置并保存 IP 地址(192.168.0.10)、子网掩码(255.255.255.0)和默认网关(192.168.0.1)地址，配置命令如下。

```
ip 192.168.0.10 255.255.255.0 192.168.0.1
save
```

![image-20210408081514305](picture/image-20210408081514305.png)

同理，按照前述 IP 地址设计，配置并保存 Host2~Host8 的 IP 地址、子网掩码、默认网关。

#### 配置路由器端口地址并测试连通性

##### 测试网络连通性

此时路由器和交换机都是默认配置，使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机 |          `ping`测试结果          |
| :--: | :------: | :------: | :------------------------------: |
|  1   |  Host1   |  Host2   |             success              |
|  2   |  Host1   |  Host3   | host (192.168.0.1) not reachable |
|  3   |  Host1   |  Host5   | host (192.168.0.1) not reachable |
|  4   |  Host1   |  Host7   | host (192.168.0.1) not reachable |
|  5   |  Host3   |  Host4   |             success              |
|  6   |  Host3   |  Host1   | host (192.168.1.1) not reachable |
|  7   |  Host3   |  Host5   | host (192.168.1.1) not reachable |
|  8   |  Host3   |  Host7   | host (192.168.1.1) not reachable |
|  9   |  Host5   |  Host6   |             success              |
|  10  |  Host5   |  Host1   | host (192.168.2.1) not reachable |
|  11  |  Host5   |  Host3   | host (192.168.2.1) not reachable |
|  12  |  Host5   |  Host7   | host (192.168.2.1) not reachable |
|  13  |  Host7   |  Host8   |             success              |
|  14  |  Host7   |  Host1   | host (192.168.3.1) not reachable |
|  15  |  Host7   |  Host3   | host (192.168.3.1) not reachable |
|  16  |  Host7   |  Host5   | host (192.168.3.1) not reachable |

##### 配置路由器端口地址

根据网络规划与设计，配置路由器 R1 的接口 IP 地址。配置时要先进入路由器全局配置模式，然后再进入接口模式，方可给该接口配置 IP 地址，配置命令如下。

```
config terminal
interface fastethernet 0/0
ip address 10.0.0.1 255.255.255.0
no shutdown
exit

interface fastethernet 1/0
ip address 192.168.0.1 255.255.255.0
no shutdown
exit

interface fastethernet 1/1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

exit
write
```

查看路由器 R1 的路由表信息，结果如下。

<img src="picture/image-20210409174117624.png" alt="image-20210409174117624" style="zoom:80%;" />

同理配置路由器 R2 的接口地址，并查看路由表信息，结果如下。

<img src="picture/image-20210409175041605.png" alt="image-20210409175041605" style="zoom:80%;" />

##### 再次测试网络连通性

再次使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机  |        `ping`测试结果        |
| :--: | :------: | :-------: | :--------------------------: |
|  1   |  Host1   |   Host2   |           success            |
|  2   |  Host1   |   Host3   |           success            |
|  3   |  Host1   |   Host5   | Destination host unreachable |
|  4   |  Host1   |   Host7   | Destination host unreachable |
|  5   |  Host3   |   Host4   |           success            |
|  6   |  Host3   |   Host1   |           success            |
|  7   |  Host3   |   Host5   | Destination host unreachable |
|  8   |  Host3   |   Host7   | Destination host unreachable |
|  9   |  Host5   |   Host6   |           success            |
|  10  |  Host5   |   Host1   | Destination host unreachable |
|  11  |  Host5   |   Host3   | Destination host unreachable |
|  12  |  Host5   |   Host7   |           success            |
|  13  |  Host7   |   Host8   |           success            |
|  14  |  Host7   |   Host1   | Destination host unreachable |
|  15  |  Host7   |   Host3   | Destination host unreachable |
|  16  |  Host7   |   Host5   |           success            |
|  17  |  Host1   | R1 / F0/0 |           success            |
|  18  |  Host1   | R1 / F1/0 |           success            |
|  19  |  Host1   | R1 / F1/1 |           success            |
|  20  |  Host1   | R2 / F0/0 |           timeout            |
|  21  |  Host1   | R2 / F1/0 | Destination host unreachable |
|  22  |  Host1   | R2 / F1/1 | Destination host unreachable |
|  23  |  Host5   | R1 / F0/0 |           timeout            |
|  24  |  Host5   | R1 / F1/0 | Destination host unreachable |
|  25  |  Host5   | R1 / F1/1 | Destination host unreachable |
|  26  |  Host5   | R2 / F0/0 |           success            |
|  27  |  Host5   | R2 / F1/0 |           success            |
|  28  |  Host5   | R2 / F1/1 |           success            |

分析

#### 配置路由器 R1 的静态路由并测试连通性

##### 配置路由器 R1 的静态路由

在路由器 R1 中，添加到达 192.168.2.0/24 和 192.168.3.0/24 这两个网络的路由信息，配置命令如下。

```
config terminal
ip route 192.168.2.0 255.255.255.0 10.0.0.2
ip route 192.168.3.0 255.255.255.0 10.0.0.2
```

查看路由器 R1 的路由表信息，结果如下。

<img src="picture/image-20210409190351580.png" alt="image-20210409190351580" style="zoom:80%;" />

##### 测试网络连通性

使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机  |        `ping`测试结果        |
| :--: | :------: | :-------: | :--------------------------: |
|  1   |  Host1   |   Host2   |           success            |
|  2   |  Host1   |   Host3   |           success            |
|  3   |  Host1   |   Host5   |           timeout            |
|  4   |  Host1   |   Host7   |           timeout            |
|  5   |  Host3   |   Host4   |           success            |
|  6   |  Host3   |   Host1   |           success            |
|  7   |  Host3   |   Host5   |           timeout            |
|  8   |  Host3   |   Host7   |           timeout            |
|  9   |  Host5   |   Host6   |           success            |
|  10  |  Host5   |   Host1   | Destination host unreachable |
|  11  |  Host5   |   Host3   | Destination host unreachable |
|  12  |  Host5   |   Host7   |           success            |
|  13  |  Host7   |   Host8   |           success            |
|  14  |  Host7   |   Host1   | Destination host unreachable |
|  15  |  Host7   |   Host3   | Destination host unreachable |
|  16  |  Host7   |   Host5   |           success            |
|  17  |  Host1   | R1 / F0/0 |           success            |
|  18  |  Host1   | R1 / F1/0 |           success            |
|  19  |  Host1   | R1 / F1/1 |           success            |
|  20  |  Host1   | R2 / F0/0 |           timeout            |
|  21  |  Host1   | R2 / F1/0 |           timeout            |
|  22  |  Host1   | R2 / F1/1 |           timeout            |
|  23  |  Host5   | R1 / F0/0 |           success            |
|  24  |  Host5   | R1 / F1/0 | Destination host unreachable |
|  25  |  Host5   | R1 / F1/1 | Destination host unreachable |
|  26  |  Host5   | R2 / F0/0 |           success            |
|  27  |  Host5   | R2 / F1/0 |           success            |
|  28  |  Host5   | R2 / F1/1 |           success            |

#### 配置路由器 R2 的静态路由并测试连通性

##### 配置路由器 R2 的静态路由

参考路由器 R1 的静态路由配置，在路由器 R2 中，添加所需要的静态路由信息，配置命令如下。

```
config terminal
ip route 192.168.0.0 255.255.255.0 10.0.0.1
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

查看路由器 R2 的路由表信息，结果如下。

<img src="picture/image-20210409192501936.png" alt="image-20210409192501936" style="zoom:80%;" />

##### 测试网络连通性

使用`ping`命令，测试各 PC 之间的连通性，通信结果如下表所示。

| 序号 | 请求主机 | 响应主机  | `ping`测试结果 |
| :--: | :------: | :-------: | :------------: |
|  1   |  Host1   |   Host2   |    success     |
|  2   |  Host1   |   Host3   |    success     |
|  3   |  Host1   |   Host5   |    success     |
|  4   |  Host1   |   Host7   |    success     |
|  5   |  Host3   |   Host4   |    success     |
|  6   |  Host3   |   Host1   |    success     |
|  7   |  Host3   |   Host5   |    success     |
|  8   |  Host3   |   Host7   |    success     |
|  9   |  Host5   |   Host6   |    success     |
|  10  |  Host5   |   Host1   |    success     |
|  11  |  Host5   |   Host3   |    success     |
|  12  |  Host5   |   Host7   |    success     |
|  13  |  Host7   |   Host8   |    success     |
|  14  |  Host7   |   Host1   |    success     |
|  15  |  Host7   |   Host3   |    success     |
|  16  |  Host7   |   Host5   |    success     |
|  17  |  Host1   | R1 / F0/0 |    success     |
|  18  |  Host1   | R1 / F1/0 |    success     |
|  19  |  Host1   | R1 / F1/1 |    success     |
|  20  |  Host1   | R2 / F0/0 |    success     |
|  21  |  Host1   | R2 / F1/0 |    success     |
|  22  |  Host1   | R2 / F1/1 |    success     |
|  23  |  Host5   | R1 / F0/0 |    success     |
|  24  |  Host5   | R1 / F1/0 |    success     |
|  25  |  Host5   | R1 / F1/1 |    success     |
|  26  |  Host5   | R2 / F0/0 |    success     |
|  27  |  Host5   | R2 / F1/0 |    success     |
|  28  |  Host5   | R2 / F1/1 |    success     |

分析

---



# 2 网络应用服务架构



---

## 2.2 电子邮件服务架构

### 2.2.1 实验原理

首先我们简要介绍一些邮件发送过程中的基本概念：

- MUA（Mail User Agent）：用户邮件代理——用户邮件代理是具有发送电子邮件能力，能够从电子邮件服务器为用户获取邮件且进行管理的任意客户端程序，常见的MUA有Outlook，Foxmail和Thunderbird等。

- MTA（Mail Transfer Agent）：邮件传输代理——邮件传输代理在MUA的角度看来与邮件服务器的概念是重合的。其是一种能够接受客户端电子邮件，存储然后将其发送给正确的接收MTA；接收方的MTA则检查收到的电子邮件是否在其接收范围，再决定是否将其投送至MDA。MTA是整个电子邮件系统中的核心部分，例如Postfix和Exchange都属于MTA范畴。

- MDA（Mail Delivery Agent）：邮件分发代理——邮件分发代理的作用是从MTA接收邮件且将其存储到对应用户的邮件目录下，允许用户获取其邮件。且很多时候MDA直接集成于MTA中，在整个电子邮件系统中经常被忽略。如dovecot就属于MDA的范畴。

- SMTP（Simple Mail Transfer Protocol）：简单邮件传输协议——SMTP是一种提供可靠且有效的电子邮件传输的协议，其规定了在两个相互通信的SMTP进程之间应如何交换信息。当两个SMTP进程交换信息时，负责发送邮件的SMTP进程被称为SMTP客户端，而负责接收邮件的SMTP进程就是SMTP服务器。

- ESMTP（Extended SMTP）：最初的SMTP是为传送ASCII码设计的，虽然后来有了MIME可以传送二进制数据，但在传送非ASCII码的长报文时，在网络上的传输效率是不高的；此外SMTP传送的邮件是明文，不利于信息安全。为了解决该问题，2008年RFC 5321对SMTP进行了扩充，称为扩充的SMTP，其在原有的命令框架中添加扩展参数，使得SMTP的功能被大大拓展了。

- POP3（Post Office Protocol Version 3）/IMAP（Internet Message Access Protocol）：邮局协议版本3/网际报文存取协议——POP3与IMAP的功能都是从电子邮件服务器读取数据到本地，两者有明显的差别，主要的区别是POP3读取服务器的邮件后，服务器将删除该邮件，不够方便；而IMAP则是将邮件复制到本地且保持同步。具体的功能差别见下表：

  | 操作位置   | 操作内容                     | IMAP                 | POP3         |
  | ---------- | ---------------------------- | -------------------- | ------------ |
  | 收件箱     | 阅读、标记、移动、删除邮件等 | 客户端与邮箱同步更新 | 仅在客户端内 |
  | 发件箱     | 保存到已发送                 | 客户端与邮箱同步更新 | 仅在客户端内 |
  | 创建文件夹 | 新建自定义的文件夹           | 客户端与邮箱同步更新 | 仅在客户端内 |
  | 草稿       | 保存草稿                     | 客户端与邮箱同步更新 | 仅在客户端内 |
  | 垃圾文件夹 | 接收并移入垃圾文件夹的邮件   | 支持                 | 不支持       |
  | 广告邮件   | 接收并移入广告邮件夹的邮件   | 支持                 | 不支持       |

然后，在抽象的层面上，我们考察一封邮件从发送到被收件人获取的生命周期，其大致可以被概括为4个步骤：

1. MUA通过SMTP向本地MTA发送邮件，电子邮件被本地MTA转储
2. 本地MTA尝试发送（可能经过MTA中继），直到被接收方MTA接收或者超出指定的时间发送失败
3. 接收方MTA将收到的电子邮件传送给MDA，由MDA将电子邮件存储到收件者的邮件目录
4. MUA通过POP3/IMAP从MDA获取电子邮件

```
发送邮件:    MUA ----> MTA ----> (MTA relays) ----> MDA
接收邮件:    MUA <--------------------------------- MDA
```

最后，简要介绍本次实验需要用到的Docker与Docker Compose。Docker是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到需要部署服务的机器上，可以高效、安全地架构服务。Compose是用于定义和运行多容器Docker应用程序的工具。通过Compose，可以使用 YAML 文件来配置Docker应用程序，方便的从YAML配置文件创建并启动所需服务。

### 2.2.2 实验环境

本部分实验使用一台操作系统为Arch Linux，内核为5.11.15-arch1-2，图形界面采用KDE Plasma 5的个人计算机。系统装有抓包分析的Wireshark与提供虚拟机支持的VMware，用于模拟不同邮件服务器组网与分析电子邮件服务器运行过程中网络上产生的通信；另外，宿主计算机上配置使用的邮件客户端为Thunderbird，用于代表MUA执行电子邮件的发送与获取。

VMware安装维护两台系统为CentOS 8的虚拟机，需要配置网络连接，支持提供docker服务，两台虚拟机将代表两个互相独立的电子邮件服务器。

### 2.2.3 实验内容与结果

#### 2.2.3.1 环境准备

检查VMware为虚拟机提供的网络环境，实验中虚拟机利用NAT的联网，连接因特网时与主机共享IP。

<img src="mail-pic/1.png" alt="network" style="zoom:80%;" />

下载CentOS 8的ISO文件，安装第一台虚拟机命名为mailserver1，由于测试环境搭建在虚拟机上，方便起见直接使用root账户进行测试。虚拟机安装完成后，参考`https://docs.docker.com/engine/install/centos/`与`https://docs.docker.com/compose/install/`文档页面安装docker与docker-compose。

![docker](mail-pic/2.png)

本实验选择利用开源项目`docker-mailserver`架构邮件服务器，可以在`https://github.com/docker-mailserver`与`https://docker-mailserver.github.io/docker-mailserver/v9.1/`找到其源代码与使用文档；这是一个简单但功能齐全的邮件服务器，其于整个电子邮件系统中，位于MTA与MDA的位置，项目内部使用非常流行的开源软件postfix和dovecot分别实现相应功能且附带一份可用的默认配置，非常适合用来快速架构邮件服务与进行测试，避免过于琐碎的配置细节掩盖本次实验的核心内容。

确认docker服务可用后，运行命令`docker pull docker.io/mailserver/docker-mailserver:latest`，从镜像源拉取`docker-mailserver:latest`到本地，运行`docker image ls`可以看到本地的可用镜像。

![docker image list](mail-pic/3.png)

创建mail文件夹，根据文档说明获取对应镜像版本的配置文件与管理脚本（需配置代理服务器，此处略过）。

```
wget -O .env https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/master/compose.env
wget https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/master/docker-compose.yml
wget https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/master/mailserver.env
wget https://raw.githubusercontent.com/docker-mailserver/docker-mailserver/v9.1.0/setup.sh
# 赋予脚本执行的权限
chmod a+x ./setup.sh
```

配置好mailserver1后，将其复制，修改主机名为mailserver2，作为实验中另一台邮件服务器。

另外，本实验在VMware创建的虚拟局域网vmnet8中进行，采用DHCP的方式对IP地址进行分配，但在本次实验期间，可认为宿主机与虚拟机均具有固定不变的IP地址。

| 主机名称      | IP地址         |
| ------------- | -------------- |
| fyy（宿主机） | 192.168.36.1   |
| mailserver1   | 192.168.36.130 |
| mailserver2   | 192.168.36.131 |

#### 2.2.3.2 邮件服务器启动与配置

本实验中，我们认为`mailserver1`匹配域名`mailserver1.com`，而`mailserver2`匹配域名`mailserver2.com`，由于我们不实际拥有这两个域名的所有权，且实验用的两台虚拟机共享同一个公网IP，我们不能使用因特网上的DNS服务器完成翻译域名到IP步骤。本实验采用修改hosts文件指定域名与IP地址映射关系的方式进行替代实现。

由于postfix默认绕过hosts文件，需要于config目录下提供额外的配置文件`postfix-main.cf`，文件内容指定smtp在进行电子邮件发送时的寻址方式。

```
smtp_host_lookup = native
```

同时需要修改docker-compose.yml配置文件指定额外的host配置用于寻址，此处以`mailserver1`为例，`mailserver2`照此方式修改指定`mailserver1.com`与映射的IP地址即可。

```yaml
services:
  mailserver:
	...
    extra_hosts:
      - "mailserver2.com:192.168.36.131"
  	...
```

切换至mail目录下，输入`docker-compose up -d mailserver`从`docker-compose.yml`启动邮件服务器，`-d`选项代表`detached mode`，代表后台启动服务，与当前终端分离，仅回显启动容器的名称。

![docker-compose up](mail-pic/4.png)

使用`docker-compose ps`查看服务状态，注意在默认设置中POP3与110端口已经被弃用，实验中仅采用IMAP从MDA获取邮件。

![docker-compose status](mail-pic/5.png)

`mailserver2`的启动操作与上述操作完全一致，可以直观体会到docker部署与docker-compose管理的优势。

完成服务器的部署后我们需要创建测试用的账户，我们可以方便地使用`setup.sh`脚本完成。

```
./setup.sh help
	...
	COMMAND email :=
        ./setup.sh email add <EMAIL ADDRESS> [<PASSWORD>]
        ./setup.sh email update <EMAIL ADDRESS> [<PASSWORD>]
        ./setup.sh email del [ OPTIONS... ] <EMAIL ADDRESS>
        ./setup.sh email restrict <add|del|list> <send|receive> [<EMAIL ADDRESS>]
        ./setup.sh email list
    ...
EXAMPLES
	./setup.sh email add test@domain.tld
	...
```

最后在`mailserver1`添加`yzh@mailserver1.com`的邮箱，`mailserver2`添加`yzh@mailserver2.com`的邮箱。

![mailserver1 email list](mail-pic/6.png)

![mailserver2 email list](mail-pic/7.png)

#### 2.2.3.3 邮件客户端配置与邮件收发

首先修改主机hosts文件，指定`mailserver1.com`与`mailserver2.com`的地址。

配置Thunderbird，将两邮件账户与相应的电子邮件服务器均添加到电子邮件客户端中，实验中我们smtp选择默认不加密的25端口，且配置IMAP关闭加密，以此方便之后的抓包分析，实际因特网的邮件服务使用加密连接是非常重要的，之后的实验分析部分也可以看到服务器间的smtp连接仍是默认加密的。

<img src="mail-pic/8.png" alt="mail client setting" style="zoom: 67%;" />

`mailserver2`的邮件客户端配置略过，配置完成后于邮箱列表中看到邮件服务器上相应的文件夹配置，表明我们的邮件客户端已经成功的通过IMAP与MDA建立了通信。

<img src="mail-pic/9.png" alt="client list" style="zoom:80%;" />

接下来我们对smtp发送电子邮件进行测试，使用`yzh@mailserver1.com`新建一封发送给`yzh@mailserver2.com`的测试邮件进行发送。

<img src="mail-pic/10.png" alt="testing mail" style="zoom:75%;" />

发送后，一小段时间，`yzh@mailserver2.com`的收件箱就出现了我们刚刚发送的邮件，测试发件收件过程成功。

<img src="mail-pic/11.png" alt="notification" style="zoom: 67%;" />

<img src="mail-pic/12.png" alt="received content" style="zoom:75%;" />

### 2.2.4 实验分析

我们已经成功利用架构的邮件服务器达成了邮件发送，从使用者的感知到了邮件服务的正常运作，本部分将再次重复发件的过程，利用Wireshark抓包，具体分析邮件发送过程中运输层具体发生了什么。

启动Wireshark，于vmnet8虚拟局域网上进行抓包，配置过滤器只抓取25与143端口的tcp包以忽略非邮件系统产生的tcp与udp通讯。

<img src="mail-pic/13.png" alt="wireshark overview" style="zoom:50%;" />

我们将通过`yzh@mailserver2.com`对之前的测试邮件进行回复 ，再检查该过程中的抓包结果。

<img src="mail-pic/14.png" alt="reply mail" style="zoom:65%;" />

在抓取的tcp包中找到协议显示为SMTP/IMF与IMAP/IMF，在Wireshark对包的解析中，可以看到被电子邮件在传输层是以何种格式进行传输的，虽然Wireshark的解析不支持中文编码，但英文部分的邮件内容可以清晰的分辨。可以发现数据包中标识了大量的信息，许多信息在邮件客户端中对用户而言是透明，而在抓包分析时则发现远超用户所见的信息将在客户端与邮件服务器间通讯。

<img src="mail-pic/15.png" alt="smtp tcpdump" style="zoom:50%;" />

<img src="mail-pic/16.png" alt="imap tcpdump" style="zoom:50%;" />

另外，除了我们我们不加密的明文SMTP与IMAP通信外，可以看到两台邮件服务器间的通信进行了加密，我们无法从抓包的内容中直接捕获到邮件内容，保证了信息传输的安全性。

<img src="mail-pic/17.png" alt="encrypted smtp" style="zoom:50%;" />

接下来，我们结合抓包分析SMTP发送中的三个阶段过程，图中对关键部分做了标注：
<img src="mail-pic/18.png" alt="three steps in smtp" style="zoom:75%;" />

1. 建立连接：客户端发现有邮件需要发送，与接收方的25端口建立TCP连接，此时SMTP服务器返回`220 Service Ready`告知客户端服务就绪，注意到图中标识协议为ESMTP，即拓展的SMTP，所以才能够收发非ASCII码的中文字符；然后客户端向服务器发送EHLO命令（在SMTP中应发送HELO，而ESMTP为了区分服务器的对拓展协议的支持性，将命令修改为EHLO），服务器应答`250 OK`表示有能力接收ESMTP发送的邮件；最后客户端利用AUTH命令进行身份验证，服务器返回`235 Authentication OK`表示验证通过，允许客户端进行邮件发送。
2. 邮件传送：邮件传送从MAIL命令开始，服务器继续应答`250 OK`表示准备好接收，紧接着RCPT命令指定收件人，服务器返回`250 OK`表明接收方验证成功，由于仅有一个收件人，故仅有一条RCPT指令；客户端发送DATA指令表示开始传送邮件内容，服务器返回`354`告知客户端邮件数据应当以`<回车>.<回车>`的格式标识结束；接收完毕后，服务器返回`250 OK`表明邮件收到了。
3. 连接释放：客户端最后发送QUIT命令，告知服务器断开连接，而服务器返回`221`同意释放tcp连接，关闭服务，至此一次完整的SMTP连接完成了。

# 3 拓展性实践