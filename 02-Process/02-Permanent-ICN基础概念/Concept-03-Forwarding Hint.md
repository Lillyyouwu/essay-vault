---
tags:
  - NDN
  - Forwarding
  - ForwardingHint
created: 2025-11-13
---
来自论文 《SNAMP: Secure namespace mapping to scale NDN forwarding》[[KEY-05-SNAMP Secure namespace mapping to scale NDN forwarding|Forwarding Hint]]
网页介绍：[Interest Packet Format](https://docs.named-data.net/NDN-packet-spec/current/interest.html)
## 基础概念
Forwarding Hint （以下简称Hint）, 是命名数据网（Named Data Network）中，请求包（Interest Packet）中的一个可选字段。
```
ForwardingHint = FORWARDING-HINT-TYPE TLV-LENGTH 1*Name
```

转发提示元素包含一个名称列表（“委托”）。转发提示的出现意味着可以通过将兴趣包沿所列名称指向的路径进行转发来检索数据。带有转发提示的兴趣包的转发逻辑的具体细节将在单独的文件中定义。
> The ForwardingHint element contains a list of Names (“delegations”). Presence of the forwarding hint implies that Data can be retrieved by forwarding the Interest over path(s) pointed by the listed Names. Specifics of the forwarding logic for Interests with `ForwardingHint` will be defined in a separated document.

## 转发流程
基础的Interest转发流程为：
CS -> PIT -> FIB。
在查询FIB阶段，如果Interest的原始名字，在FIB中没有路由时，则会使用Hint指示的名称，在FIB中进行匹配。



## 参考文献

```
[1] A. Afanasyev, C. Yi, L. Wang, B. Zhang and L. Zhang, "SNAMP: Secure namespace mapping to scale NDN forwarding," 2015 IEEE Conference on Computer Communications Workshops (INFOCOM WKSHPS), Hong Kong, China, 2015, pp. 281-286, doi: 10.1109/INFCOMW.2015.7179398. keywords: {World Wide Web;Routing;Internet;Routing protocols;IP networks;Scalability;Proposals},
```