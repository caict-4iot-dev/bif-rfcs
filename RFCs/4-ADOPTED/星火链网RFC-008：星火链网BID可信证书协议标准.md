# 星火链网RFC-008：星火链网BID可信证书协议标准

# 1. 序言

编号：RFC-008

类型：标准

标题：星火链网BID可信证书协议标准

作者：金键，[jin.jian@caict.ac.cn](mailto:jin.jian@caict.ac.cn)；谢家贵，[xiejiagui@caict.ac.cn](mailto:xiejiagui@caict.ac.cn)；李志平，[lizhiping@caict.ac.cn](mailto:lizhiping@caict.ac.cn)；郭世杰，[guoshijie@caict.ac.cn](mailto:guoshijie@caict.ac.cn)；马若龙，[maruolong@caict.ac.cn](mailto:jin.jian@caict.ac.cn)

发布时间：2021-11-24

状态：采纳

更新时间：2021-12-14

讨论地址：https://github.com/caict-4iot-dev/BIF-RFCs/issues/7

依赖RFC：[星火链网RFC-002：星火链网分布式标识BID](星火链网RFC-002：星火链网分布式标识BID.md)

​                  [星火链网RFC-006：星火链网BIDComm协议标准](星火链网RFC-006：星火链网BIDComm协议标准.md)

# 2. 摘要

本文介绍星火链网上基于BID分布式身份的可信证书协议标准和系统设计。

# 3. 动机

证书是日常社会生活中的重要组成部分，例如驾照证明所有者的驾驶能力，学历证书证明所有者智力水平和专业素养，有了各种证书社会工作才能高效率的组织和进行。

随着各种工作逐渐转移到互联网上，证书的线上使用场景越来越多。但目前的证书线上使用方式通常是拍照后网上传递。这种方式存在个人隐私泄露，图片恶意伪造等风险，验证过程需要人工介入，成本高昂，时效性较慢。需要有一种真正将证书数字化的方式来解决线上场景的证书使用问题。

# 4. 原理

## 4.1 背景和基本概念

### 4.1.1 可信数字证书

可信数字证书指通过数字化描述和保存，并基于密码学方法做有效性证明的证书形式。作为对比，传统的物理介质证书一般包括以下内容：

1.	证明主体信息（姓名，身份证号等）
2.	发行方信息（学校，公安等）
3.	证书类型（学历，驾照）
4.	证书证明结论（学位，健康码）
5.	证书限制条件（有效期，有效地域范围）

可信数字证书将以上信息数字化表达，再基于密码学技术例如数字签名等做防伪证明，使得持有人可以在不担心被伪造篡改的前提下使用可信数字证书。

相对物理证书，可信数字证书的保存，传输和使用会更加方便。同时，数字证书还可以基于选择性披露，零知识证明等密码学方法来做隐私保护，相对物理证书会更加的安全。

### 4.1.2 星火链网

星火·链网-底层区块链平台(BIF-Core)，简称BIF-Core，是中国信息通信研究院基于区块链技术打造的一条许可公有链，是实现“星火·链网”这一国家级区块链与工业互联网协同创新新型基础设施的关键技术。

BIF-Core内置了星火标识（BID，Blockchain-based IDentifier），为人、企业、设备和数字对象等提供标识注册解析、数字身份等基础服务。BIF-Core采用“主链+子链”链群架构，主链负责链群管理、监管规则、公共数据和价值锚定，子链针对不同业务场景独立设计，可通过许可制接入主链，实现数据安全隔离、高性能运行。BIF-Core立足安全、自主、可控的原则，面向各行业领域开源开放，赋能全行业开发者，共建生态繁荣。

### 4.1.3 基于星火链网分布式身份的可信数字证书协议

星火链提供的不可篡改存储，智能合约提供的可信执行，配合星火链分布式身份标识体系BID，非常适合可信证书系统。

本文描述了一套在星火链基础架构上运行，基于星火BID体系的可信数字证书系统和协议规范。

## 4.2. 系统构成和使用场景

### 4.2.1 系统构成

可信数字证书体系中的主要参与者有：证书发行方、证书主体、证书持有者、证书验证方，和公开可信数据集。

![image](https://user-images.githubusercontent.com/1982712/145777655-2f971713-3afc-4382-8808-c0d7c9ab7aaf.png)

1. 证书发行方

    证书发行方通常为权威机关如公安局，高校等，通过密码学技术签名为证书主体颁发可信数字证书。

    通过认证的发行方bid地址和资质信息会统一放在公开可信数据集里供相关参与方参考查询。

1.	证书主体

    可信数字证书针对的主体，用bid地址唯一标识。

1.	证书持有者

    证书持有者和证书主体一般是同一个对象，也可能主体将证书统一托管给第三方管理，或者其他主体代持（比如父母持有子女的相关证书）。

1. 证书验证方

    需要对数字证书进行验证的角色，可能是需要验证用户资质的服务商，也可能是第三方专门做验证的服务商。证书验证方负责对证书持有者提交的可信声明进行校验。

1.	可信公开数据集

    可信数字证书系统需要一些公开参考数据来提供系统运作基础，比如各大权威发证方的标识地址，各种特定证书和发行方的对应关系以及证书格式等。

### 4.2.2 系统工作流程

基于星火链的可信数字证书系统的通常运作流程如下：

1. 用户向发行方提交材料，包括自己的bid，自证材料等，待发行方验证后，对该bid主体颁发可信数字证书，用户持有并自行保存该证书。

2. 各个参与方按照业务需求调用对应智能合约更新可信数据集中的信息，包括可颁发的证书类型，证书格式，撤回证书列表等。

3. 到使用场景时，用户基于持有证书构造可信声明，通过线上发送给验证方。

4. 验证方按照规定校验声明的各个条件，通过/不通过验证并给与用户答复。

# 5. 规范

## 5.1 协议内容范围

本协议讨论和规定范围主要包括：

1. 发行方签发数字证书的格式和规范。
2. 持有方构造可信声明的格式规范
3. 持有方和验证方证书交互流程规范
4. 可信公开数据集的访问方式
5. 证书生命周期管理规范

本协议不包括的范围：

1. 用户与发行方申请证书的流程
2. 用户提交证书前与验证方的沟通流程
3. **本协议**未规定交互流程中如何去识别用户id, 例如证书依赖方或者验证方在接收用户提交的可信声明时, 如何确认当前交互用户即为证书主体用户bid, 建议的解决方案如下:
   1. 具体业务使用时, 增加前置auth环节, 通过签名验证用户bid后, 用户在当前对话session内保持该bid不变.

   1. 每次信息交互外部都套一层签名信息, 用来验证用户bid.

## 5.2 星火联网基于BID的可信证书协议

### 5.2.1 数据模型和定义

#### 5.2.1.1 星火标识BID

星火·标识，即BID标识，是基于W3C DID标准开发的新型分布式标识，任意实体可自主生成BID，不需要中心化注册机构就可以实现全球唯一性，具有分布式、自主管理、隐私保护和安全易用等特点。

星火·链网-底层区块链平台的原生内嵌地址使用BID，主链底层原生账户地址预置可信属性，保障区块链地址的安全可信；子链通过增加AC号的BID标识其业务实体，实现全网BID的统一聚合管理。

BID的组成结构参见[星火链网RFC-002：星火链网分布式标识BID](星火链网RFC-002：星火链网分布式标识BID.md#bid)中的BID规范。

BID结合区块链可以提供一个安全强大的去中心化解决方案，BIF-Core上存储BID公钥，实现了去中心化公钥存储，从而使两个BID的持有者（人或智能设备等需要网络通信的实体）可以通过SSI体系进行可控地安全交互，赋能星火·链网万链互联。

#### 5.2.1.2 可信数字证书

证书发布方发布给特定主体的资质数字证明文件，发证方用BID公钥对应私钥对证书内容签名认证。

证书可以存在星火链上通过唯一标识引用，也可以内嵌在可信声明中。

证书的保存可以由证书主体自己管理，也可以委托给第三方管理。

#### 5.2.1.3 证书验证请求

当业务场景需要验证用户资质时，资质依赖方向证书持有方发出的验证请求。

证书验证请求里标明了验证所需要的证书信息，以及支持的可信声明推断方法，同时依赖方也可以指定某一个第三方验证服务提供商来做验证服务。

#### 5.2.1.4 可信数字声明

出于隐私考虑或者业务场景要求，证书持有方通常不会直接将自己的证书交给验证者，而是将证书组织成可信数字声明，再发送给验证者验证。

从携带的证书内容到声明的结论的推导称为声明推断，声明推断方法包括直接推断，即证书信息本身就是声明内容；也有选择性披露，即只披露证书中的部分属性来完成声明，更复杂的方法还有零知识证明，基于一个或多个证书来得出声明结论。

#### 5.2.1.5 可信公开数据集

可信公开数据集是星火链上的一个公开数据库，由中国信息通信研究院维护，里面主要存放以下内容： 

1. 注册的权威发证方信息
2. 各个发证方自己维护的证书格式及相关信息列表
3. 证书系统运行信息如json-ld基础词汇表，链上的证书验证历史记录等

公开数据集由部署在星火链上的智能合约进行管理，各个证书发行方可以注册并维护自己的证书数据，由中国信息通信研究院统一监管和维护。

### 5.2.2 协议流程

![image](https://user-images.githubusercontent.com/1982712/145779457-3d0f40d2-c9e9-44d3-8ecd-17f35fcc032c.png)

协议假定证书持有方已经提前或者在收到验证需求后，和对应证书发行方沟通并获取到对应证书。证书使用流程如下：

1. 依赖方向主体发出证书验证请求，注明需要的证书条件和验证方信息。
2. 持有方根据验证要求，组合可信证书构造可信声明，发送给指定的验证方。
3. 验证方验证声明后，如果验证通过，返回验证结果。
4. 持有方拿到验证结果后，交给证书依赖方。（如果验证方和依赖方是同一个主体时，这一步可以省略）。

流程中如果有任一步失败，则流程结束。各方依赖的一些公开数据（证书格式，可信发证方信息）等均存在可信公开数据集中。

#### 5.2.2.1 证书格式要求

协议对可信证书的基础字段做了要求和规范，各个发证方针对实际的场景可以在证书里添加扩展字段。

在可信公开数据集上，每个发证方可以更新自己的相关信息，包括自己可以颁发的证书类型和对应证书格式。

可信证书系统各个参与方可以根据可信公开数据集上的内容来颁发、生成和验证证书格式。

#### 5.2.2.2 证书获取和保存

用户可以从可信公开数据集里查找某个特定证书的颁发方地址和颁发要求，具体如何与颁发方沟通和获取证书由实际场景决定。

证书持有方可以选择将证书作为biddocument保存在星火链上，也可以自行链下保存。证书管理、声明推断可以交给持有方的程序或者app，也可以托管给第三方证书管理机构。

#### 5.2.2.3 证书验证申请

当需要证书持有方证明某个资质时，证书依赖方向证书持有方发出证书验证申请，本协议规定了验证申请的格式规范，具体如何发送请求到持有方由使用场景决定。

实际应用时，持有方可以通过deeplink调用证书管理app或者转发给证书管理机构来处理验证请求。

验证申请里也标明了验证方目前能支持的声明构造方式，如直接方式（证书内容直接构成声明内容），或者基于隐私保护考虑，基于各种零知识证明方案的构造方式（参见[可信声明推断](#jump6.5.5)）。

#### 5.2.2.4 构造可信声明

证书持有方收到验证申请后，可以在公开数据集里查询下验证者信息，确认合规后根据验证申请里的要求，基于已有的数字证书构造可信声明，构造方式由验证申请里的支持方式决定。

如果持有方没有对应的证书，流程直接结束，验证失败；有对应的证书但是无法按支持方式构造出可信声明，流程也直接结束，验证失败；构造出可信声明后，持有方将声明发送给验证方。

#### 5.2.2.5 验证声明

收到可信声明后，验证方需要校验声明的内容，通过校验后的后续步骤由具体场景决定。

如果是第三方验证，验证方可以发给持有方一个数字证明，持有方基于该证明与证书依赖方继续业务流程。

如果是依赖方直接验证，那么验证通过后可以直接继续业务流程。

验证方在对声明做验证时，必须验证如下几点：

1. 需要验证声明对应的验证请求合法且未过期。根据业务，验证方可以要求可信声明对应的验证请求id合法且未被用过，防止重放攻击。
2. 对声明里附带的每一个数字证书，需要验证内容签名合法有效。
3. 需要验证所有证书的有效期符合要求，证书格式符合可信公开数据集里的格式定义，并且所有证书均未被撤销。
4. 需要验证所有证书的发证方信息与可信公开数据库里信息一致。
5. 需要验证证书到声明的推断合法有效。

#### 5.2.2.6 证书生命周期管理

1. 证书刷新

    刷新功能非协议强制，但强烈建议证书发行方提供该接口，出于隐私考虑，持有方应当定期刷新持有的各项证书，用来防止隐私泄露和保持格式最新。

2. 证书撤销

    当证书主体不再符合证书资质要求时，发证方可以在可信公开数据集里登记撤销其证书，通过调用星火链可信数据集智能合约登记证书id，即可以标明该证书已经撤销。

# 6. 实现

这里介绍协议涉及的几个主要消息的格式要求和实现细节。另外需要注意，协议里涉及的所有消息均需要以JWS格式签名发送，发送方用自己did document内公钥对应私钥签名。

有关JWS签名，请参见[JWS RFC文档](https://datatracker.ietf.org/doc/html/rfc7515)。

出于安全考虑，签名算法仅支持Ed25519或者SM2。本章只讨论JWS消息内payload部分的格式。

## 6.1 可信数字证书

可信数字证书由证书发行方制作发送给持有方，消息示例如下：


```json
{
    "@context":[
        "https://www.w3.org/2018/credentials/v1",
        "https://www.w3.org/2018/credentials/examples/v1"
    ],
    "id":"http://example.edu/credentials/1872",
    "bid":"did:bid:CredentialID",
    "type":[
        "VerifiableCredential",
        "AlumniCredential"
    ],
    "issuer":"did:bid:CredentialIssuerID",
    "issuanceDate":"2010-01-01T19:23:24Z",
    "templateId":"20100101T23Nedex",
    "validBefore":"2010-10-01T19:23:24Z",
    "credentialSubject":{
        "id":"did:bid:CredentialSubjectID",
        "alumniOf":{
            "id":"did:bid:AlumniID",
            "name":[
                {
                    "value":"Example University",
                    "lang":"en"
                }
            ]
        }
    },
    "revocationId": "20100101NN30d398",
    "parseType": "sel-disclose-SM2"
}
```

可信数字证书的格式参照星火链BID文档规范和[W3C数字可信证书标准](https://www.w3.org/TR/vc-data-model/#toc) ，其中各项数据说明如下：

* @context  json-ld标准格式，一组解释 JSON-LD 文档的规则,遵循 DID 规范，必须包含https://www.w3.org/2018/credentials/v1。
* id: 证书发行方内唯一id，供验证方查询使用。
* bid: 如果该证书有在星火链上保存，填写该证书星火标识。
* type: 数组，证书所属类型，必须包含VerifiableCredential。
* templateId: 模板id，证书扩展字段格式模板编号。
* issuer: 发证方bid地址。
* issuanceDate: 证书发行日期。
* validBefore: 证书有效期截止时间。
* credentialSubject: 证书内容，包含一个必须字段id，其余字段根据协议具体的template变动。
* revocationId: 凭证撤销查询地址，验证方可以在可信公开数据集查询该证书是否被撤销。
* parseType : 证书解析方式，详情参见[可信声明推断](#jump6.5.5)。

## 6.2 资质验证请求

由验证方发送给持证主体，标明需要证书持有方出示的证书相关信息，参考示例如下:

```json
{
    "@context":[
        "https://www.w3.org/2018/credentials/v1"
    ],
    "id":"http://example.edu/credentialrequest/1111",
    "type":"credentialRequest",
    "verifier":"did:bid:VerifierID",
    "sender":"did:bid:SenderID",
    "requirements":{
        "credentials":[
            [
                {
                    "type":"AlumniCredential",
                    "content":{
                        "alumniOf":{
                            "id":"did:bid:AlumniID"
                        }
                    },
                    "minVersion":"1.0.2",
                    "maxVersion":"1.9.1",
                    "validBefore":"2010-01-01T19:23:24Z",
                    "issuer":"did:bid:CredentialIssuerID",
                    "subject":"did:bid:CredentialSubjectID"
                }
            ]
        ],
        "presentation":{
            "composeType":[
                "sel-disclose-SM2"
            ]
        }
    }
}
```

各项数据说明:

* @context: 数组,json-ld规范要求必备字段

* id: 验证请求id，在验证方唯一

* type: 请求类型字段，必须为"credentialRequest"

* verifier: 验证方标识，标准地址格式。

* sender: 消息发送方, 标准地址格式。

* requirements: 验证条件字段, 说明本次验证需要的证书和声明格式：

    * credentials: 证书要求字段, 表示需要持有方出示的可信声明里包含的证书, 格式为一个二维数组. 持有方出示声明时, 证书符合第一层数组内任意子数组内的所有证书要求即可。例如，当credentials为[[a], [b, c]]时，持有方可以出示[a]或者[b,c]的组合来通过验证。

        具体对每一个证书,可以约束以下字段：
        
        * type: 证书类型,必须字段
        * content: 证书需要证明内容，可选字段
        * minVersion: 证书格式版本最小值
        * maxVersion: 证书格式版本最大值
        * issuer: 证书发布方，标准地址格式
        * valideBefore: 证书最早失效时间
        * subject: 证书主体，标准地址格式
    * presentation：表示对可信声明部分的要求
        * composeType：验证方支持可信证书组装成可信声明的形式，具体定义见[可信声明推断](#jump6.5.5)。
    

## 6.3 可信数字声明

收到资质验证请求后，持有方根据要求基于数字证书构造可信声明，示例格式如下：

```json
{
    "@context":[
        "https://www.w3.org/2018/credentials/v1"
    ],
    "type":"VerifiablePresentation",
    "credentialRequest":{
        "jws":""
    },
    "credentialParse": [
        {
            "parseType": "...",
            "format" : ""
        }
    ],
    "verifiableCredential":[
        {
            "@id":"did:bid:CredentialID"
        },
        {
            "jws":"...",
            "proof":{
        		"composeType": "sel-disclose-SM2",
        		"created":"2018-09-14T21:19:10Z",
        		"credentialSubject":"..."
    		}
        },
        {
            "@context":[
                "https://www.w3.org/2018/credentials/v1",
                "https://www.w3.org/2018/credentials/examples/v1"
            ],
            "id":"http://example.edu/credentials/1872",
            "bid":"did:bid:CredentialID",
            "type":[
                "VerifiableCredential",
                "AlumniCredential"
            ],
            "issuer":"did:bid:CredentialIssuerID",
            "issuanceDate":"2010-01-01T19:23:24Z",
            "templateId":"20100101T23Nedex",
            "validBefore":"2010-10-01T19:23:24Z ",
            "credentialSubject":{
                "id":"did:bid:CredentialSubjectID",
                "alumniOf":{
                    "id":"did:bid:AlumniID",
                    "name":[
                        {
                            "value":"Example University",
                            "lang":"en"
                        }
                    ]
                }
            },
            "revocationId":"20100101NN30d398",
            "proof":{
                "type":"RsaSignature2018",
                "created":"2017-06-18T21:19:10Z",
                "jws":"..."
            }
        }
    ],
    "proofs":{
    }
}
```

可信声明的格式参考[W3C标准](https://www.w3.org/TR/vc-data-model/#dfn-verifiable-presentations)。

各项数据说明如下：

* @context：json-ld标准格式，必须包含 https://www.w3.org/2018/credentials/v1

* type：类型字段，必须为VerifiablePresentation。

* credentialRequest：验证请求，对应的资质验证请求的jws token。

* credentialParse：数组,表示各个证书的解析方式,顺序和verifiableCredential里的证书一一对应.

    * format

      证书内容格式, 目前支持'jws'，'bid', 'context'。 

      jws表示jws格式证书，bid表示提供链上证书id，context表示证书原文。

    * parseType

      证书内容解析方式，一般为direct，对于选择性披露格式为**sel-%hash算法%**。参见[可信声明推断](#jump6.5.5)。

* verifiableCredential 可信证书字段，数组，具体证书支持三种格式：
    * @id：证书在链上存有bid document，直接给出bid标识
    * jws：
        * 内嵌数字证书，直接给出对应证书的jws token，如支持选择性披露，则jws为签名后的jws token。参见[可信声明推断](#jump6.5.5)。
        * proof：可选字段，证书推断证明，这里针对不同的推断方式，proof会有不同的内容。参见[可信声明推断](#jump6.5.5)。
            * composeType：证明推断方式。
            * created：证明创建时间。
            * credentialSubject：选择性披露的字段。
    * 内嵌数字证书原文：格式见可信数字证书格式，另外必须增加proof字段，内容是证书发行方对可信证书的签名：
    
        * type：签名算法
        * created：签名时间
        * jws：签名后jws token
    
* proofs ：保留字段，待扩展


## 6.4 验证通过证明

验证通过证明用以在第三方验证场景，验证方签发给证书持有方的证明。持有方可以将该证明交给依赖方表明验证通过。

```json
{
    "@context":[
        "https://www.w3.org/2018/credentials/v1"
    ],
    "type":"PresentationVerified",
    "credentialRequest":{
        "jws":""
    },
    "create":"2010-01-01T19:23:24Z",
    "issuer":"did:bid:CredentialIssuerID"
}
```

各项字段说明如下：

* type：类型字段，必须为PresentationVerified；
* credentialRequest：必须为对应资质验证请求的jws token
* create：验证方证明签发时间
* issuer：发证方的bid

## 6.5. 可信公开数据集

可信公开数据集为一套由部署在星火链网上的智能合约管理的数据集，主要负责数字可信证书体系的基础数据，对外暴露一系列读写接口。

数据集包含的主要数据和接口如下：

### 6.5.1	公开发证方数据

公开发证方数据主要记录各个权威机构的bid地址，统一由信通院管理更新，各个发证方需要申请注册后，信通院通过调用智能合约上链记录。

#### 6.5.1.1	接口

1. https://${url}/issuer
   
    返回当前注册的所有公开发证方信息。

    返回:

    ```json
    [
        { 
            "bid": "",
            "name": ""
        }
    ]
    ```

2. https://${url}/issuer/${bid}

    查询某个具体发证方的信息。

    返回：

    ```json
    {
		"bid": "",
        "name": "",
        "register_date": "",
        "location": "",
        "about": ""
    }
    ```

### 6.5.2	发证方证书类型、模板元数据

注册在案的发证方可以调用智能合约注册自己支持的证书类型并登记对应证书格式，信通院作为管理机构也会对证书数据进行监管。

证书模板主要指数字可信证书里的credentialSubject的格式，每一个具体的证书类型的credentialSubject由证书发行方自己定义。

#### 6.5.2.1 接口

1. [get] https://${url}/vc/?issuer=${bid}

    查询某一个指定发证方当前注册且有效的证书类型和模板列表

    返回:

    ```json
    [
        {
            "type": "证书类型",
            "version": "",
            "template_id": "",
            "create": ""
        }
    ]
    ```

2. [get] https://${url/vc/type?issuer=${issuer}&type=${type}

    查询某一个证书类型的具体信息

    返回:
    ```json
    {
        "type": "证书类型",
        "description": "具体描述",
        "issuer": "发证方地址",
        "info": [
            { "version": "版本号", "template_id": "模板id"},
        	{ "version": "..",      "template_id": "…" },
        …
        ]
    }
    ```

3. [get] https://${url}/vc/template/?id=${id}

    查询某一个指定id的证书模板，模板文件内容为一个json-ld context。

    返回:

    ```json
    {
        "create": "",
        "id": "",
        "credentialSubject": {
        }
    }
    ```

4. [post] https://${url}/vc/template/create

    发证方调用，用来创建一个模板，
    
    Post数据：

    ```json
    {
		"jws": "…"
    }
    ```

    其中jws部分为Post数据的jws token，payload部分格式为:

    ```json
    {
        "issuer":"发证方bid",
        "template_id":"模板id",
        "context":{
    
        }
    }
    ```
    
    + issuer：为发证方bid地址，JWS payload必须用该地址对应私钥签名。
    
    
    + template_id：发证方自定义，要求不能重复。
    
    
    + context：为 credentialSubject的jsonld context描述。
    
5. [post] https://${url}/vc/type/create

    发证方调用，用来创建一个证书规范。

    Post数据：

    ```json
    {
		"jws": "…"
    }
    ```

    其中jws部分为Post数据的jws token，payload部分格式为:

    ```json
	{
        "issuer":"发证方bid",
        "type":"证书类型",
        "version":"",
        "template_id":"",
        "description":""
    }
    ```
    + issuer：为发证方bid地址，JWS payload必须用该地址对应私钥签名。

    + type：证书类型，例如 "Residential" 居住证明。

    + version：版本号，同一个issuer同一个type下，version只能递增。

    + template_id：模板id，引用同一个issuer之前注册的模板。

    + description：描述信息。

### 6.5.3	可信验证方信息

独立的可信声明验证服务商需要注册登记上链，并且做验证时需要保留记录到链上。

#### 6.5.3.1 接口

1. [get] https://${url}/verifier

    当前可信验证方信息列表

    返回：

    ```json
    [
        { "id" : "验证方bid标识"
        "supported": "验证方支持的可验证声明推断类型"
        },
        …
    ]
    ```

2. [get] https://${url}/verifier/id=${verifier}

    某个可信验证方的具体信息

    返回：

    ```json
    {
	   "id": "验证方bid",
	   "register": "注册时间",
        "description": "描述信息",
        "verifications": "验证方过去的验证报告列表"
    }
    ```

### 6.5.4 证书状态信息

证书状态信息用来查询某一个具体证书的当前状态。

#### 6.5.4.1 接口

1. [get] https://${url}/vc/status?id=${id}&issuer={issuer}

    查询具体某一个证书的信息

2. [get] https://${url}/vc/revoke/id=${verifier}&issuer=${issuer}

    查询某一个证书是否被撤回。

    返回：

    ```json
    {
	   "id": "证书id",
	   "issuer": "证书发行方",
        "revoked": "是否被撤回",
        "modify_time": "被撤回的时间",
        "reason": "撤回原因"
    }
    ```

3. [post] https://${url}/vc/revoke/id=${verifier}&issuer=${issuer}

    发证方调用，用来撤回某一个证书。

    Post数据：

    ```json
    {
		"jws": "…"
    }
    ```

    其中jws部分为Post数据的jws token，payload部分格式为:

    ```json
	{
        "issuer":"发证方bid",
        "id":"证书id",
        "revocation_id":"撤回id 号",
        "reason":"撤回原因"
    }
    ```

    返回：

    ```json
    {
	    "id": "证书id",
	    "issuer": "证书发行方",
        "revoked": "是否被撤回",
        "modify_time": "被撤回的时间"
    }
    ```

### 6.5.5 <span id="jump6.5.5">可信声明推断</span>

由于隐私考虑和技术进步，可信声明中携带的证书到声明的结论有直接推断、选择性披露、零知识证明等多种推断方式。

为了协调验证方和持有方的可信推断和验证过程，在证书验证请求和可信声明里双方对支持的推断方式需要协商一致，因此对可信声明推断需要做标准化规范。

#### 6.5.5.1 直接推断方案

直接推断就是声明附带的证书直接可以推出结论。比如上高速时给出的驾驶资格声明里直接附带了驾驶证凭据。

#### 6.5.5.2 选择性披露方案

选择性披露方案，指证书发布方做数字签名时针对属性值的哈希值做签名，而非对原值签名。
为了防止哈希碰撞攻击，证书发布方需要对每个属性字段加盐。
持证方在做可信声明时，可以选择性披露证书中部分字段的原值，由验证方验证哈希值一致性，从而保护其他字段。
持证方披露部分字段时，要同时披露该字段原值和对应的盐；为了可以使验证方验证发证方签名，需要同时披露所有字段的哈希值。

**示例：选择性披露年龄**

1. 用户a收集相关资料后，向公安机关申请居民信息证书。

2. 公安机关发布证书里附带了age（年龄），gender (性别), name （年龄）。同时，公安机关发证方支持选择性披露，所以发布时的credentialSubject部分如下:

    ```json
    {
        "credentialSubject":{
            "id":"did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG",
            "name":{
                "hash":"...",
                "value":"...",
                "salt":"..."
            },
            "gender":{
                "hash":"...",
                "value":"...",
                "salt":"..."
            },
            "age":{
                "hash":"...",
                "value":"22",
                "salt":"..."
            }
        }
    }
    ```

    各个属性的salt均为发布证书时生成随机值，hash = H(value+salt)。

    在针对该证书做签名时，发证方略去了name.value/salt, gender.value/salt, age.value/salt字段。

3. 某一个场合需要用户a出示年龄证明时，验证方向a发出验证申请，里面标明验证方支持sel-disclose-SHA256选择性披露。

4. 用户a从自己持有的居民证书中复制出jws token字段，从证书原文中复制出需披露的字段原文和其他字段哈希（注意，非证书原文，因为属性value值在原文里），组装成可信声明如下:

    ```json
    {
        "verifiableCredential":[
            {
                "jws":"...",
                "proof":{
                    "composeType":"sel-disclose-SHA256",
                    "created":"2018-09-14T21:19:10Z",
                    "credentialSubject":{
                        "name":{
                            "hash":"..."
                        },
                        "gender":{
                            "hash":"..."
                        },
                        "age":{
                            "hash":"...",
                            "value":"22",
                            "salt":"..."
                        }
                    }
                }
            }
        ]
    }
    ```

5.	验证方收到可信声明后，首先通过验证证书的jws token签名，以确认发证方符合要求、声明中hash的正确性；
然后验证proof.credentialSubject里的age部分SHA256(age.value + age.salt) = age.hash
验证符合后，验证方可相信该用户age确实为22，同时无法知道用户的其他信息。

#### 6.5.5.3 可信声明推断类型枚举

| 推断类型 | 类型命名 | 示例 |
| ---- |-----|-------|
| 直接推断 | direct | direct |
| 选择性披露 | sel-disclose-%哈希算法% | sel-disclose-SHA256 |

### 6.5.6 安全考虑

选择性披露的hash方法由发证方自行选择，建议采用SHA256，另外出于防止碰撞攻击考虑，强烈建议用户在做过选择性披露后，立刻向原发证方请求刷新一份新的数字证书供后续使用。

## 6.6 可信数字证书使用实例

### 6.6.1 可信企业数字证书原文

```json
{
    "@context": ["https://www.w3.org/2018/credentials/v1"],
    "credentialSubject": {
        "bid": {
            "hash": "de036b5f7724094b864dd4403844c409117a0c9443eae9d3c759819250f9faf0",
            "salt": "4880480410620290",
            "value": "did:bid:efbqWw8mbKvtGdQEi9A268YBhTrDg9pe"
        },
        "certBid": {
            "hash": "fb2fc450aaaa6c1a030536b563aa2c02de7c2ffc18964f02d5afdbf28096a250",
            "salt": "1720811821663454",
            "value": "did:bid:efVdhV2nrZeDBLjxQrqBsMej8JC1oRUK"
        },
        "companyName": {
            "hash": "7d37d8e402f2980ab1866b6ca2f5667d1f05c1939f5b7480c0528cb1e871e030",
            "salt": "2706047974179116",
            "value": "**区块链技术有限公司"
        },
        "id": "did:bid:efbqWw8mbKvtGdQEi9A268YBhTrDg9pe"
    },
    "id": "did:bid:efVdhV2nrZeDBLjxQrqBsMej8JC1oRUK",
    "issuanceDate": "2022-04-09T06:36:30Z",
    "issuer": "did:bid:ef14uPsX7XYLzsU4t2rnRrsK2zfUbFEj",
    "parseType": "sel-disclose-SM2",
    "revocationId": "did:bid:efmcmSddaQjhTY3kCiDDNzFTb2KWKj3P",
    "templateId": "did:bid:efvBPiZWCuB89LpynLUpmXCozjGEouhU",
    "type": ["VerifiableCredential", "identityCertification"],
    "validBefore": "2099-01-01 00:00:00"
}
```

### 6.6.2 可信验证声明原文

```json
{
    "@context": ["https://www.w3.org/2018/credentials/v1"],
    "credentialParse": [{
        "format": "jws",
        "parseType": "sel-disclose-SM2"
    }],
    "credentialRequest": {
        "jws": "eyJhbGciOiJTTTIifQ.eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJpZCI6IjQwN2YxMGQ1LWI5YjMtNGEyYS04MTE5LWFmMTE1Njk3NTdhYyIsInJlcXVpcmVtZW50cyI6eyJjcmVkZW50aWFscyI6W1t7ImNvbnRlbnQiOiJ7XCJjZXJ0QmlkXCI6XCJkaWQ6YmlkOmVmWHlLRFBhbnZqNEtmdzZZYmF4eGpSa3pyS3dRSjhmXCJ9IiwiaXNzdWVyIjoiZGlkOmJpZDplZjE0dVBzWDdYWUx6c1U0dDJyblJyc0syemZVYkZFaiIsIm1heFZlcnNpb24iOiIxLjAuMCIsIm1pblZlcnNpb24iOiIxLjAuMCIsInN1YmplY3QiOiJkaWQ6YmlkOmVmYnFXdzhtYkt2dEdkUUVpOUEyNjhZQmhUckRnOXBlIiwidHlwZSI6ImlkZW50aXR5Q2VydGlmaWNhdGlvbiIsInZhbGlkQmVmb3JlIjoiMjA5OS0wMS0wMSAwMDowMDowMCJ9XV0sInByZXNlbnRhdGlvbiI6eyJjb21wb3NlVHlwZSI6WyJzZWwtZGlzY2xvc2UtU00yIl19fSwic2VuZGVyIjoiZGlkOmJpZDplZnV6d2lUdDJieU55V3dYb3Zkd1NucFUzcmprVEpBeCIsInR5cGUiOlsiY3JlZGVudGlhbFJlcXVlc3QiXSwidmVyaWZpZXIiOiJkaWQ6YmlkOmVmdXp3aVR0MmJ5TnlXd1hvdmR3U25wVTNyamtUSkF4In0.K5OaOnvkY3adoVGX7uuaJIQKN04rW0SWR8sUHq7Eqvuey02DVYeLEAl_6OfsRHOycLv1LdXyAHof7tsHxIrBDA"
    },
    "type": "VerifiablePresentation",
    "verifiableCredential": [
        {
            "jws": "eyJhbGciOiJTTTIifQ.eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJjcmVkZW50aWFsU3ViamVjdCI6eyJiaWQiOnsiaGFzaCI6ImIyMmI2NzBkZDk1YmY1ODZiYzE1YzI1ZTY0NTgzZjA5ZmRhZDZhOGZhNjUyZWNjNjU3NTk3ZTZmNTY5OGNkNmQifSwiY2VydEJpZCI6eyJoYXNoIjoiMGNjNjEyOWM3YWE4Y2Q1NWFlOTU1ZWI0OTNhNWY5MGYwZDdmNmIzYWYyMzk0ZGIxN2VkMjU0OGMwMzBjOGMxMSIsInNhbHQiOiIxNTAwMzEzODk2NDIyOTgwIiwidmFsdWUiOiJkaWQ6YmlkOmVmTXJvN2lBMVROYTh0TXdINEUzenlYeHk2dTNBbUdEIn0sImlkIjoiZGlkOmJpZDplZmJxV3c4bWJLdnRHZFFFaTlBMjY4WUJoVHJEZzlwZSJ9LCJpZCI6ImRpZDpiaWQ6ZWZNcm83aUExVE5hOHRNd0g0RTN6eVh4eTZ1M0FtR0QiLCJpc3N1YW5jZURhdGUiOiIyMDIyLTA0LTAyVDA5OjUxOjM4WiIsImlzc3VlciI6ImRpZDpiaWQ6ZWYxOEY5QVZLNFNRTFpQUnJQa3JWV3dwOWticGRYSHgiLCJwYXJzZVR5cGUiOiJzZWwtZGlzY2xvc2UtU00yIiwicmV2b2NhdGlvbklkIjoiIiwidGVtcGxhdGVJZCI6ImRpZDpiaWQ6ZWYyQU5IOHpyVk04S28xSEFqVjZObmNhZGdDRUxCOU4xIiwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsImlkZW50aXR5Q2VydGlmaWNhdGlvbiJdLCJ2YWxpZEJlZm9yZSI6IjIwOTktMDEtMDEgMDA6MDA6MDAifQ.MTIz",
            "proof": {
                "composeType": "sel-disclose-SM2",
                "created":"2022-04-09T06:36:30Z",
                "credentialSubject":{
                    "bid":{
                        "hash":"de036b5f7724094b864dd4403844c409117a0c9443eae9d3c759819250f9faf0"
                    },
                    "certBid":{
                        "hash":"fb2fc450aaaa6c1a030536b563aa2c02de7c2ffc18964f02d5afdbf28096a250",
                        "value":"did:bid:efVdhV2nrZeDBLjxQrqBsMej8JC1oRUK",
                        "salt":"1720811821663454"
                    },
                    "companyName":{
                        "hash":"7d37d8e402f2980ab1866b6ca2f5667d1f05c1939f5b7480c0528cb1e871e030"
                    }
                }
            }
        }
    ]
}
```



# 7. 缺陷

暂无