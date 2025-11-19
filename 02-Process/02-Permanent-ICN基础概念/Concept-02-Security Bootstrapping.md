---
tags:
  - NDN
  - Boostrapping
created: 2025-11-17
---
分为连接与配置两个课题。
关于配置，基本可以等同于目前的Security Bootstrapping及相关方案实现。
关于连接，目前讨论的更多是路由协议、消费者如何获取名称，以及Interest如何转发。有关生产者的部分，讨论的更多是生产者移动性的问题如何解决。



## 库内链接

## 说明性工作

系统讨论了NDN中的DHCP课题，可以分为（安全）配置与网络连接（路由建立）：[[KEY-01-Enabling Plug-n-Play in Named Data Networking|Plug-n-Play]]
两页，简短地介绍了NDN安全模型：[[BG-01-An Overview of Security Support in Named Data Networking |Overview of Security Support]]
系统介绍了NDN安全引导工作的范式：[[BG-01-An Overview of Security Support in Named Data Networking|Security Bootstrapping]]

### 安全配置方案
如何在域间建立安全模型：[[BG-03-Intertrust establishing inter-zone trust relationships|Intertrust]]
### 安全引导方案
四步协议步骤，完成密钥交换：[[Ref-05-PION Password-based IoT Onboarding Over NDN|PION]]
密码学方法，生成可用密钥：[[Ref-06-BAS-NDN|BAS-NDN]]
### 网络连接方案
[[KEY-02-On broadcast-based self-learning in named data networking]]

## 参考文献

[[BG-01-An Overview of Security Support in Named Data Networking]]
所有Security Bootstrapping 的起点。
[1] Z. Zhang et al., "An Overview of Security Support in Named Data Networking," in IEEE Communications Magazine, vol. 56, no. 11, pp. 62-68, November 2018, doi: 10.1109/MCOM.2018.1701147.

[[BG-05-On the Security Bootstrapping in Named Data Networking]]

在Cornerstone中，总结了近几年的Bootstrapping文章，引用如下：
```
[10] Yanbiao Li, Zhiyi Zhang, Xin Wang, Edward Lu, Dafang Zhang, and Lixia Zhang. 2019. A secure sign-on protocol for smart homes over named data networking. IEEE Communications Magazine 57, 7 (2019), 62–68.

[13] Kathleen Nichols. 2021. Trust schemas and ICN: key to secure home IoT. In
Proceedings of the 8th ACM Conference on Information-Centric Networking. 95–
106.

[14] Davide Pesavento, Junxiao Shi, Kerry McKay, and Lotfi Benmohamed. 2022. PION: Password-based IoT onboarding over named data networking. In ICC 2022-IEEE International Conference on Communications. IEEE, 1070–1075.

[16] Sanjeev Kaushik Ramani, Proyash Podder, and Alex Afanasyev. 2020. NDNViber: Vibration-Assisted Automated Bootstrapping of IoT Devices. In 2020 IEEE International Conference on Communications Workshops (ICC Workshops). IEEE, 1–6.

```