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

### 什么是NDN？
[[BG-06-NDN Host Model|NDN Host Model]]

## 参考文献

介绍NDN的论文
[5] L. Zhang, A. Afanasyev, J. Burke, V. Jacobson, K. claffy, P. Crowley,
C. Papadopoulos, L. Wang, and B. Zhang, “Named data networking,”
SIGCOMM Comput. Commun. Rev., vol.44, no.3, pp. 66–73, July
2014.