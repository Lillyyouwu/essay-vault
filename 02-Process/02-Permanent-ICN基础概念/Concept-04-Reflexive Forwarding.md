---
tags:
  - NDN
  - Forwarding
created: 2025-11-17
---
[RFC](https://www.ietf.org/archive/id/draft-oran-icnrg-reflexive-forwarding-01.html)
对该草案的翻译：[[RFC-Reflexive Forwarding for CCNx and NDN Protocols]]。

## 1.1 问题应用场景
1. 远程方法调用：需要调用方提供参数，使用Interest携带会导致请求膨胀。已有方法为RICE。痛点：请求膨胀。
2. “回拨”场景：经典推送场景。已有方法是“Interest-Interest-Data”交换。痛点：如何推送。
3. 对等体状态同步：已有方法需要三次或更多次握手实现，同时存在中间人攻击等风险。痛点：流程繁琐，安全问题。
## 1.2 已有方案的局限性
A. 直接由Interest携带数据
1. ICN建议将Interest消息控制在较小规模，避免分片。
2. 大包Interest若得不到响应，则会浪费带宽资源。
B. 进行独立的Interest-Data交换
需要反转消费者-生产者的角色。
3. 路由问题：消费者的前缀也需要在在网络中传播，或者使用额外的路由机制。
4. 安全问题：此名称由消费者指定，可能会被利用，向目标发起反射攻击。
5. 状态管理隐患：经验表明维护状态比较复杂。
## 2 流程概述

1. I1 -> 初始请求，沿途下载反射RI前缀RNP。
2. 生产者创建RI状态。
3. RI <-，沿着RNP转发。
4. D1 <- 回复I1。
5. D2 -> , 回复RI，沿途移除RNP。
关键设计要素：命名、FIB动态配置、状态耦合。
### 2.1 反射请求RI命名
定义了一种新的组件类型：反射兴趣包名称组件。
其值是由==消费者==分配的64位随机数，用于在未完成兴趣包-数据包交换期间唯一标识发起消费者。
传递方式：一个单独的TLV，或者添加到Interest前。
### 2.2 转发器行为
1. FIB动态创建：资源不足时，拒绝Interest。与PIT资源不足时一致。
2. FIB查询
3. 状态清理
### 2.3 状态耦合
消费者行为：定义反射名称。收到数据包后立即销毁交换状态。
生产者行为：定时销毁共享状态。
## 3 使用案例
### 3.1 通过反射兴趣包实现远程方法调用
RICE流程启发了RI的设计。同时，RI也可以改善RICE的流程。
### 3.2 RESTful Web
采用反射兴趣包转发实现RESTful网络交互时，原始请求将编码REST请求及反射兴趣包前缀，服务器可利用该前缀反向获取客户端认证凭证和Cookie等参数。
### 3.3 物联网设备响应

## 4 安全考量
为实现多轮握手，现有设计需消费者向生产者告知全局可路由名称，这为消费者利用生产者向指定目标发起反射攻击提供了便利。
### 4.1 RI的命名冲突
使用可靠的随机数生成方法。
### 4.2 对PIT和FIB的额外资源消耗
常规CCNx和NDN兴趣包处理需考虑PIT资源耗尽攻击（尤其是兴趣包泛洪攻击）的影响。
### 4.3 隐私问题
隐私权衡的核心矛盾在于：基于名称的路由转发设计必然需向网络转发元件暴露信息对象名称。
> 反射式转发既未改变隐私权衡的整体格局，也未引入新的隐患。实际上，隐私暴露仅局限于生产者到消费者的逆向转发路径，而原始兴趣包转发可能已在该路径暴露名称。上述隐私技术同样适用于反射兴趣包中的名称保护。
## 参考文献
```
[9] David R. Oran and Dirk Kutscher. 2022. Reflexive Forwarding for CCNx and NDN
Protocols. Internet-Draft draft-oran-icnrg-reflexive-forwarding-02. IETF Secretariat. https://www.ietf.org/archive/id/draft-oran-icnrg-reflexive-forwarding-
02.txt https://www.ietf.org/archive/id/draft-oran-icnrg-reflexive-forwarding-
02.txt.
```