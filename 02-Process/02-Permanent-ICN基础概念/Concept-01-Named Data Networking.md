---
tags:
  - NDN
created: 2025-11-14
aliases:
  - Named Data Networking
---
### 为什么我们需要ICN？
分布式网络底座：在分布式网络的今天，传统的TCP/IP网络架构仍然是中心化的。如果网络基架是以中心化的思想为目标设置的，就永远做不到真正的分布式网络（底层仍需要中心机构）。这就是分布式网络化之路上的障碍。[[Related-02-Identifying Roadblocks in Building Decentralized Apps|Roadblocks]]
下一代网络：本文建议采用命名数据网络（NDN）作为未来蜂窝网络（包括6G）的基础，将重点从基于连接的通信转移到以数据为中心的通信。NDN在网络层保护数据，减少控制平面信令开销，实现安全的分布式部署。[[BG-04-Envisioning the Next-Generation Cellular Architecture With Named Data Networking|展望下一代蜂窝体系结构]]
为了实现直接的用户到用户通信，U2U。[[BG-07-On Empowering End Users in Future Networking|论未来网络中的终端用户赋权]]

### 什么是NDN主机？
[[BG-06-NDN Host Model|NDN Host Model]]
通俗的主机理解：它是一个运行应用程序的物理或虚拟设备。通过协议提供IP地址，转发配置和其它参数。主机封装并将应用程序数据发送到由IP地址标识的目的地。
NDN主机的基础概念：它是一个运行应用程序的节点。由于通信模型的变化，NDN主机在一些基本方面与IP主机不同，这些方面涉及到要配置的参数及其对应用程序的影响。
NDN主机和应用程序名称不一定与主机连接的网络名称相关联。当移动的主机将其自身连接到新网络时，它可能不需要重新提供其主机和应用程序名称;但是，它可能需要重新通告这些名称，以便消费者请求可以转发到主机。

### IP主机的基本功能和配置
在IP中，网络层与应用层是独立而且解耦的。
在网络层：有{IP地址空间}。连接时，需要使用{DHCP}等协议来完成{IP地址、网络前缀、网关路由器、和DNS服务器}的配置。
在应用层：由应用程序自身执行配置。
### NDN中的基本概念
NDN中的网络分层：应用层、网络层、数据链路层、物理层。
NDN中拥有名称的被称为实体：主机、应用程序、用户等。这个名称与公钥/私钥相关联，公钥由CA来认证。
除了NFD外，使用face来对上下层接口去做抽象。
### NDN主机的功能
以下表格总结了一个NDN主机应具备的功能，以及对应的所需配置。

| 视角  | 功能     | 需要的配置               | 配置方法   |
| --- | ------ | ------------------- | ------ |
| 应用层 | 应用运行环境 | -                   | Sync协议 |
|     | 命名空间管理 | 名称、公私钥对和证书、信任规则和信任锚 | 安全引导   |
| 网络层 | 包调度器   | 接口、FIB、转发策略         |        |
|     | 包承载器   | 缓存策略                |        |
##### 1. 应用运行环境提供商
包括常见的系统资源，如CPU和内存。也包括数据获取和NDN的传输功能，如分布式数据集同步协议。
```
[14] Wentao Shang, Yingdi Yu, Lijing Wang, Alexander Afanasyev, and Lixia Zhang. A survey of distributed dataset synchronization in named data networking. Technical Report NDN-0053, NDN, May 2017.
```
##### 2. 命名空间管理者 Namespace Manager
对应的是 [[BG-01-An Overview of Security Support in Named Data Networking|Security Bootstrapping]]
```
[15] Zhiyi Zhang, Yingdi Yu, Haitao Zhang, Eric Newberry, Spyridon Mastorakis, Yanbiao Li, Alexander Afanasyev, and Lixia Zhang. An overview of security support in Named Data Networking. Technical Report NDN-0057, Revision 3, NDN, June 2018.
```
##### 3. 包调度器 Packet Dispatcher

##### 4. 包承载器 Packet Mule

### NDN主机的配置
可以把主机的初始配置分为两类：Security Bootstrapping 和 Initial Forwarding Configurations。初始配置完成后，通过Namespace Reachability完成名称发现与传播，来完成移动性支持。
##### 安全引导
1. 获取一个名字。
2. 学习信任锚和信任规则。
3. 生成公私钥对，并获取证书。
安全引导是目标是建立初始的信任，这期间会涉及到Interest/Data交换。
##### 初始转发配置
初始化face，建立最初的FIB条目，比如`/localhop`。参考NFD Developer's Guide。
##### 命名空间可达性
兴趣转发由转发策略决定NDN主机（例如Alice的电话）通常不运行路由协议，因此其FIB不受路由协议操纵。相反，转发模块使用{默认路由、自学习、和本地应用注册}，以及对连接性改变的自动适应。
通常而言，当新的命名空间到达时，本地的NFD将据此创建新的FIB条目。
其它创建新的FIB条目的方法包括：自学习、主动传播等。
```
自学习
[16] Junxiao Shi, Eric Newberry, and Beichuan Zhang. On broadcast-based selflearning in named data networking. In Proceedings of IFIP Networking, 2017.
自动传播
[17] Yanbiao Li, Alexander Afanasyev, Junxiao Shi, et al. NDN automatic prefix propagation. Technical Report NDN-0045, NDN.
```
或者当连接情况改变后，由FIB自适应触发。

### NDN与IP主机功能和配置的比较分析
##### 1. 地址 vs 拓扑-独立名称
使用IP地址，主机移动后需要重新配置。而使用名称，由于与拓扑无关，所以不需要重新配置。
这里提到了Forwarding Hint。使用拓扑无关的名称进行转发时，可以使用Hint使数据可达，作为使用应用程序名称进行网络层传递的收益的必要成本。
##### 2. 安全：基于信道 vs 数据中心
##### 3. 转发
##### 4. 数据缓存
### 自动化NDN主机配置所需的剩余任务




## 参考文献

介绍NDN的论文
[5] L. Zhang, A. Afanasyev, J. Burke, V. Jacobson, K. claffy, P. Crowley,
C. Papadopoulos, L. Wang, and B. Zhang, “Named data networking,”
SIGCOMM Comput. Commun. Rev., vol.44, no.3, pp. 66–73, July
2014.