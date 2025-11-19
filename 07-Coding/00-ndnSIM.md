参考陈仁江的实验，修改路径如下

ns-3/src/ndnSIM
	/examples
		/lab.cpp 实验配置文件
	/ndn-cxx/ndn-cxx
		/Encoding/tlv.cpp 增加自定义字段
		interest.hpp 序列化逻辑修改
		interest.cpp
	/NFD/daemon
		/face
			face.cpp 获取邻居信息
			face.hpp
		/table
			pit-entry.cpp+hpp PIT表自定义字段
		/fw
			strategy.cpp+hpp 转发策略
			forwarder.cpp+hpp 转发器

参考网络教程