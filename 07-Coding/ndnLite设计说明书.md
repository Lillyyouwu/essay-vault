---
tags:
  - ndnLite
created: 2025-12-09
---
项目组织
```c
ndnLite/
├── CMakeLists.txt
├── include/
│   ├── API/ 
│   │   ├── api.h # 外部app使用此头文件里的接口
│   ├── forwarder/
│   │   ├── cs.h # 都是单独的模块实现
│   │   ├── pit.h
│   │   ├── fib.h
│   │   ├── face.h
│   ├── parser/ # 单独的模块实现
│   │   ├── parser.h 
│   ├── io_engine/ # 单独的模块实现
│   │   ├── io_engine.h
│   ├── core/
│   │   ├── core.h
│   ├── common/ # 其它模块中的通用头文件
│       ├── name.h
│       └── log.h
├── src/
│   ├── main.c # 启动用户态ndn协议栈
│   ├── API/
│   │   ├── consumer.c # 待定
│   │   ├── producer.c
│   ├── forwarder/
│   │   ├── cs.c
│   │   ├── pit.c
│   │   ├── fib.c
│   │   ├── face.c
│   ├── parser/
│   │   ├── parser.c 
│   ├── io_engine/
│   │   ├── sender.c
│   │   ├── receiver.c
│   │   ├── mmap.c
│   ├── core/
│   │   ├── input.c
│   │   ├── output.c
│   │   ├── core.c
│   ├── utils/
│       ├── name.cpp
│       ├── nameTrie.cpp
│       └── log.c
└── apps/ # 外部应用示例，使用 API中的接口
│   ├── skeleton/
│   │   ├── consumer.c
│   │   ├── producer.c

```

# 1. io_engine
网络包收发模块，协议栈最底层。接收来自上层`parser`传下来的封装好的raw数据包，使用AF_PACKET+SOCKRAW的方式发送。通过RAW_SOCKET+混杂模式，过滤接收到指定ether type = ETH_P_NDN的以太网络包，然后向上交付给`parser`模块去解析。不对网络包做解包处理，会统计网络信息。
### 1.1 文件说明
```c
// 封装ether type并接收/发送raw bytes
sender.c
receiver.c
// 使用mmap技术构建存储包的环形缓冲区
mmap.c
```
### 1.2 功能实现
两个缓冲区，发送、接收独立使用。
上层接口：用{src_mac, dst mac, buf, buf_len}指示一个包，交给parser层去处理。
下层接口：封装好的ethernet packet，通过指定网卡接收、发送。

使用ETH_P_NDN = 0x0609去封装独特的ndn包。打开raw_socket时，也使用ETH_P_NDN。
使用mmap映射出一段内存区域，作为缓冲区。缓冲区是以byte为单位的，通过buf指针和buf_len，可以确定一段ethernet packet的数据部分。

发送：上层的接口将待发送的数据，用{char buf[], buf_len, src_mac(ifname), dst_mac}的形式交给io_engine模块， 由io_engine封装ndn以太包从指定网卡发送出去。（这样看来似乎不需要发送的缓冲区？）io_engine不会分析buf内raw bytes的语义，只是执行封装、发送的操作。由parser保证了单个包的长度已经分片完成，由一个以太包发送即可。

接收：接收到的以太包，首先进行简单过滤，ether type = 使用ETH_P_NDN的包进入缓冲区，等待receiver处理并向上交付。向上交付时，填充好{char buf[], buf_len, src_mac(ifname), dst_mac}等字段，交付给parser层去做解析。这里的交付不涉及拷贝，parser层直接在缓冲区内去读取、解析。parser层处理完包后，应有个接口，使缓冲区内的指针移动。


# 2. parser
解析层，协议栈自底向上的第二层。接收io_engine传来的包，根据ndn包的封装规则对raw data进行解析，将解析好的数据通过结构体传递给上层`forwarder`去处理。接收经过forwarder处理，要转发出去的包，经过必要的ndn规则处理后，转化成raw_data，交给io_engine去发送。
### 2.1 文件说明
```c
// interest packet和data packet的结构体定义
parser.h // 后续可能改名
// raw byte <==> packet结构体的互相转换
parser.c
```
目前就只用一个文件来实现全部的功能了。
tlv格式规定
```
type：2B 从0xa001开始
length：2B 指示后续有多少个字节，范围 [0~65535]
value：使用unsigned char来承接，单个元素最大为[256]

```

一个包的格式是这样的：
Type : {Interest | Data}, Length: {total packet_length}, Value: { {tlv1}, {tlv2}, ...}


# 3. forwarder
ndn转发器，协议栈自底向上的第三层。功能与NFD一致，ndn协议栈的灵魂模块，包含CS, PIT, FIB。在forwarder层决定网络包是向上交付本机还是向外转发。
### 3.1 文件说明
```c
// Content Store实现与API
cs.c
name - packet
// Pending Interest Table实现与API
pit.c
name - input_face, output_face, expired_time
// Forwarding Information Base实现与API
fib.c
name - output_face
// Face实现与API
face.c
upper - process id / fd
lower - net interface
```
# 4. API
ndn基本组件，协议栈自底向上的第四层。对sendInterest, recvInterest, sendData, recvData的一个封装。避免真实的ndn应用直接调用forwarder等接口，而是使用ndn_basic_component去构建自己功能丰富的ndn app。
### 4.1 文件说明
```
// 暂时没有想好

```

### 4.2 功能实现
基础的四个功能：
- sendInterest：设置好name，填充一些选项（如有），向下交付发送即可。
- sendData：设置好name，与data。在应用层等待：先监听Interest，收到后回复向下交付发送。在CS中等待：将数据放入CS中即可。
- recvInterest：监听与name prefix对应的face，接收Interest。
- recvData：监听与name prefix对应的face，接收Data。

# 5. core
串联起四个模块，搭建input，output通道。
对于ndn中的核心`name`，相关的实现暂时放在core里。后续可能会考虑移植到内部库中。
### 5.1 文件说明
```c
// 从应用层到网络层的数据流处理
output.c
// 从网络层到应用层的数据流处理
input.c
// ndnLite模块初始化，整体数据流处理
core.c
```

# 6. utils
一些通用轮子。在别的项目中也可以复用。
### 6.1 文件说明
```c
// c语言实现的日志库
log.c

// 实现string与name之间的转换
name.cpp
// 实现前缀树数据结构
nameTrie.cpp
```
### 6.2 数据结构说明
#### 6.2.1 name
名字是NDN的核心。在许多模块中都会使用到name。
通常来说，只有在forwarder层，会对name去做更详细的处理，比如最长前缀匹配。此时会考虑到name中使用‘/’去分隔的语义。
在其它层次上，name作为朴素的字符串（string, char []）去处理、封装即可。（暂时）
以一个名称`/this/is/prefix/and/name/suffix`来作一下元素说明：
- name_elem_t：每个分隔线'/'之间的字符串，比如`this`,`is`,`prefix`, 都是一个name_elem_t。
- name_t：一个完整的name，由多个name_elem_t组成。也是对name_elem_t的封装，提供与string, char[] 类型之间的转换。
#### 6.2.2 nameTrie
存储name的数据结构，将name作为索引，借助前缀树的数据结构，实现快速的最长前缀匹配。
由forwarder中的组件使用。
- 内容存储 cs：提供 name - packet的存储。
- 待定兴趣表 pit：提供 name - input_face, output_face, expired_time之间的表项存储与增删改查等操作。
- 转发信息库 fib：提供 name - output_face, expired_time之间的表项存储与增删改查等操作。
