# 星火链网RFC-007：星火链网BIDAuth协议标准

# 1. 序言

编号：RFC-007

类型：标准

标题：星火链网BIDAuth协议标准

作者：金健，[jinjian1@caict.ac.cn](mailto:jinjian1@caict.ac.cn)；谢家贵，[xiejiagui@caict.ac.cn](mailto:xiejiagui@caict.ac.cn)；李志平，[lizhiping@caict.ac.cn](mailto:lizhiping@caict.ac.cn)；马若龙，[maruolong@caict.ac.cn](mailto:maruolong@caict.ac.cn)；郭世杰，[guoshijie@caict.ac.cn](mailto:guoshijie@caict.ac.cn)

发布时间：2021-12-08

状态：采纳

更新时间：2021-12-09

讨论地址：https://github.com/caict-4iot-dev/BIF-RFCs/issues/4

依赖RFC：[星火链网RFC-002：星火链网分布式标识BID](星火链网RFC-002：星火链网分布式标识BID.md)

​                  [星火链网RFC-006：星火链网BIDComm协议标准](星火链网RFC-006：星火链网BIDComm协议标准.md)

# 2. 摘要

本文介绍星火链网BIDAuth认证协议的流程和规范。

# 3. 动机

BIDComm提供了节点之间的交互，在交互之前，节点之间需要相互认证，认证的过程称之为BIDAuth， 这篇RFC阐释了BIDAuth的工作流程和相关规范。
# 4. 原理

下面将从BIDAuth简介、系统构成的角色、整体流程方面进行阐述。

## 4.1  简介

在BID数字世界中，如何去验证另外一方身份的真实性，往往没有在现实世界中直观和具体。星火链BIDAuth协议就是为了解决在数字世界中，验证对方身份的协议。

在BID数字世界中，需要借助于密码学知识去进行各方身份的相互验证，在BID文档中，通过指定相应的机制来实现数字世界的身份认证。BIDAuth身份认证建立通信通道后在此基础上进行授权、可验证凭据等功能实现（上层功能并不在本RFC讨论范围）

## 4.2 BIDAuth介绍

BIDAuth描述了用户对某数字身份BID拥有控制权，用户只需证明自己拥有该数字身份对应的私钥，可以使用私钥解密出该数字身份对应的公钥所加密的密文。

经过BIDAuth认证之后，不同数字身份之间建立通信通道，并且在此基础上进行授权、可验证凭据等功能实现。

BIDAuth与其它身份验证方法类似，依赖于“挑战-应答”方式，在本协议中，结合BID的特性，制定了“挑战-应答”的过程。

## 4.3 系统构成的角色

在BIDAuth的协议中，通常需要两个数字身份实体来进行该流程的操作，操作过程中会涉及到BID、BID文档、Authentication等相关概念。下面是对这些角色或内容的介绍：

BID：星火标识BID（Blockchain-based Identifier，简称BID），是星火·链网的数据载体，也是星火链底层支持的原生地址，同时BID还是加入到分布式身份标识符DID注册表的一个METHOD，它是全球唯一的标识符，不需要集中的注册机构。

BID 文档：包含描述BID详细数据结构的文档，包括身份验证材料，例如公钥和不同server端点，数字实体之间可使用这些数据进行相互身份验证，即证明对 BID 的控制，主要的思想是：BID文档中包括公钥，其他数字实体B使用该公钥对某一内容进行加密，如果拥有该公钥的实体A能够解密出其他数字实体B刚才生成的密文，那就说明该实体A拥有私钥，享有对该BID的控制。BID 文档还可能包含描述实体的其他属性或声明。 

Identity Owner：身份所有者，创建 BID 的个人、组织或事物，使用BID标识该身份所有者，Identity Owner拥有更新或撤销 BID 的最终权力；

Authentication：身份验证，身份所有者向验证方证明它对BID拥有控制权的过程，会使用到BID对应的BID文档中的公钥。

发起方：主动使用BID Auth协议，请求验证身份的个人、组织或事物。

验证方：使用BID Auth协议验证身份所有者的个人、组织或事物。

## 4.4 BIDAuth整体流程

两个BID身份之间进行交互时，第一步需要进行相互的认证；认证的过程定义为BIDAuth，依赖于“ 挑战-应答”方式。

下图是BIDAuth 序列图：
<img width="492" alt="954778" src="https://user-images.githubusercontent.com/76681420/145170809-6007a25b-7b27-4c89-b9fe-7ec8489e4501.png">


主要过程分为：

+ 请求方发送认证邀请，请求方发送自己的BID给接收方；

+  接收方收到Auth认证请求后，产生一个随机数，返回给请求方，进行挑战；同时，从星火链上解析该BID的BID文档；

+ 请求方使用自己的私钥对随机数进行签名，发送给接收方，进行应答；

+ 接收方根据从星火链拿到请求方BID文档中的公钥，以及随机数对签名进行验签，验签的结果就是Auth认证的结果；

# 5. 规范

该部分内容是对整体流程进行拆解后，对每一个步骤进行规范化的指导。

## 5.1 请求方发送认证邀请

BIDAuth通信过程Json示例：

1)  请求方发送认证邀请

```json
{
    "@type": "bidcomm_application/1.0/bidauth",
    "@id": "ace2b31a-8895-4547-8a54-1abf75aa1137",
    "content": {
	"type": "bidauth_request",
	"bid": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS ",
	"publicKey": "b06566014b06ef94821e325e047a880b328aa9276060b97d6b148e16199ab1b1edff11"
    }
}
```

BIDComm字段解释：

@type：BIDComm协议的类型

@id：消息的id

content：消息主体

具体内容,包括：

type：在BIDAuth过程中的步骤动作；

bid：请求方的bid；

publicKey：可选项，请求方的公钥；接收方可用链上查询的BID文档中的公钥与该公钥进行对比；

### 5.1.1 @type规范

使用@type属性为消息指定类型。为消息的必要属性。

type属性值必须为一个有效的消息类型URI，接收方可通过消息类型的描述，预测请求方的消息意图，并按照此类型的消息格式对消息头和消息主体进行解析。

**消息类型格式设计规范：**

使用URI明确地标识消息类型，URI的格式有标准化定义，和消息映射的解析，通过协议中的消息类型，交互双方可以明确通信的协议类型和版本。

一个标准的消息类型URI的构成如下所示：

  ```
protocol-name / protocol-version / message-type-name
  ```

消息类型分为三部分，每部分用“/”隔开；

+ protocol-name为协议名称；

+ protocol-version为协议版本；

+ message-type-name为消息类型名称；

BIDComm中默认消息类型如下：


| 消息类型     | 消息格式                                        | 消息描述                                 |
| ------------ | ----------------------------------------------- | ---------------------------------------- |
| 发起连接请求 | bidcomm_ping/1.0/request                        | 请求建立bid通信                          |
| 回复连接请求 | bidcomm_ping/1.0/response                       | 回复建立通信的请求                       |
| BIDAuth验证  | bidcomm_application/1.0/bidauth                 | BIDAuth相关流程                          |
| 可验证声明   | bidcomm_application/1.0/  verifiable_credential | 可验证声明相关流程，包括证书颁发、验证等 |

**消息类型扩展：**

协议的消息类型支持扩展，根据通信双方的业务场景需要，双方可自定义协议消息类型，达成特定通信目的。

```json
{
    "@type": "bidcomm_ping/1.0/request"
}
```

### 5.1.2 @id规范
消息ID为消息的唯一标识，使用@id属性为消息指定id。为消息的必要属性。

消息的发送方负责创建消息ID，任何消息都可以通过发送方BID和消息ID的组合进行标识。消息ID应为一个足够独特的标识符，其应当为全球唯一的值。

消息ID格式设计规范：

+ id的值是一个不大于32字节的字符串，符合正则表达式[-_./a-ZA-Z0-9]{8,64}

+ 不区分大小写

+ 其格式不受严格控制，建议使用UUID

```
{
    "@id": "ace2b31a-8895-4547-8a54-1abf75aa1137" 
}
```

### 5.1.3 content规范

content内容具体包括type、bid、publicKey。
#### 5.1.3.1 type

在BIDAuth协议中，涉及到多个步骤，可以使用type字段对每一个步骤进行定义。为了更直观的表述，type类型尽量地能够反应出步骤的作用，如下表所指示：
| type_value        | 步骤描述                                                     |
| ----------------- | ------------------------------------------------------------ |
| bidauth_request   | 请求方发送BIDAuth认证邀请给接收方，是BIDAuth的开始           |
| bidauth_challenge | 接收方收到请求方的认证邀请后，生成并返回一个随机数nonce给请求方，对请求方进行挑战 |
| bidauth_response  | 请求方收到挑战后，使用自己的非对称加密私钥对nonce进行签名，并返回给接收方，进行应答 |
| bidauth_result    | 接收方向请求方返回认证结果                                   |


#### 5.1.3.2 bid

在BIDAuth协议中，需要使用到BID规范中的BID。

BID的组成结构参见[星火链网RFC-002：星火链网分布式标识BID](星火链网RFC-002：星火链网分布式标识BID.md#bid)中的BID规范。

#### 5.1.3.3 publicKey

公钥可用于数字签名、加密操作，而在BIDAuth中，使用签名的目的是实现挑战-应答过程。请求方使用私钥对随机数进行签名，接收方使用私钥对应的公钥对签名进行验证，根据签名的不可抵赖性，如果验证通过，则证明请求方拥有对BID的控制权。

请求方的公钥存在于请求方的BID文档中，比如在publicKey字段中，定义了两个公钥：




```json
{
  "id": "did:example:123456789abcdefghi",  
  "publicKey": [
	{
      "id": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-1",
      "type": "Ed25519",
      "controller": "did:example:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS ",
      " publicKeyHex": "b06566ce8c1efd23bc8193da2650dcdfa8efa84da29df"
	}, 
	{
      "id": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS#keys-2",
      "type": "SM2",
      "controller": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS ",
      "publicKeyHex": "b07a660474e69c1ad9d29a95d15c644720b2c6136fa31"
	}
  ]
}
```

制定公钥的规则：包含id，type，controller,publicKeyHex四个字段。

+ id:公钥的ID。

+ type：字符串，代表公钥的加密算法类型，支持Ed25519和SM2两种。

+ controller：一个BID,表明此公钥的归属。

+ publicKeyHex：公钥的十六进制编码。


## 5.2 接收方返回随机数nonce，进行挑战

接收方会根据请求方提供的bid，从星火链上获取请求方的BID文档，该流程不在协议讨论范围内。

```json
{
    "@type": "bidcomm_application/1.0/bidauth",
    "@id": "14a719d3-1d9e-4db5-9106-7764061dd7fa",
    "content": {
        "type": "bidauth_challenge",
        "nonce": "8989ab45f373ba42"
    }
}
```

@type、@id的规范，请参考4.2中的内容。

content中：

type：表示接收方收到请求方的认证邀请后，生成并返回一个随机数nonce给请求方，对请求方进行挑战。

nonce：接收方随机生成的nonce，发送给请求方，请求方需要对该nonce进行签名；

推荐使用uuid生成随机数字符串，默认是16位字节。

uuid_rfc: https://www.ietf.org/rfc/rfc4122.txt

## 5.3 请求方发送对nonce的签名，进行应答

当请求方收到接收方的挑战后，获取到接收方发送的nonce，使用自己的私钥对nonce进行签名，将签名内容发送给接收方，进行应答。

发送给接收方的内容：

```json
{
    "@type": "bidcomm_application/1.0/bidauth",
    "@id": "852c7162-d4a2-45ba-9dcb-e0d37c1fa296",
    "content": {
        "type": "bidauth_response",
        " plainText": "8989ab45f373ba42",
        "bid": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS",
        "publicKey": "b06566014b06ef94821e325e047a880b328aa9276060b97d6b148e16199ab1b1edff11",
        "cryptoType": "SM2",
        "signText": "U2FsdGVkX19Oxb+c5WAbp5/d/Bp8xpBZdaNXPgPbUnE="
    }
}
```

@type、@id的规范，请参考4.2中的内容。

content中：

type：表示请求方收到挑战后，使用自己的非对称加密私钥对nonce进行签名，并返回给接收方，进行应答。

plainText：接收方根据uuid生成的nonce值；请求方不用关心具体生成过程，只需要对该nonce值进行签名即可。

bid：请求方的bid

publicKey：请求方的公钥，接收方收到该消息后，会使用该公钥去验证签名。在验证之前，接收方会根据请求方的bid从星火链上获取到对应的BID文档，首先验证该公钥是否存在于BID文档中，如果存在，代表公钥可靠，反之，代表公钥有可能不是请求方的。

cryptoType：签名类型，可以使用ED25519、SM2进行签名，推荐使用SM2进行签名。

signtext：签名内容。请求方使用publicKey对应的私钥，对nonce的签名值。

## 5.4 返回认证结果

接收方收到请求方应答后；

首先，根据请求方的bid，从星火链上获取到对应的bid文档；

验证应答中的publicKey是否存在于bid文档中，若不存在，认证失败；

若存在，根据该publicKey、随机数nonce去验证签名内容signtext，若验证不通过，那么认证失败；

若验证通过，即代表BIDAuth认证结果通过。

接收方将结果返回给请求方。

```json
{
    "@type": "bidcomm_application/1.0/bidauth",
    "@id": "b8610adc-5971-4867-9fcf-5b6827e46a21",
    "content": {
        "type": "bidauth_result",
        "bid": "did:bid:zf27zkk8D72F13HAzY1ECsK12VPUHxKZS",
        "result": true
    }
}
```

@type、@id的规范，请参考4.2中的内容。

content中：

type：表示接收方向请求方返回认证结果。

bid：请求方的bid

result：接收方的auth认证结果，bool类型。

# 6. 实现

| 名词/ 链接 | 实现说明                                           |
| ---------- | -------------------------------------------------- |
| bif        | 星火链网的BID代理源代码中，有BIDAuth流程规范的实现 |


# 7. 缺陷

暂无