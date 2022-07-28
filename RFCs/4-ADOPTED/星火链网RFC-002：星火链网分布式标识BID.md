# 星火链网RFC-002：星火链网分布式标识BID

# 1. 序言

编号：RFC-002

类型：标准

标题：星火链网分布式标识BID

作者：金健，[jinjian1@caict.ac.cn](mailto:jinjian1@caict.ac.cn)；谢家贵，[xiejiagui@caict.ac.cn](mailto:xiejiagui@caict.ac.cn)；李志平，[lizhiping@caict.ac.cn](mailto:lizhiping@caict.ac.cn)；马若龙，[maruolong@caict.ac.cn](mailto:maruolong@caict.ac.cn)；张波，[zhangbo3@caict.ac.cn](mailto:zhangbo3@caict.ac.cn)

发布时间：2021-12-07

状态：采纳

更新时间：2021-12-10

讨论地址：https://github.com/caict-4iot-dev/BIF-RFCs/issues/2

依赖RFC：无

# 2. 摘要

本文介绍星火链网分布式标识，简称星火标识BID(Blockchain-based Identifier, BID)。

# 3. 动机

星火链网需要一套属于自己的分布式标识符，是基于分布式标识符DID规范的、适用于星火·链网分层架构的、可以实现寻址功能和数字身份功能的、工业互联网和物联网场景下的通用性标识。

# 4. 原理

星火标识BID(Blockchain-based Identifier, BID)，BID标识是基于W3C DID标准开发的新型分布式标识，任意实体可自主生成星火标识，不需要中心化注册机构就可以实现全球唯一性，具有**分布式、自主管理、隐私保护和安全易用**等特点，同时根据算法的不同，BID支持39-57位**变长编码**方式，有效适应各种业务场景，兼容各类设备。

**BID**主要包含以下方面：**BID标识符**、**BID文档**、**BID解析协议**、**基于BID的数字凭证**：

- **BID标识符：**
  - 是基于新型分布式标识符DID规范的一种方法，任意实体可自主生成BID，不需要中心化注册机构就可以实现全球唯一性；
  - **去中心化、互通**

- **BID文档：** 
  -  BID标识符只是表示一个身份的标识符，不包含身份的信息。而BID文档就是用于描述身份详细信息的文档，一个BID标识符关联到一个BID文档。BID标识符具体解析为BID文档，BID文档内包括BID标识符和公钥详细信息(持有者、加密算法、密钥状态等)，以及BID持有者的其他非隐私属性描述等；
  - **信息承载**

- **BID解析协议：**
  -  星火·标识深耕物联网领域，自主构建了BID解析协议，优化了海量物联网设备的接入场景，同时支持DID解析器，为星火·链网智能设备自主交互奠定了基础；
  - **互通**

- **基于BID的数字凭证：**
  - 提供了一种规范来描述实体所具有的某些属性。BID持有者，可以通过可验证声明，向其他实体证明自己的某些属性是可信的。结合数字签名和零知识证明等密码学技术，可以使得声明更加安全可信，并进一步保障用户隐私不被侵犯；
  - **隐私**

![image](https://user-images.githubusercontent.com/90955034/145747023-aa985f24-2ef2-4e08-b7ea-c9fa898dc6ec.png)

# 5. 规范

<span id="bid">星火标识编码规范如下：</span>

> 星火标识编码规范

![image-20211206163837375](https://user-images.githubusercontent.com/76681420/144978971-1e240d08-2569-4777-8c94-21c2681766d7.png)

由前缀Prefix、共识码（AC号）、加密类型、编码类型、后缀组成。

# 6. 实现

**星火·标识BID相关规范和实现包括以下内容：**

- [BID生成规则和地址容量标准](./星火链网RFC-003：星火链网BID生成规则和地址容量标准.md)
- [BID注册管理](./星火链网RFC-004：星火链网BID标识管理规范.md)
- [BID协议解析](./星火链网RFC-005：星火链网BID标识解析协议规范.md)
- [基于BID的BIDComm通信协议](./星火链网RFC-006：星火链网BIDComm协议标准.md)
- [基于BID的BIDAuth协议](./星火链网RFC-007：星火链网BIDAuth协议标准.md)
- [基于BID的数字凭证](./星火链网RFC-008：星火链网BID可信证书协议标准.md)

# 7. 缺陷

暂无。

 