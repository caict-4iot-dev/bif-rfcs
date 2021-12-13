# 星火链网RFC-008：星火链网BIDComm协议标准

# 1. 序言

编号：RFC-008

类型：RFC

标题：星火链网BIDComm协议标准

作者：马若龙，maruolong@caict.ac.cn

发布时间：2021-11-24

状态：通过

更新时间：2021-12-10

讨论地址：https://github.com/CAICT-DEV/BIF-RFCs/issues/8

依赖RFC：[星火链网RFC-002：星火链网BID](星火链网RFC-002：星火链网BID.md)

# 2. 摘要

本文介绍星火链网BIDComm的协议标准和规范流程。

# 3. 动机

在基于星火链网标识符BID的交互，需要一套标准的通信协议，描述通信双方如何以BID为数据载体，进行数据交互和信息通信。同时，基于该标准协议，BID的基本认证流程（BIDAuth）、可验证证书（BID-VC）等交互流程的规范流程同样需要定义。因此，本RFC定义BIDComm协议标准和交互流程。

# 4. 原理

星火标识BID通信协议（Blockchain-based Identifier Communication, 简称BIDComm），是星火·链网的数据通信协议，是支持星火标识BID的通信载体，也是基于星火·链网的分布式数字身份通信的协议标准。

本节通过BIDComm设计目标、设计架构、消息结构等部分详细介绍BIDComm的原理。

## 4.1. 总体设计目标

BIDComm的主要目标是设计一种基于星火标识BID的、基于DIDComm设计规范的、遵循W3C规范的分布式标识符通信协议标准，可以实现分布式数据身份、工业互联网和物联网场景下的通用传输协议。通过BIDComm协议，可实现通信连接、关系维护、颁发凭证、提供证明等通信交互能力。

分布式数字身份使用四层模型，模型架构如图4-1所示。

>  图 4-1 分布式数字身份四层模型

![image](https://user-images.githubusercontent.com/90955034/145359487-4da6286d-94bb-4cfa-bb09-1ed52deb8bf6.png)

其中下面两层主要用于实现技术信任，上面两层用于实现人的信任。

在第二层通信交互中，依赖于BIDComm协议，两方之间可以建立安全可信通信，在通信中实现数据加密、防篡改等功能。

得益于BIDComm协议的规范，第三层信任关系中，BIDAuth、数字证书可信交换、可验证声明等数据信任得到有效保证。

BIDComm的设计目标为：

- 安全性

- 隐私保护

- 可互操作

- 以传输方式无关

- 可扩展性

通信双方的数据信息具有隐私性和不可篡改性，协议提供多种加密形式保证通信安全；

由于协议的可互操作性，协议可跨编程语言、区块链、供应商、OS、网络、加密等，跨地域和时间工作；

本协议可由应用开发人员在协议标准规范指导下，进行字段扩展，以实现应用定制化功能，通过开发人员对协议的扩展设计，可使协议应用在不同场景；

BIDComm使用JSON编码，兼容部分JSON-LD。

## 4.2. 总体结构

### 4.2.1.  BIDComm架构

BIDComm的架构如图4-2所示。

BIDComm协议有两层结构组成，信封层和消息层。

- 底层为消息层，定义了协议中的标准字段，包括基础协议和应用数据，基础协议为固定字段，应用数据中的字段为应用场景的私有协议；

- 顶层为信封层，负责将消息层的数据进行加密打包。打包后的加密数据将在互联网上传输。

> 图4-2 BIDComm架构图

![image](https://user-images.githubusercontent.com/90955034/145359536-280da3a6-18b8-4a39-a09f-21540c5cdcf0.png)

### 4.2.2.  BIDComm消息结构

基于信封层、消息层的架构设计，BIDComm的消息结构使用嵌套结构模式。结构模式如图4-3所示。

> <span id="jump4-3">图4-3 BIDComm消息结构</span>

![image](https://user-images.githubusercontent.com/90955034/145359595-2ad4ea9c-c1ad-4600-9c44-6dfb2df6bc9f.png)

BIDComm格式类型分为三种：消息明文、签名信封、加密信封。

- 消息明文，为BIDComm中描述通信数据、应用数据的基础数据；

- 签名信封，对消息明文经加密算法签名后的数据；

- 加密信封，对消息明文或签名信封经加密算法加密后的数据。

当发送方以此嵌套模式将消息发送后，接收方依次进行解密、验签、解析原文，获取消息内容。

每层消息中均有typ属性，标明该层消息的类别，类型描述见表4-1。

> 表 4-1 消息类型描述

| 类型     | 字段                   |
| -------- | ---------------------- |
| 加密信封 | bidcomm-encrypted+json |
| 签名信封 | bidcomm-signed+json    |
| 消息明文 | bidcomm-plaintext+json |

#### 4.2.2.1.   消息明文结构

BIDComm协议基于DIDComm规范，设计为支持BID的使用，并在其基础上进行改进和扩展。BIDComm消息明文基础字段如下：

- @type

- @id

- decorator

- content

其中，除content外的字段称为消息头，为协议的标准数据结构；content字段为消息主体，定义应用场景中所需的字段和数据结构。

消息明文消息字段规范见[消息明文规范](#jump5-3)。

#### 4.2.2.2.   信封结构

##### <span id="jump4-1">4.2.2.2.1. 签名信封结构</span>

BIDComm签名参考JWS（Json Web Signature）规范[https://datatracker.ietf.org/doc/html/rfc7515#appendix-F](https://datatracker.ietf.org/doc/html/rfc7515#appendix-F)，设计BIDComm签名信封规范。签名信封结构如图4-4所示。

> 图4-4 签名信封结构图

![image](https://user-images.githubusercontent.com/90955034/145359662-3af30801-96e5-45d5-a704-c22bf0e6e7f3.png)

为了更好的支持多重签名，在以上基础上进行调整，形成的BIDComm多重签名信封结构图如图4-5所示。

> 图 4-5 多重签名信封结构

![image](https://user-images.githubusercontent.com/90955034/145359692-f571f51d-f2f7-452e-bd0b-aa5866fb1845.png)

- 多重签名：签名信封结构支持多重签名，在多重签名列表中标注签名信息。

- 签名数据的构成元素：签名信封消息头和签名值；
  - 签名信封消息头：
    - 本签名的签名算法；
    - 本签名的签名公钥；

  - 签名值：使用签名公钥和签名算法对签名原文签名的值；

- 一般地，BID文档中仅有一个公钥，签名公钥与BID文档中公钥一致。若BID文档中存在多个公钥，可通过签名公钥属性指定使用的公钥；

- BIDComm的签名信封统一使用多重签名信封结构，若仅有单一签名，则在多重签名中只填充一组签名字段。

签名信封的具体流程和详细字段规范见[签名信封规范](#jump5-1)。

##### <span id="jump4-2">4.2.2.2.2. 加密信封结构</span>

BIDComm加密参考JWE(JSON Web Encryption)规范[https://datatracker.ietf.org/doc/html/rfc7516](https://datatracker.ietf.org/doc/html/rfc7516)，设计BIDComm加密信封规范。

加密信封结构如图4-6所示。

在BIDComm加密规范中，对称加密推荐使用SM4，非对称加密推荐使用SM2。

SM2规范：[https://datatracker.ietf.org/doc/html/draft-shen-sm2-ecdsa-02](https://datatracker.ietf.org/doc/html/draft-shen-sm2-ecdsa-02)

SM4规范：[https://datatracker.ietf.org/doc/html/draft-crypto-sm4-00](https://datatracker.ietf.org/doc/html/draft-crypto-sm4-00)

> 图 4-6 加密信封结构图

![image](https://user-images.githubusercontent.com/90955034/145359726-c74973b0-067f-4a2c-bdae-b1a1844e6101.png)

- 加密信封消息头：描述消息加密使用的非对称加密算法和对称加密算法；

- 收件人列表：加密信封结构支持多个收件人，在收件人列表中标注收件人信息。

- 收件人详细信息：
  - 对称加密密钥的非对称加密密文：

​				通过随机向量生成对称加密密钥；

​				使用加密密钥对消息明文进行对称加密；

​				使用收件人公钥对对称加密密钥进行非对称加密；

​				生成的密文即对称加密密钥的非对称加密密文；

- 收件人公钥：进行非对称加密所使用的收件人公钥；

- 随机向量：生成对称加密密钥的随机向量；

- 对称加密密文：消息明文使用对称加密密钥加密的密文

加密信封的具体流程和详细字段规范见[加密信封规范](#jump5-2)。

# 5. 规范

根据BIDComm消息结构中[图4-3](#jump4-3)的设计结构层级，按照签名信封、加密信封和消息明文制定BIDComm的规范。

## 5.1.  <span id="jump5-1">签名信封规范</span>

### 5.1.1.  签名信封流程规范

BIDComm签名流程如图5-1所示。

> 图5-1 BIDComm签名流程

![image](https://user-images.githubusercontent.com/90955034/145359770-38ca8658-ebbb-4aea-bf05-bd7b3d70ba92.png)

流程图描述：

1. 请求方根据业务需求生成消息明文；

2. 生成签名信封消息头，定义签名算法以及本方签名公钥；

3. 使用非对称加密私钥对消息明文进行签名；

4. 若是有多个签名，重复2-3步骤；

5. 组织生成签名信封数据结构，加密信封封装后发送或直接发送；

6. 接收方根据发送方的BID，从星火链上解析出该BID对应的BID文档；

7. 从BID文档中取出发送方的公钥，与签名信封中的公钥进行对比；

8. 使用发送方公钥验证签名。

### 5.1.2.  签名信封字段规范

根据[签名信封结构](#jump4-1)设计的签名信封结构，设计以下字段，对应相应的结构层级，给出编码方式。

| 字段名     | 子字段 | 子字段 | 结构层级       | 描述                 | 编码方式   |
| ---------- | ------ | ------ | -------------- | -------------------- | ---------- |
| signatures |        |        | 多重签名       | 多重签名数组结构     | JSON数组   |
| signatures | header |        | 签名信封消息头 | 消息头结构           | JSON元素   |
| signatures | header | alg    | 签名算法       | 签名使用的算法       | 字符串     |
| signatures | header | kid    | 签名公钥       | 签名使用的公钥       | 字符串     |
| signature  |        |        | 签名值         | 对消息明文的签名     | 签名密文   |
| payload    |        |        | 签名原文       | 消息明文编码后的数据 | Base64编码 |

BIDComm签名信封示例：

```json
{
	"typ": "bidcomm-signed+json",
	"signatures":[
	{
		"header":{
		"alg": "SM2",
		"kid": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-1",
	}
	"signature":"FW33NnvOHV0Te…WNMgP2EVCQ",
	}],
	"payload": "eyJpZCI6IjEyMzQ1Njc4O…aXRzIHZhbHVlIn19"
}
```

## 5.2.  <span id="jump5-2">加密信封规范</span>

### 5.2.1.  加密信封加密流程

BIDComm加密流程如图5-2所示。

> 图 5-2 BIDComm加密流程

![image](https://user-images.githubusercontent.com/90955034/145359816-0864905f-5944-4659-8f35-354c4035679f.png)

流程图描述：

1. 请求方根据业务需求生成明文，数据为消息明文或签名信封；

2. 生成随机向量，并根据该随机向量生成对称加密密钥；

3. 使用该密钥加密明文，生成对称加密密文；

4. 从星火链上获取接收方的BID文档；

5. 从文档中取出接收方的公钥，使用该公钥对第2步的对称加密密钥进行非对称加密；

6. 组织加密信封数据结构，发送给接收方；

7. 接收方收到加密信封消息后，检查发送方进行加密时使用的公钥是否属于自己；

8. 接收方使用私钥解密第5步密文，解出对称加密密钥；

9. 接收方根据对称加密密钥解密第3步密文，形成明文。

其中，在BID文档中，定义了身份的公钥，当其他身份向该身份发送加密消息时，其他身份可以在该身份的BID文档中找到该公钥，并使用该公钥对对称密钥加密。BID文档如下所示：

```json
{
  	"@context": "https://www.w3.org/ns/did/v1",
  	"id": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS",
 	...
  	"keyAgreement": [{
      	"id": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-1",
      	"type": "SM2", 
      	"controller": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS",
      	"publicKeyHex": "b07a6604e513ff2954e8…a55b3e"
    }],
  ...
}
```

> 公钥为piblickKeyHex字段。

### 5.2.2.  加密信封字段规范

根据[加密信封结构](#jump4-2)设计的加密信封结构，设计以下字段，对应相应的结构层级，给出编码方式。

| 字段名     | 子字段        | 结构层级                     | 描述                         | 编码方式   |
| ---------- | ------------- | ---------------------------- | ---------------------------- | ---------- |
| header     |               | 加密信封消息头               | 消息头结构                   | JSON元素   |
| header     | alg           | 非对称加密算法               | 加密使用的算法               | 字符串     |
| header     | enc           | 对称加密算法                 | 加密使用的算法               | 字符串     |
| recipients | 收件人列表    | 收件人列表                   | 收件人数组结构               | JSON数组   |
| recipients | encrypted key | 对称加密密钥的非对称加密密文 | 对称加密密钥的非对称加密密文 | 字符串     |
| recipients | kid           | 接收人公钥                   | 非对称加密使用的接收人公钥   | 字符串     |
| iv         |               | 随机向量                     | 生成密钥的随机向量           | Base64编码 |
| ciphertext |               | 对称加密密文                 | 对称加密后的密文数据         | 加密密文   |

BIDComm加密信封示例： 

```json
{
	"typ": "bidcomm-encrypted+json",
	"header": {
		"alg": "SM2",
		"enc": "SM4"
	},
	"recipients":[{
		"encrypted_key":"ZIL6Leligq1Xp……KkGgODG",
		"kid":"did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-1"
	},
	{
		"encrypted_key":"sOjs0A0typIRSsh…….qPnoJ3iCOr",
		"kid":"did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-2"
	}],
	"iv":"X183OV9QdjYtZmc=",
	"ciphertext":"WCufCs2lMZfkxQ0……F_HDR60Jaiw"
}

```

### 5.2.3.  信封嵌套规范

根据加密和签名的选择，消息分为以下四种类型：

- 可验证私有消息：

​		对消息进行加密，在消息中添加签名信息。

​		该类型的使用场景为A向B的隐私通信，消息可验证为A发送，消息内容仅B可读且不可篡改。

- 不可验证私有消息：

​		对消息进行加密，不在消息中添加签名信息。

​		该类型的使用场景为A向B的隐私通信，A可隐藏自己的发送人信息，消息内容仅B可读，但发送方未知。

- 可验证公有消息：

​		消息不加密，在消息中添加签名信息。

​		该类型的使用场景为A发送一个公开凭证，不特定消息接收方。

- 不可验证公有消息：

​		消息不加密，不在消息中添加签名信息。

​		该类消息明文传输，不保证安全性，可用来校验通信格式等用途。

#### 5.2.3.1.   可验证私有消息

根据BIDComm的信封结构嵌套规范，应对消息先签名，后加密。即先对消息明文进行签名，形成签名信封，再对签名信封进行加密，形成加密信封。

该消息为嵌套格式，接收方根据`typ`属性，逐层解析加密信封、签名信封、消息明文。

加密信封JSON示例：

```json
{
	"typ": "bidcomm-encrypted+json",
	"header": {
		"alg": "SM2",
		"enc": "SM4"
	},
	"recipients":[{
		"encrypted_key":"ZIL6Leligq1Xp……KkGgODG",
		"kid":"did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-1"
	}],
	"iv":"X183OV9QdjYtZmc=",
	"ciphertext":"WCufCs2lMZfkxQ0……F_HDR60Jaiw"
}
```

其中，`ciphertext`即为对签名信封加密后的封装，接收方通过解密`ciphertext`数据，即可获取签名信封数据。

签名信封JSON示例：

```json
{
	"typ": "bidcomm-signed+json",
	"signatures":[{
		"header":{
			"alg": "SM2",
			"kid": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-1",
		},
		"signature":"FW33NnvOHV0Te…WNMgP2EVCQ",
	}],
	"payload": "eyJpZCI6IjEyMzQ1Njc4O…aXRzIHZhbHVlIn19"
}
```

其中，`payload`即为消息明文Base64编码后的封装，接收方通过解码`payload`数据，即可获取消息明文数据。

嵌套流程如图5-3所示。

> 图 5-3 嵌套流程图

![image](https://user-images.githubusercontent.com/90955034/145359873-75bdf5f4-5da0-4b60-801d-6d5667549729.png)

1. 使用发送方私钥对该明文进行签名；

2. 生成明文Base64编码后的`payload`；

3. 生成签名信封；

4. 生成签名信封数据密文`ciphertext`；

5. 生成加密信封，完成消息明文、签名信封、加密信封的嵌套；

6. 当接收方收到消息后，首先从加密信封中根据`ciphertext`获取到签名信封数据；

7. 根据签名信封中的`payload`和发送方公钥对`signature`签名进行验证；验证通过后，代表`payload`真实有效；

8. 解码`payload`，解析消息明文。

#### 5.2.3.2.   不可验证私有消息

对消息进行加密，不在消息中添加签名信息。

跳过签名信封封装，直接将消息明文数据加密生成密文`ciphertext`。

接收方根据每层`typ`值判断解析层级。

#### 5.2.3.3.   可验证公有消息

消息不加密，在消息中添加签名信息。

跳过加密信封封装，直接将签名信封发送至接收方。

接收方根据每层`typ`值判断解析层级。

#### 5.2.3.4.   不可验证公有消息

消息不加密，不在消息中添加签名信息。

直接将消息明文发送至接收方。

接收方根据每层`typ`值判断解析层级。

## 5.3.  <span id="jump5-3">消息明文规范</span>

### 5.3.1.  消息类型

使用`@type`属性为消息指定类型。为消息的必要属性。

`@type`属性值必须为一个有效的消息类型URI，接收方可通过消息类型的描述，预测发送方的消息意图，并按照此类型的消息格式对消息头和消息主体进行解析。

**消息类型格式设计规范：**

使用URI明确地标识消息类型，URI的格式有标准化定义，和消息映射的解析，通过协议中的消息类型，交互双方可以明确通信的协议类型和版本。

一个标准的消息类型URI的构成如下所示：

```
protocol-name / protocol-version / message-type-name
```

- 消息类型分为三部分，每部分用“/”隔开；

- protocol-name为协议名称；

- protocol-version为协议版本；

- message-type-name为消息类型名称。

**BIDComm中默认消息类型如下：**

| 消息类型     | 消息格式                                        | 消息描述                                 |
| ------------ | ----------------------------------------------- | ---------------------------------------- |
| 发起连接请求 | bidcomm_ping/1.0/request                        | 请求建立bid通信                          |
| 回复连接请求 | bidcomm_ping/1.0/response                       | 回复建立通信的请求                       |
| BIDAuth验证  | bidcomm_application/1.0/bidauth                 | BIDAuth相关流程                          |
| 可验证声明   | bidcomm_application/1.0/  verifiable_credential | 可验证声明相关流程，包括证书颁发、验证等 |

```json
{
	"@type": "bidcomm_ping/1.0/request"
}
```

**消息类型扩展：**

协议的消息类型支持扩展，根据通信双方的业务场景需要，双方可自定义协议消息类型，达成特定通信目的。

### 5.3.2.  消息ID

消息ID为消息的唯一标识，使用`@id`属性为消息指定id。为消息的必要属性。

消息的发送方负责创建消息ID，任何消息都可以通过发送方BID和消息ID的组合进行标识。消息ID应为一个足够独特的标识符，其应当为全球唯一的值。

消息ID格式设计规范：

- id的值是一个不大于32字节的字符串，符合正则表达式`[-_./a-ZA-Z0-9]{8,64}`；

- 不区分大小写；

- 其格式不受严格控制，建议使用UUID。

```json
{
	"@id": "ace2b31a-8895-4547-8a54-1abf75aa1137"
}
```

### 5.3.3.  消息修饰

协议拥有消息修饰字段，是对消息的补充表述的修饰信息。

可以有选择地添加消息修饰信息，所有修饰信息均为可选项。

#### 5.3.3.1.   确认标识符

please_ack：接收者的BID，字符串数组类型。发送者使用该属性来表示本消息希望收到数组列表中的接收者的已读回执，接收者在回复消息中需指定`ack`属性。

ack：被回复消息的`@id`，若收到的消息包含`please_ack`属性，则回复的消息中需包含`ack`属性，表示接收者向发送者确认已收到此`@id`的消息。

消息发送：

```json
{
	"@type": "bidcomm_ping/1.0/request",
	"@id": "ace2b31a-8895-4547-8a54-1abf75aa1137",
	"please_ack": ["did:bid:example_abc", "did:bid:example_efg"]
}

```

消息回复：

```json
{
	"@type": "bidcomm_ping/1.0/response",
	"@id": "14a719d3-1d9e-4db5-9106-7764061dd7fa",
	"ack": "ace2b31a-8895-4547-8a54-1abf75aa1137"
}
```

#### 5.3.3.2.   时间标识符

timing：时间标识符，修饰时间相关字段，包括消息创建时间和消息过期时间。

- created_time：描述发送者创建消息的时间，以时间戳表示；

- expires_time：发送方规定的消息过期时间，以时间戳表示；

- _l10n：数据本地化，可用当地时间描述相关字段，如`“created_time_l10n”: “2021-01-01T01:00:00Z”`。

```json
{
	"@type": "bidcomm_ping/1.0/request",
	"@id": "ace2b31a-8895-4547-8a54-1abf75aa1137",
	"timing": {
		"created_time": "1637546400",
		"expires_time": "1637719200",
		"created_time_l10n": "2021-11-22T10:00:00Z",
		"expires_time_l10n": "2021-11-24T10:00:00Z"
	}
}
```

#### 5.3.3.3.   线程标识符

thread：线程标识符，用来描述消息间的关系。

##### 5.3.3.3.1. 消息序列

消息线程是指一个消息序列，在`thread`中使用`thid`作为该组会话的唯一标识。`thid`的值与`@id`的值符合相同的设计规范。一般而言，在一个消息序列中，第一条消息的`@id`值即为后续所有消息的`thid`值。

不同的`thid`消息被认为不在同一个消息序列中。

当在一个序列中触发另一个序列时，称之为父序列和子序列，在子序列中使用`pthid`字段标识父序列的`thid`值，以表示子消息序列的从属关系。子消息序列中亦有`thid`属性，其值为子会话的首条消息`@id`。

- thid：本消息序列的唯一标识；

- pthid：本消息序列的父级消息序列唯一标识，为父级的`thid`。

假设一个基于BIDComm的颁发证书通信正在进行中。在此交互阶段，证书发行者需要持证者进行身份认证。处于消息组合性等考虑，既要标明身份认证是基于证书颁发的会话，又要将身份认证和证书颁发的交互独立化，因此，一个基于证书颁发的子消息序列便诞生了。在这个例子中，颁发证书交互（消息序列1）是身份认证交互（消息序列2）的父级，在消息序列2中的`thread`属性中必须包含`pthid`字段，其值为消息序列1中的`thid`值。

消息发送：

```json
{
	"@type": "bidcomm_ping/1.0/request",
	"@id": "ace2b31a-8895-4547-8a54-1abf75aa1137",
	"thread": {
		"thid": "ace2b31a-8895-4547-8a54-1abf75aa1137"
	}
}
```

消息回复：

```json
{
	"@type": "bidcomm_ping/1.0/response",
	"@id": "14a719d3-1d9e-4db5-9106-7764061dd7fa",
	"thread": {
		"thid": "ace2b31a-8895-4547-8a54-1abf75aa1137"
	}
}
```

消息发送，子交互：

```json
{
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "852c7162-d4a2-45ba-9dcb-e0d37c1fa296",
	"thread": {
		"pthid": "ace2b31a-8895-4547-8a54-1abf75aa1137",
		"thid": "852c7162-d4a2-45ba-9dcb-e0d37c1fa296"
	}
}
```

消息回复，子交互：

```json
{
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "b8610adc-5971-4867-9fcf-5b6827e46a21",
	"thread": {
		"pthid": "ace2b31a-8895-4547-8a54-1abf75aa1137",
		"thid": "852c7162-d4a2-45ba-9dcb-e0d37c1fa296"
	}
}
```

##### 5.3.3.3.1. 消息序号

在交互中，交互双方需要知道在消息序列中，消息应当如何排序，因为本协议允许发送方在回复方响应之前再次发送若干消息。然而时间戳可能并无法准确地描述发送方期望的消息顺序。因此，引入一个数字序号表示发送消息的顺序。

同样地，回复方再回复时，需要附上已收到发送者消息的最大序号，以达到让发送者了解接收方是否已经接收到发送方的最后一条消息的目的。

每一方的第一条消息以序号0开始，依次加一。

- sender_order：数字序号，标明此条消息为发送方在此消息序列中的第几条消息；

- receiver_orders：接收方回复消息时，报告给发送方其接收到的消息中最高的`sender_order`值，其作用为一个隐式ACK。由于可能是两人以上的多方通信，消息回复中本字段可以拥有多个元素。未发送消息但也需要在交互中指明回复的对象，以-1表示。如`"received_orders": {"did:bid:abcdef":0, "did:bid:ghijkl":11, "did:bid:mnopqr":-1}`。

消息序号再新的消息序列或子消息序列中重新开始编号。

消息发送：

```json
{
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "ace2b31a-8895-4547-8a54-1abf75aa1137",
	"thread": {
		"thid": "ace2b31a-8895-4547-8a54-1abf75aa1137",
		"sender_order": 3,
		"received_orders": {"did:bid:abcdef":1}
	}
}
```

消息回复：

```json
{
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "14a719d3-1d9e-4db5-9106-7764061dd7fa",
	"thread": {
		"thid": "ace2b31a-8895-4547-8a54-1abf75aa1137",
		"sender_order": 2,
		"received_orders": {"did:bid:ghijkl":3}
	}
}
```

### 5.3.4.  消息主体

消息主体由`content`属性描述，其内容根据`@type`属性、具体的应用场景，拥有不同的、为特定场景服务的数据结构和字段描述。

应用场景中依据不同的流程，可能会自行设计type值、id值等，与基础协议中的字段不冲突，且应用服务自行解析。

例如一个简单的content例子：

```json
{
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "ace2b31a-8895-4547-8a54-1abf75aa1137",
	"content": {
		"type": "bidauth_request",
		"bid": "did:bid:efCB2YndSDRHrMF7mTbGVkpbatAdybKW",
		"publicKey": "b06566014b06ef94821e325e047a880b328aa9276060b97d6b148e16199ab1b1edff11"
	}
}
```

# 6. 实现

# 6.1 协议应用

### 6.1.1 BIDAuth应用

两个BID身份之间进行交互时，第一步需要进行相互的认证；认证的过程定义为bidAuth，依赖于“挑战-响应”方式。

BIDAuth序列图如图6-1所示。

> 图 6-1 BIDAuth序列图

![image](https://user-images.githubusercontent.com/90955034/145359931-0831d5c0-df25-4952-966d-72fdd851a67b.png)

主要过程分为：

- 请求方发送认证邀请，请求方发送自己的BID给接收方；

- 接收方收到Auth认证请求后，产生一个随机数，返回给请求方，进行挑战；同时，从星火链上解析该BID的BID文档；

- 请求方使用自己的私钥对随机数进行签名，发送给接收方，进行应答；

- 接收方根据从星火链拿到请求方BID文档中的公钥，以及随机数对签名进行验签，验签的结果就是Auth认证的结果；

BIDAuth通信过程Json示例：

**1)**  **请求方发送认证邀请**

```json
{
	"typ": "bidcomm-plaintext+json",
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "ace2b31a-8895-4547-8a54-1abf75aa1137",
	"content": {
		"type": "bidauth_request",
		"bid": "did:bid:efCB2YndSDRHrMF7mTbGVkpbatAdybKW",
		"publicKey": "b06566014b06ef94821e325e047a880b328aa9276060b97d6b148e16199ab1b1edff11"
	}
}
```

BIDComm字段详解：

- @type：BIDComm协议的类型

- @id：消息的id

- content：消息主体
  - type：在BIDAuth过程中的动作；
  - bid：请求方的bid；
  - publicKey：可选项，请求方的公钥；接收方可用链上查询的BID文档中的公钥与该公钥进行对比。

**2)**  **接收方返回随机数nonce，进行挑战**

接收方会根据请求方提供的bid，从星火链上获取请求方的BID文档，该流程不在协议讨论范围内。

```json
{
	"typ": "bidcomm-plaintext+json",
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "14a719d3-1d9e-4db5-9106-7764061dd7fa",
	"content": {
		"type": "bidauth_challenge",
		"nonce": "8989ab45f3"
	}
}
```

- nonce：接收方随机生成的nonce，发送给请求方，请求方需要对该nonce进行签名；

**3)**  **请求方发送对nonce的签名，进行应答**

```json
{
	"typ": "bidcomm-plaintext+json",
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "852c7162-d4a2-45ba-9dcb-e0d37c1fa296",
	"content": {
		"type": "bidauth_response",
		"plainText": "8989ab45f3",
		"bid": "did:bid:efCB2YndSDRHrMF7mTbGVkpbatAdybKW",
		"publicKey": "b06566014b06ef94821e325e047a880b328aa9276060b97d6b148e16199ab1b1edff11",
		"signText": "U2FsdGVkX19Oxb+c5WAbp5/d/Bp8xpBZdaNXPgPbUnE="
	}
}
```

- plainText：接收方发过来的nonce值；

- bid：请求方的bid；

- publicKey：请求方的公钥；

- cryptoType：签名类型；

- signtext: 申请方使用自己的私钥，对nonce的签名值。

**4)**  **返回认证结果**

接收方会根据从星火链上获取到的请求方的公钥，对申请方发过来的签名进行验证。签名的验证结果即是Auth认证的结果，接收方将结果返回给请求方。

```json
{
	"typ": "bidcomm-plaintext+json",
	"@type": "bidcomm_application/1.0/bidauth",
	"@id": "b8610adc-5971-4867-9fcf-5b6827e46a21",
	"content": {
		"type": "bidauth_result",
		"bid": "did:bid:efCB2YndSDRHrMF7mTbGVkpbatAdybKW",
		"result": true
	}
}
```

- bid：请求方的did；

- result：接收方的auth认证结果，布尔类型。

至此，一个完整的BIDAuth流程结束，通信双方可继续进行交互。

# 7. 缺陷

暂无。

 