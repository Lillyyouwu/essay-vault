---
tags:
  - review
created:
status: todo
aliases:
  - ndn-autoconfig
---
#### 标题
ndn-autoconfig
#### 作者
NFD操作手册
#### 引用格式
```
[11] Named Data Networking Project, “ndn-autoconfig,” https://docs.named-data.net/NFD/current/manpages/ndn-autoconfig.html
```
#### 链接
[ndn-autoconfig](https://docs.named-data.net/NFD/current/manpages/ndn-autoconfig.html)
#### 概述
当一个终端主机启动时，或检测到其网络环境发生变化时，可通过此流程来发现NDN路由器，以接入NDN研究测试床。此流程既能发现本地网络中的NDN路由器，也能发现NDN测试床网关路由器（通常称为“枢纽节点”）。

本流程包含四种发现NDN路由器的方法：

1. 通过组播方式寻找本地NDN路由器。适用于家庭或小型办公网络。
    
2. 通过携带默认后缀的DNS查询寻找本地NDN路由器。适用于网络管理员在大型企业网络中配置NDN路由器的场景。
    
3. 向NDN-FCH服务器发送HTTP请求，查找最近的枢纽节点。
    
4. 根据用户证书连接归属枢纽节点。此方法可确保用户从任意位置获得网络连接。
    

连接建立后，将在路由器上注册两个前缀：

1. `/`—— 支持应用程序通信
2. `/localhop/nfd`—— 用于向NFD-RIB通告已建立路由器连接
#### 点评


## AI总结