# 星火链网RFC-011：星火链网BID标识二进制解析协议规范

# 1.序言

编号：RFC-011

类型：标准

标题：星火链网BID标识二进制解析协议规范

作者： 金键，[jin.jian@caict.ac.cn](mailto:jin.jian@caict.ac.cn)；谢家贵，[xiejiagui@caict.ac.cn](mailto:xiejiagui@caict.ac.cn)；李志平，[lizhiping@caict.ac.cn](mailto:lizhiping@caict.ac.cn)；张波，[zhangbo3@caict.ac.cn](mailto:zhangbo3@caict.ac.cn)；郭世杰，[guoshijie@caict.ac.cn](mailto:guoshijie@caict.ac.cn)；徐景民，[jm.xu@yamu.com](mailto:jm.xu@yamu.com)；汲传鑫，[cx.ji@yamu.com](mailto:cx.ji@yamu.com)；李文军，[wj.li@yamu.com](mailto:wj.li@yamu.com)

发布时间：2022-12-29

状态：演示

更新时间：2022-12-29

讨论地址：https://github.com/caict-4iot-dev/bif-rfcs/issues/14

依赖RFC：[星火链网RFC-005：星火链网BID标识解析协议规范](星火链网RFC-005：星火链网BID标识解析协议规范.md)

# 2.摘要

本文介绍星火链网BID标识二进制解析和使用的规范定义。

# 3.动机

为了实现BID标识高性能解析，设计一种基于UDP协议的、星火链网链下的、兼顾物联网低功耗终端和互联网终端，协议灵活、可扩展的、二进制标识解析协议，本篇RFC是对该协议的具体阐述。

# 4.原理

## 4.1.架构

BID的架构参见[星火链网RFC-004：星火链网BID标识管理规范](星火链网RFC-004：星火链网BID标识管理规范.md)中的BID架构。
主链和子链链下支持BID高性能解析，支持与链上BID注册数据同步。
[![image](https://user-images.githubusercontent.com/119830277/205559112-2f838f7b-12b0-47e5-8556-25bb13acef44.png)](https://user-images.githubusercontent.com/119830277/205559112-2f838f7b-12b0-47e5-8556-25bb13acef44.png)

## 4.2.协议元数据

BID的协议元数据为BID文档。BID文档遵循DID Document规范，并在之基础上做了一定的扩展。
BID文档字段说明参见星火链网RFC-004：星火链网BID标识管理规范中的协议元数据。

## 4.3.BID协议解析

BID解析是指获取给定BID的BID文档的过程,所有主链上的超级节点和接入星火链网的子链均需支持BID解析。

### 4.3.1.主链解析架构

主链支持解析主链BID文档、子链解析服务地址两类数据。解析主链BID文档分为解析主链普通属性BID文档和解析凭证属性BID文档。解析子链解析服务地址又分为根据带AC号的BID解析子链的解析服务地址和根据AC号解析子链的解析服务地址。

1.解析主链普通属性BID文档
[![image](https://user-images.githubusercontent.com/119830277/205559487-caea4481-cd31-4bb0-8edd-c53e9ed766da.png)](https://user-images.githubusercontent.com/119830277/205559487-caea4481-cd31-4bb0-8edd-c53e9ed766da.png)
2.解析主链凭据属性BID文档
[![image](https://user-images.githubusercontent.com/119830277/205559685-cac08870-685c-4a6c-a26d-9b0b63847b0e.png)](https://user-images.githubusercontent.com/119830277/205559685-cac08870-685c-4a6c-a26d-9b0b63847b0e.png)
3.根据带AC号的BID解析子链解析服务地址
[![image](https://user-images.githubusercontent.com/119830277/205559781-64b46fda-a2f6-49d9-bf0e-adba4d79596f.png)](https://user-images.githubusercontent.com/119830277/205559781-64b46fda-a2f6-49d9-bf0e-adba4d79596f.png)

### 4.3.2.子链解析架构

[![image](https://user-images.githubusercontent.com/119830277/205559886-1fbbdf84-fb98-4eae-8f6c-0e44dfba0315.png)](https://user-images.githubusercontent.com/119830277/205559886-1fbbdf84-fb98-4eae-8f6c-0e44dfba0315.png)

# 5.协议规范

二进制解析协议传输使用UDP通信协议，使用二机制编码。

## 5.1.协议要素

BID协议遵循以下规范，以确保不同实现之间的互操作性。确保不同实现之间的互操作性。

### 5.1.1.传统要素

1. 网络传输遵循大端序。
2. 允许使用UDP/TCP/HTTP/HTTPS传输，TCP/UDP建议端口都是2259。
3. 使用UDP传输时，最大长度限制1024字节内，超过长度的需要被分割为多个数据包传输，每个分片数据包中包含对应的序列号。
4. 因网络或服务器性能影响，UDP数据包的重传时间间隔建议为2-5秒。
5. 使用TCP传输时，服务端应支持多个TCP连接。默认情况下，服务端应该在请求完成后，关闭TCP连接。
6. BID文档通过unit数据格式组合构建，每个unit包含ID、长度、unitPayload三部分，如下，其中unitPayload字段仍然可以是unit格式。

| ID    | Length | unitPayload |
| ----- | ------ | ----------- |
| 2字节 | 2字节  | unit数据    |

### 5.1.2.协议要素

BID协议的每一条传输数据包含三个部分：消息头、消息体、消息签名。

#### 5.1.2.1.消息头

消息头部分是必须存在的，消息头通过option字段，可扩展，最小16个字节。如下：

- version：4 bit，版本号。
- headerLength：4 bit，单位是4字节，消息头长度最长是60字节。
- flag：16 bit, 2个字节。
  - QR(Query Response)：1 bit，请求/应答标志位。置0表示查询请求；置1表示查询应答。
  - TC(TrunCation)：1 bit，消息体分片标志位。置0表示消息体没有分片；置1表示消息体有分片。
  - RD(Recursion Desired)：1 bit，递归查询标志位。客户端希望服务器对此次查询进行递归查询时将该位置1，否则置0。响应时RD位会复制到响应报文内。
  - DT(Do Trust)：1 bit，可信验证标志位。表示是否对结果进行可信解析认证，在请求中设置此位为1，表示需要对解析结果做认证，在回复中设置此位为1，表示没有对解析结果做可信验证。
  - TR(Trust Result)：1 bit，可信验证结果标志位。置0表示可信解析失败；置1表示可信解析成功。
  - CT(CerTified bit)：1 bit，消息签名标志位。CT被置位,表示要求服务器对其应答进行签名。CT被置位的应答表示消息已被签名。如果请求时CT被置位，服务器必须对应答数据签名。如果服务器不能在应答中提供有效的签名，客户端应该丢弃应答并视此请求失败。
  - opCode：4 bit，标记是解析查询、注册管理等。
  - 其他bit待后续扩展。
- unUsed：1个字节，待扩展。
- replyCode：2个字节, 标记应答结果类型。
- messageID：2个字节，分片数据包ID。
- TransactionID：4个字节，会话ID，用于请求和应答对应。
- messageLength：4个字节，消息数据长度，包含消息体和消息签名。
- option：
  - optionType (1个字节) + optionPayload

[![image](https://user-images.githubusercontent.com/119830277/205564868-338c2ea7-3186-469a-9423-3714e9e755a2.png)](https://user-images.githubusercontent.com/119830277/205564868-338c2ea7-3186-469a-9423-3714e9e755a2.png)

##### 5.1.2.1.1.Opcode定义

| opCode | 名称          | 说明                                       |
| ------ | ------------- | ------------------------------------------ |
| 0      | QUERY         | 标准查询。通过BID查询BID文档或其相关属性   |
| 1      | REVERSE QUERY | 反向查询。通过某些BID属性，查询属于哪个BID |
| 2      | BID UPDATE    | BID管理。BID文档的添加、删除等操作         |

##### 5.1.2.1.2.Rcode定义

| 返回码                          | 返回码说明                                 |
| ------------------------------- | ------------------------------------------ |
| 1(success)                      | 对一次成功的解析                           |
| 2(error)                        | 发生错误                                   |
| 3(server too busy)              | 服务器忙                                   |
| 4(protocol error)               | 协议错误                                   |
| 5(operation not supported)      | 不支持的操作                               |
| 6(not found)                    | 没有找到标识                               |
| 7(server not response)          | 服务器没有响应                             |
| 8(field not found)              | 不存在的字段                               |
| 9(verify failed)                | 签名验证失败                               |
| 10(nonexistent chain code info) | 不存在AC号                                 |
| 11(authentication required)     | 需要对客户端做认证，客户端重新发起认证查询 |
| 255                             | Rcode值在扩展Option字段中                  |

##### 5.1.2.1.3.Option定义

option数据有两部分组成：optionType (1个字节) + optionPayload。其中，不同的optionType对应不同的定长的optionPayload数据。长度不足4个倍数的部分，填充0补足。

#### 5.1.2.2消息体

消息体部分包含了每一个协议操作的特定数据。它的格式根据消息头部中操作码和应答码的不同而变化。消息体有可能是空的。消息体的内容受消息签名数据的保护。
消息体总是跟在消息头部之后。消息体的字节长度可以通过消息头部中的来确定。定义如下：

1.消息体定义:

- 消息体有N多个unit组成
- unit可以任意拆分、组合

2.unit定义：

- ID: 2个字节
- length: 2个字节
- payload: payload仍然可以是unit

3.每种type类型，都有自己的payload格式定义
[![image](https://user-images.githubusercontent.com/119830277/205571671-6962129c-6062-4b13-a890-9c0d1497536b.png)](https://user-images.githubusercontent.com/119830277/205571671-6962129c-6062-4b13-a890-9c0d1497536b.png)
unitPayload部分如果包含unit数组，则，这些unit串行拼接在unitPayload数据区中。例如，消息体包含4个unit，分别是unit 100和3个unit 200，则unitPayload数据如下：
[![image](https://user-images.githubusercontent.com/119830277/205572292-a51f35c0-75d8-479a-b4e1-854f0705c512.png)](https://user-images.githubusercontent.com/119830277/205572292-a51f35c0-75d8-479a-b4e1-854f0705c512.png)
unitID范围定义如下：

| unitID范围    | 说明                     | 备注 |
| ------------- | ------------------------ | ---- |
| 0x0-0xFF      | 预留范围                 |      |
| 0x1000-0x1FFF | URP1.0协议定义范围       |      |
| 0x2000-0x9FFF | URP2.0协议定义范围       |      |
| 0xA000-0xCFFF | URP2.0协议自定义扩展范围 |      |
| 0xD000-0xFFFF | URP2.0预留范围           |      |

#### 5.1.2.3.消息签名

消息签名为客户端和服务器之间交换的任何消息提供传输安全机制。一个非空的消息凭据可能包含了数字签名（来自消息的最初生成者）或者基于提前建立的会话密钥生成的单向消息认证码（MAC）。
消息签名可用于客户端和服务器之间的消息认证。它还可以用于检验传输完成的数据的完整性。
消息体签名是一个特殊的Unit,包含三部分：

- creator：签名的BID公钥ID，也是一个unit
- digestAlgorithm：签名的摘要算法，也是一个unit
- signatureValue:签名数据，也是一个unit

### 5.1.3.消息传输

超过1024字节的BID协议数据包，在以TCP/UDP传输过程中，可能会被分片，分成多个数据包传输。消息接收者需要将这些分片的数据包重组后，再处理。分片数据包只能针对消息头之外的部分。
分片的数据包的消息头的TC标志位置1。第一个分片数据包的messageID是1，后续其他的分片数据包依次加1。

## 5.2.协议操作

本节定义BID协议客户端和服务端的之间的信息交换，以及不同opcode和rcode对应的消息体格式。

### 5.2.1.BID解析

标准查询请求必须设置其消息头部的为QUERY，为0。

#### 5.2.1.1.消息解析

##### 5.2.1.1.1.BID文档查询请求

BID查询请求的消息体是一个unit，定义如下：

| Unit类型          | UnitID | Payload | 说明 |
| ----------------- | ------ | ------- | ---- |
| didDocument.query | 1      | unit    |      |

Payload定义如下：

| Payload字段    | 类型        | 说明                               |
| -------------- | ----------- | ---------------------------------- |
| UnitID         | Short,2字节 | BID字段的unit ID，0表示查询BID文档 |
| didDocument.id | String      | bid                                |
| FieldID        | String      | 指定查询的字段，可选参数           |

##### 5.2.1.1.2.BID文档应答

返回普通BID文档

| unit名称                                          | unitID | 类型   | 说明                                                |
| ------------------------------------------------- | ------ | ------ | --------------------------------------------------- |
| bidDocument                                       | 0x2100 | unit   | BID文档                                             |
| bidDocument.version                               | 0x2101 | string | BID文档的版本                                       |
| bidDocument.id                                    | 0x2102 | string | 解析的BID                                           |
| bidDocument.publicKey                             | 0x2110 | unit   | 公钥                                                |
| bidDocument.publicKey.id                          | 0x2111 | string | 公钥id                                              |
| bidDocument.publicKey.type                        | 0x2112 | string | 公钥算法类型                                        |
| bidDocument.publicKey.controller                  | 0x2113 | string | 一个BID表明此公钥的归属                             |
| bidDocument.publicKey.publicKeyHex                | 0x2114 | string | 十六进制公钥                                        |
| bidDocument.authentication                        | 0x2100 | unit   | 一组公钥id                                          |
| bidDocument.alsoKnownAs                           | 0x2120 | unit   | 关联id                                              |
| bidDocument.alsoKnownAs.type                      | 0x2121 | Int    | 关联id的类型                                        |
| bidDocument.alsoKnownAs.id                        | 0x2122 | string | 关联id                                              |
| bidDocument.extension                             | 0x3000 | unit   | 扩展字段                                            |
| bidDocument.extension.recovery                    | 0x3100 | unit   | 一组公钥id                                          |
| bidDocument.extension.ttl                         | 0x3200 | Long   | 缓存时间，单位秒                                    |
| bidDocument.extension.delegateSign                | 0x3300 | unit   | 第三方对publicKey的签名，可信解析使用               |
| bidDocument.extension.delegateSign.signer         | 0x3310 | string | 签名公钥id                                          |
| bidDocument.extension.delegateSign.signatureValue | 0x3320 | string | 签名的base64编码                                    |
| bidDocument.extension.type                        | 0x3400 | Int    | 见附录属性类型                                      |
| bidDocument.extension.attributes                  | 0x3500 | unit   | 一组属性,属性结构                                   |
| bidDocument.extension.acsns                       | 0x3600 | unit   | AC号列表                                            |
| bidDocument.extension.acsns.acsn                  | 0x3610 | string | AC号                                                |
| bidDocument.extension.acsns.acsnAddress           | 0x3620 | string | AC号对应的子链的解析地址BID                         |
| bidDocument.extension.verifiableCredentials       | 0x3700 | unit   | 凭证列表，只有主链非凭证类型的BID文档才可能有本字段 |
| bidDocument.extension.verifiableCredentials.id    | 0x3710 | string | 凭证ID                                              |
| bidDocument.extension.verifiableCredentials.type  | 0x3720 | Int    | 凭证类型                                            |
| bidDocument.service                               | 0x2130 | unit   | 一组服务地址                                        |
| bidDocument.service.id                            | 0x2131 | string | 服务地址的ID                                        |
| bidDocument.service.type                          | 0x2132 | string | 字符串，代表服务的类型                              |
| bidDocument.service.serviceEndpoint               | 0x2136 | string | 服务的URL地址                                       |
| bidDocument.created                               | 0x2104 | string | 创建时间                                            |
| bidDocument.updated                               | 0x2105 | string | 上次的更新时间                                      |
| bidDocument.proof                                 | 0x2140 | unit   | 签名信息                                            |
| bidDocument.proof.creator                         | 0x2141 | string | 签名公钥id                                          |
| bidDocument.proof.signatureValue                  | 0x2142 | string | 签名的base64编                                      |

当server.type为子链解析服务时，service结构如下：

| unit 名称                           | unitID | 类型   | 说明                      |
| ----------------------------------- | ------ | ------ | ------------------------- |
| bidDocument.service.id              | 0x2131 | string | 服务地址的ID              |
| bidDocument.service.type            | 0x2132 | string | 字符串，代表服务的类型    |
| bidDocument.service.version         | 0x2133 | string | 解析服务支持的BID协议版本 |
| bidDocument.service.protocol        | 0x2134 | Int    | 解析服务支持的传输协议    |
| bidDocument.service.serverType      | 0x2135 | Int    | 解析地址类型              |
| bidDocument.service.serviceEndpoint | 0x2136 | string | 解析地址                  |
| bidDocument.service.port            | 0x2137 | Int    | 解析端口                  |

#### 5.2.1.2.BID公钥字段解析

##### 5.2.1.2.1.BID公钥字段查询请求

参考[5.2.1.1.1章节](#52111bid文档查询请求)，unitPayload中的unitID为0x2200。

##### 5.2.1.2.2.BID公钥字段应答

| unit 名称                          | unitID | 类型   | 说明                    |
| ---------------------------------- | ------ | ------ | ----------------------- |
| bidDocumentField                   | 0x2200 | unit   | 解析结果                |
| bidDocument.version                | 0x2101 | string | BID文档的版本           |
| bidDocument.id                     | 0x2102 | string | 解析的BID               |
| bidDocument.publicKey              | 0x2110 | unit   | 公钥                    |
| bidDocument.publicKey.id           | 0x2111 | string | 公钥id                  |
| bidDocument.publicKey.type         | 0x2112 | string | 公钥算法类型            |
| bidDocument.publicKey.controller   | 0x2113 | string | 一个BID表明此公钥的归属 |
| bidDocument.publicKey.publicKeyHex | 0x2114 | string | 十六进制公钥            |

##### 5.2.1.2.3.BID指定公钥字段解析

参考[5.2.1.1.1章节](#52111bid文档查询请求)，unitPayload中的unitID为0x2200，fieldID是请求公钥字段id。

##### 5.2.1.2.4.BID指定公钥字段应答

参考[5.2.1.2.2章节](#52122bid公钥字段应答)。

#### 5.2.1.3.BID属性字段解析

##### 5.2.1.3.1.BID属性字段查询请求

参考[5.2.1.1.1章节](#52111bid文档查询请求)，unitPayload中的unitID为0x2700。

##### 5.2.1.3.2.BID属性字段查询应答

| Unit 类型                                                  | Unit ID | 类型   | 说明                                  |
| ---------------------------------------------------------- | ------- | ------ | ------------------------------------- |
| data.didDocument                                           | 0x2200  | unit   | 解析结果                              |
| data.didDocument.version                                   | 0x2101  | String | BID文档的版本                         |
| data.didDocument.id                                        | 0x2102  | String | 解析的BID                             |
| data.didDocument.extension.attributes.key                  | 0x3510  | String | 属性的Key                             |
| data.didDocument.extension.attributes.desc                 | 0x3520  | String | 属性的描述                            |
| data.didDocument.extension.attributes.encrypt              | 0x3530  | int    | 是否加密，0非加密，1加密              |
| data.didDocument.extension.attributes.format               | 0x3540  | String | Image、text、video、mixture等数据类型 |
| data.didDocument.extension.attributes.value                | 0x3550  | String | 属性自定义value                       |
| data.didDocument.extension.attributes.proof                | 0x3560  | unit   | 签名证明                              |
| data.didDocument.extension.attributes.proof.creator        | 0x3561  | String | proof 的创建者，这里是一个公钥的 id   |
| data.didDocument.extension.attributes.proof.signatureValue | 0x3562  | String | 使用相应私钥对凭证内容的签名          |

#### 5.2.1.4.BID AC号字段解析

##### 5.2.1.4.1.BID AC号字段查询请求

参考[5.2.1.1.1章节](#52111bid文档查询请求)，unitPayload中的unitID为0x2800。

##### 5.2.1.4.2.BID AC号字段查询应答

| unit 名称                               | unitID | 类型   | 说明                        |
| --------------------------------------- | ------ | ------ | --------------------------- |
| bidDocumentField                        | 0x2100 | unit   | 解析结果                    |
| bidDocument.version                     | 0x2101 | string | BID文档的版本               |
| bidDocument.id                          | 0x2102 | string | 解析的BID                   |
| bidDocument.extension.acsns             | 0x3600 | unit   | AC号列表                    |
| bidDocument.extension.acsns.acsn        | 0x3601 | string | AC号                        |
| bidDocument.extension.acsns.acsnAddress | 0x3602 | string | AC号对应的子链的解析地址BID |

##### 5.2.1.4.3.BID指定AC号查询请求

参考[5.2.1.1.1章节](#52111bid文档查询请求)，unitPayload中的unitID为0x2800。fieldID是指定的ac码。

##### 5.2.1.4.4.BID指定AC号查询应答

参考[5.2.1.4.2章节](#52142bid-ac号字段查询应答)。

#### 5.2.1.5BID凭证列表字段解析

##### 5.2.1.5.1.BID凭证列表字段查询请求

参考[5.2.1.1.1章节](#52111bid文档查询请求)，unitPayload中的unitID为0x2900。

##### 5.2.1.5.2.BID凭证列表字段查询应答

| unit 名称                                        | unitID | 类型   | 说明                                                |
| ------------------------------------------------ | ------ | ------ | --------------------------------------------------- |
| bidDocumentField                                 | 0x2100 | unit   | 解析结果                                            |
| bidDocument.version                              | 0x2101 | string | BID文档的版本                                       |
| bidDocument.id                                   | 0x2102 | string | 解析的BID                                           |
| bidDocument.extension.verifiableCredentials      | 0x3700 | unit   | 凭证列表，只有主链非凭证类型的BID文档才可能有本字段 |
| bidDocument.extension.verifiableCredentials.id   | 0x3710 | string | 凭证ID                                              |
| bidDocument.extension.verifiableCredentials.type | 0x3720 | Int    | 凭证类型                                            |

#### 5.2.1.6.BID服务地址字段解析

##### 5.2.1.6.1.BID服务地址字段查询请求

参考[5.2.1.1.1章节](#52111bid文档查询请求)， unitPayload中的unitID为0x3000。

##### 5.2.1.6.2.BID服务地址字段查询应答

| unit 名称                           | unitID | 类型   | 说明                      |
| ----------------------------------- | ------ | ------ | ------------------------- |
| bidDocumentField                    | 0x2100 | unit   | 解析结果                  |
| bidDocument.version                 | 0x2101 | string | BID文档的版本             |
| bidDocument.id                      | 0x2102 | string | 解析的BID                 |
| bidDocument.service.id              | 0x2131 | string | 服务地址的ID              |
| bidDocument.service.type            | 0x2132 | string | 字符串，代表服务的类型    |
| bidDocument.service.version         | 0x2133 | string | 解析服务支持的BID协议版本 |
| bidDocument.service.protocol        | 0x2134 | Int    | 解析服务支持的传输协议    |
| bidDocument.service.serverType      | 0x2135 | Int    | 解析地址类型              |
| bidDocument.service.serviceEndpoint | 0x2136 | string | 解析地址                  |
| bidDocument.service.port            | 0x2137 | Int    | 解析端口                  |

##### 5.2.1.6.3.BID指定服务地址字段解析

参考[5.2.1.1.1章节](#52111bid文档查询请求)，unitPayload中的unitID为0x3000, fieldID是要请求service.id。

##### 5.2.1.6.4.BID指定服务地址字段查询应答

参考[5.2.1.6.2章节](#52162bid服务地址字段查询应答)。

### 5.2.2.服务端的失败响应

unitid是errResponse 0x2010 ，unitPayload部分包含两个unit，如下：

| unit 名称      | unitID | unitPayload | 说明      |
| -------------- | ------ | ----------- | --------- |
| didDocument.id | 0x2102 | string      | 解析的BID |
| Result.msg     | 0x2011 | string      | 失败原因  |

### 5.2.3.客户端认证

客户端在访问某些特殊BID解析服务器的时候，BID解析服务器需要验证客户端的身份。
BID协议的客户端认证，通过在传输层和BID协议层之间增加TLS层，即通过TLS协议实现客户段的认证，具体可参考rfc2246、rfc4346、rfc5246、rfc8446。

## 5.3.协议安全与扩展

### 5.3.1.变长消息头

BID协议需要兼顾物联网低功耗终端和互联网终端，协议设计需要灵活、可扩展。消息头的格式采用变成设计。

- 定长：消息解码简单，可扩展性差。为考虑后续扩展，消息头的定义会比较长，在物联网低功耗终端或受限的网络中传输，可能存在问题。
- 变长：扩展性好，但是解码复杂。

### 5.3.2.传输安全

为保证传输过程中的数据安全，通过在TCP/UDP传输协议层与BID协议层之间增加TLS层，即，通过TLS加密的方式，保证数据传输的安全性。

### 5.3.3.用户安全

BID协议的客户端认证，通过在传输层和BID协议层之间增加TLS层，即通过TLS协议实现客户端的认证。

### 5.3.4.服务安全

BID服务端通过对客户端认证，只对被允许的客户端提供服务，保证服务端的安全性。通过在传输层和BID协议层之间增加TLS层，即通过TLS协议实现客户端的认证。

### 5.3.5.可信解析

可信解析主要是对BID文档的公钥和签名内容进行可信验证，遵循DPKI规范。
DPKI系统中，每个BID都可以自己对自己进行身份认证，不再依赖于CA中心，每个BID基于区块链的共识协议建立和维护他人的信任。但在实际的区块链系统中，每个设备在同步这些共识数据的时候，需要大带宽和大量存储空间，这在资源受限制且计算能力有限的物联网设备中，是不可接受的。可以通过在星火链网内建立多个为普通BID 消息publickKey签名认证的认证BID（即，权威机构的BID，可以作为CA中心）,保证数据来源的可靠性，确保递归解析过程中每个经过的解析服务都是可信的，被这些BID认证的其它BID，就认为是可信的。

#### 5.3.5.1.可信验证

可信认证行为包含两部分，如下：
1.对BID的publicKey的签名认证

- extension：BID扩展字段。
  - delegateSign: 选填字段。第三方对publicKey的签名，可信解析使用。包括：signer、digestAlgorithm和signatureValue。
    - signer ：签名者，这里是一个公钥的id。
    - digestAlgorithm：签名里的摘要算法,支持SHA-256和SM3两种。
    - signatureValue: 使用相应私钥对publicKey字段的签名。

2.对BID文档内容的签名认证

- proof：选填字段。消息所有者对消息内容的签名，包括：creator、digestAlgorithm和signatureValue。
  - creator：proof的创建者，这里是一个公钥的id。
  - digestAlgorithm：签名里的摘要算法,支持SHA-256和SM3两种。
  - signatureValue：使用相应私钥对除proof字段的整个BID文档签名。
    BID解析器需要在本地保存认证BID列表中所有BID的公钥。在可信解析链的验证过程中，如果BID是在认证BID列表中，则使用该BID的本地公钥验签该BID文档，完成可信解析流程。

#### 5.3.5.2.可信声明验证

BID文档中的凭据字段是可验证性声明，权威机构用自己的BID给用户的BID文档中“attributes”字段中的某些属性做签名，给这些属性做背书，表示这些属性是权威的。
在BID凭据字段验证过程中，需要考虑时间是否失效，是否被吊销。
[![image](https://user-images.githubusercontent.com/119830277/205588524-8fc0f888-f5ca-481e-8468-6cc21935a118.png)](https://user-images.githubusercontent.com/119830277/205588524-8fc0f888-f5ca-481e-8468-6cc21935a118.png)

#### 5.3.5.3.解析服务信任验证

查询BID公钥被其他BID直接签名，该BID文档的所有者是其他BID。验证流程如下：
[![image](https://user-images.githubusercontent.com/119830277/205588706-3fb6dea6-860a-40c0-8be1-b0979668af04.png)](https://user-images.githubusercontent.com/119830277/205588706-3fb6dea6-860a-40c0-8be1-b0979668af04.png)

# 6.实现

BID可广泛应用于工业制造、物流以及产品销售环节，基于制造业BID应用的特殊性，除了安全性要求外，对于BID注册、解析性能有一定要求。通过BID标识二进制解析协议的应用，可适应物联网、工业制造业BID应用需求。
[![image](https://user-images.githubusercontent.com/119830277/205588817-71e76068-ec76-4544-80f4-f41f49829e5d.png)](https://user-images.githubusercontent.com/119830277/205588817-71e76068-ec76-4544-80f4-f41f49829e5d.png)
1.供应链运输产品配件至产品制造工位，通过解析包装BID解析出配件BID以及配件技术参数等，用于新产品制造；
2.制造工位生产新产品，通过BID注册服务注册新产品BID，包含新产品使用的配件信息，产品参数等；
3.仓储环节通过BID注册服务注册运输包装BID，包装内包含产品BID；
4.销售环节通过BID注册服务注册订单BID，订单包含包装BID信息；

# 7.缺陷

暂无
