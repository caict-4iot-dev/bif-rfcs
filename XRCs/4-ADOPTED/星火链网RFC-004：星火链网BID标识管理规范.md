# 星火链网RFC-004：星火链网BID标识管理规范

# 1. 序言

编号：XRC-004

类型：XRC

标题：星火链网BID标识管理规范

作者：刘侃，liukan@caict.ac.cn

发布时间：2021-12-09

状态：通过

更新时间：2021-12-21

讨论地址：https://github.com/CAICT-DEV/BIF-RFCs/issues/4

依赖XRC：[星火链网RFC-002：星火链网分布式标识BID](星火链网RFC-002：星火链网分布式标识BID.md)

# 2. 摘要

本文介绍星火链网上BID分布式数字身份的管理协议规范和实现。

# 3. 动机

BID管理协议的主要目标是设计一种基于分布式标识符DID规范的、适用于星火·链网分层架构的、可以实现寻址功能和数字身份功能的、工业互联网和物联网场景下的通用性标识管理协议。

# 4. 原理

本章主要介绍下星火标识BID(Blockchain-based Identifier)的概念和技术原理。

## 4.1 BID简介

星火标识BID（Blockchain-based Identifier，简称BID）,是星火·链网的数据载体，也是星火链底层支持的原生地址，同时BID还是加入到分布式身份标识符DID注册表的一个METHOD。

BID的组成结构参见[星火链网RFC-002：星火链网分布式标识BID](星火链网RFC-002：星火链网分布式标识BID.md#bid)中的BID规范。

did:bid:byo1(AC号) 这样的BID是一类特殊的BID, 存放子链解析服务，只有前三个部分，不包含后缀。对应的BID文档里存放子链解析地址。

BID标识的ABNF定义如下：

```
bid-did = "did:bid:" bid-specific-identifier ; 固定的did:bid前缀

bid-specific-identifier = 0*1(acsn ":") suffix / acsn ":" 0*1(suffix) ; acsn(可选):后缀 或者 acsn:后缀(可选)

acsn = 4(ALPHA / DIGIT); 4个字母或数字组合

suffix = (22,42)(ALPHA / DIGIT);长度范围22-42的字母或数字组合
```

## 4.2 架构

<span id="bid">BID的架构如下：</span>

![image](https://user-images.githubusercontent.com/1982712/146703973-7093a9ff-e39f-4c10-97f6-91ca3b3e8d8c.png)

BID标识依托于星火链主子链架构，是一个层次化的模型，由主链和子链组成。同一私钥在主链和子链上使用相同的数字身份,只是在子链上的BID多了AC号。

![image](https://user-images.githubusercontent.com/1982712/146704043-c3044c31-c5bd-483e-b07c-660a994448c7.png)

主链上主要存储BID在主链的数字身份信息、在主链的基本属性信息、到子链的寻址信息。子链存储在子链的数字身份信息和在子链的基本属性，如与子链所处行业相关的信息、具体标识的设备信息等。

# 5. 规范

## <span id="meta">5.1 协议元数据</span>

BID的协议元数据为BID文档。BID文档遵循DID Document规范，并在之基础上做了一定的扩展。BID文档字段说明如下：

* @context：必填字段。一组解释JSON-LD文档的规则,遵循DID规范，用于实现不同DID Document的互操作，必须包含 https://www.w3.org/ns/did/v1。
* version：必填字段。文档的版本号，用于文档的版本升级。
* id：必填字段。文档的BID。
* publicKey：选填字段。一组公钥，包含id，type，controller,publicKeyHex四个字段，凭证类的BID文档不包含该字段。

    * id:公钥的ID。
    * type：字符串，代表公钥的加密算法类型，具体支持的类型见附录公钥类型章节。
    * controller：一个BID,表明此公钥的归属。
    * publicKeyHex：公钥的十六进制编码。

* authentication：必填字段。一组公钥的BID，表名此BID的归属，拥有此公钥对应私钥的一方可以控制和管理此BID文档。

* alsoKnownAs。选填字段。一组和本BID关联的其他ID, 包括type和id两个字段。

    * type:关联标识的类型,取值详见附录关联标识类型。
    * id:关联的标识。

* extension：BID扩展字段。包含如下字段：

    * recovery：选填字段。一组公钥id, 在authentication 私钥泄漏或者丢失的情况下用来恢复对文档的控制权。

    * ttl: 缺省值为0。Time-To-Live，即如果解析使用缓存的话缓存生效的时间，单位秒。

    * delegateSign: 选填字段。第三方对publicKey的签名，可信解析使用。包括：signer和signatureValue。

        * signer ：签名者，这里是一个公钥的id。
        * signatureValue: 使用相应私钥对publicKey字段的签名。

    * type: 必填字段。BID文档的属性类型，取值见附录属性类型。

    * attributes: 必填字段。一组属性，根据文档属性类型不同attributes有不同的字段。
        * 当type为凭证类型时，属性为可验证声明，结构如下：

            | 字段名 | 描述 |
            |----| ----|
            | issuer | 必填。发证者BID |
            | issuanceDate | 必填。发证日期 |
            | effectiveDate |	必填。生效日期 |
            | expirationDate |	必填。失效日期 |
            | revocationId | 必填。凭证吊销服务地址ID |
            | templateId |	必填。凭证模板ID |
            | credentialSubject | 对象，字段见下文 |
            | proof |  对象，字段见下文 |
            
            credentialSubject 对象:
            
            | 字段名 | 描述 |
            |----| ----|
            | 必填。id | 凭证拥有者的BID |
            | 必填。type | 凭证类型(数字)。详见附录凭证类型 | 
            | 选填。name | 被颁发者机构名称 |
            | 选填。description | 描述 |
            | 选填。content | 凭证的具体内容，根据模板进行解析 |

            proof 对象：

            | 字段名 | 描述 |
            |----| ----|
            | 选填。creator | proof的创建者，这里是一个公钥的id。 |
            | 选填。signatureValue | 使用相应私钥对attribute内容的签名。 |

        * 当type为其他属性类型时，属性为如下结构：

            | 字段名 | 描述 |
            |----| ----|
            | key | 属性的关键字 | 
            | desc | 选填。属性描述 |
            | encrypt | 选填。是否加密，0非加密，1加密 |
            | format | 选填。image、text、video、mixture等数据类型 |
            | value | 选填。属性自定义value |

    * acsns:选填字段。一组子链AC号，只有BID文档类型不是凭证类型且文档是主链上的BID文档才可能有该字段，存放当前BID拥有的所有AC号。

    * verifiableCredentials:选填字段。凭证列表，包含id和type两个字段。只有BID文档类型不是凭证类型才可能有该字段。
        * id:可验证声明的BID。
        * type：凭证类型。详见附录凭证类型。

    * service：选填字段。一组服务地址，包括id，type，serviceEndpoint三个必填字段。
        * id: 服务地址的ID。
        * type：字符串，代表服务的类型。取值见附录服务类型。
        * serviceEndpoint：一个URI地址。

            当type为子链解析服务时， service为以下结构：

            | 字段名 | 描述 |
            | ----- | ---- |
            | id | 服务地址的ID |
            | type | DIDSubResolver | 
            | version | 服务支持的BID解析协议版本 | 
            | protocol | 解析协议支持的传输协议类型, 具体取值见附录解析服务协议类型 | 
            | serverType | 服务地址类型，0为域名形式，1为IP地址形式 |
            | serviceEndpoint | 解析地址的IP或域名 |
            |port | serverType为1时有该字段，解析服务的端口号 |

    * created：必填字段。创建时间。
    * updated：必填字段。上次的更新时间。
    * proof：选填字段。文档所有者对文档内容的签名，包括：creator和signatureValue。
        * creator：proof的创建者，这里是一个公钥的id。
        * signatureValue：使用相应私钥对除proof字段的整个BID文档签名。

## 5.2 元数据示例

```json
{
	"@context": ["https://www.w3.org/ns/did/v1"],
	"version": "1.0.0",
	"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
	"publicKey": [{
		"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1",
		"type": "Ed25519",
		"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
		"publicKeyHex": "b9906e1b50e81501369cc777979f8bcf27bd1917d794fa6d5e320b1ccc4f48bb"
	}, {
		"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2",
		"type": "Ed25519",
		"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
		"publicKeyHex": "31c7fc771eba5b511b7231e9b291835dd4ebde51cc0e757a84464e7582aba652"
	}],
	"authentication": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1"],
	"extension": {
		"recovery": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2"],
		"ttl": 86400,
		"delegateSign ": {
			"signer": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG#key-1",
			"signatureValue": "eyJhbGciOiJSUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19"
		},
		"type": 206
	},
	"service": [{
		"id": "did:bid:ef24NBA7au48UTZrUNRHj2p3bnRzF3YCH#subResolve",
		"type": "DIDSubResolve",
		"version": "1.0.0",
		"serverType": 1,
		"protocol": 3,
		"serviceEndpoint": "192.168.1.23",
		"port": 8080
	}],
	"created": "2021-05-10T06:23:38Z",
	"updated": "2021-05-10T06:23:38Z",
"proof": {
		"creator": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG#key-1",
		"signatureValue": "9E07CD62FE6CE0A843497EBD045C0AE9FD6E1845414D0ED251622C66D9CC927CC21DB9C09DFF628DC042FCBB7D8B2B4901E7DA9774C20065202B76D4B1C15900"
	}
}
```

# 6. 实现

本章主要描述BID管理协议的具体实现。

## 6.1 管理协议

BID管理协议基于HTTP/HTTPS通信协议，使用POST方法，参数使用JSON编码。

### 6.1.1 注册

1. 接口说明

	注册接口主要完成BID文档的注册。创建BID文档时proof字段的签名者需要是authentication字段里的公钥才有权限创建成功。

2.	请求参数

	| 参数 | 字段类型 | 说明 |
	| ---- | ------- | ---- |
	| id | String | 要创建的BID |
	| operation | String | “create” |
	| didDocument | Object | 要创建的BID文档 |

3.	请求示例

	```json
	{
		"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
		"operation": "create",
		"didDocument": {
			"@context": ["https://www.w3.org/ns/did/v1"],
			"version": "1.0.0",
			"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
			"publicKey": [{
				"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1",
				"type": "Ed25519",
				"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
				"publicKeyHex": "b9906e1b50e81501369cc777979f8bcf27bd1917d794fa6d5e320b1ccc4f48bb"
			}, {
				"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2",
				"type": "Ed25519",
				"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
				"publicKeyHex": "31c7fc771eba5b511b7231e9b291835dd4ebde51cc0e757a84464e7582aba652"
			}],
			"authentication": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1"],
			"extension": {
				"recovery": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2"],
				"ttl": 86400,
				"delegateSign ": {
					"signer": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG#key-1",
					"signatureValue": "eyJhbGciOiJSUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19"
				},
				"type": 206
			},
			"service": [{
				"id": "did:bid:ef24NBA7au48UTZrUNRHj2p3bnRzF3YCH#subResolve",
				"type": "DIDSubResolve",
				"version": "1.0.0",
				"serverType": 1,
				"protocol": 3,
				"serviceEndpoint": "192.168.1.23",
				"port": 8080
			}],
			"created": "2021-05-10T06:23:38Z",
			"updated": "2021-05-10T06:23:38Z",
			"proof": {
				"creator": " did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1",
				"signatureValue": "9E07CD62FE6CE0A843497EBD045C0AE9FD6E1845414D0ED251622C66D9CC927CC21DB9C09DFF628DC042FCBB7D8B2B4901E7DA9774C20065202B76D4B1C15900"
			}
		}
	}
	```

4.	返回参数

	| 参数 | 字段类型 | 说明 |
	|----- | ------- | ---- |
	| errorCode | Long | 返回码，成功为0，失败见响应码说明章节 |
	| message | String | 具体返回信息 |

5.	返回示例

	```json
	{
		"errorCode": 1,
		"message": "without permission"
	}
	```

### 6.1.2 BID更新

1.	接口说明

	更新接口主要完成BID文档的更新，更新操作不允许更新authentication字段。更新BID文档时proof字段的签名者需要是authentication字段里的公钥才有权限更新成功。

2.	请求参数

	| 参数 | 字段类型 | 说明 |
	| ---- | ------- | ---- |
	| id | String | 要创建的BID |
	| operation | String | “update” |
	| didDocument | Object | 更新后的BID文档 |

3.	请求示例

	```json
	{
		"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
		"operation": "update",
		"didDocument": {
			"@context": ["https://www.w3.org/ns/did/v1"],
			"version": "1.0.0",
			"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
			"publicKey": [{
				"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1",
				"type": "Ed25519",
				"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
				"publicKeyHex": "b9906e1b50e81501369cc777979f8bcf27bd1917d794fa6d5e320b1ccc4f48bb"
			}, {
				"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2",
				"type": "Ed25519",
				"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
				"publicKeyHex": "31c7fc771eba5b511b7231e9b291835dd4ebde51cc0e757a84464e7582aba652"
			}],
			"authentication": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1"],
			"extension": {
				"recovery": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2"],
				"ttl": 86400,
				"delegateSign ": {
					"signer": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG#key-1",
					"signatureValue": "eyJhbGciOiJSUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19"
				},
				"type": 206
			},
			"service": [{
				"id": "did:bid:ef24NBA7au48UTZrUNRHj2p3bnRzF3YCH#subResolve",
				"type": "DIDSubResolve",
				"version": "1.0.0",
				"serverType": 1,
				"protocol": 3,
				"serviceEndpoint": "192.168.1.23",
				"port": 8080
			}],
			"created": "2021-05-10T06:23:38Z",
			"updated": "2021-05-10T06:23:38Z",
			"proof": {
				"creator": " did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1",
				"signatureValue": "9E07CD62FE6CE0A843497EBD045C0AE9FD6E1845414D0ED251622C66D9CC927CC21DB9C09DFF628DC042FCBB7D8B2B4901E7DA9774C20065202B76D4B1C15900"
			}
		}
	}
	```

4. 返回参数

	| 参数 | 字段类型 | 说明 |
	| ---- | ------- | ---- |
	| errorCode | Long | 返回码，成功为0，失败见响应码说明章节 |
	| message | String | 具体返回信息 |

5.	返回示例

	```json
	{
		"errorCode": 0,
		"message": "success"
	}
	```

### 6.1.3 BID停用

1.	接口说明

	停用接口主要完成对BID文档撤销，撤销后的BID文档更新为空而不是删除。停用BID文档时proof字段的签名者需要是recovery字段里的公钥才有权限停用成功。

2.	请求参数

	| 参数 | 字段类型 | 说明 |
	| ---- | ------- | ---- |
	| id   | String | 要创建的BID |
	| operation | String | “delete” |
	| proof | Object | recovery字段里的公钥对请求体的签名 |

3.	请求示例

	```json
	{
		"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
		"opratiton": "delete",
		"proof": {
			"creator": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-1",
			"signatureValue": "9E07CD62FE6CE0A843497EBD045C0AE9FD6E1845414D0ED251622C66D9CC927CC21DB9C09DFF628DC042FCBB7D8B2B4901E7DA9774C20065202B76D4B1C15900"
		}
	}
	```

4.	返回参数

	| 参数 | 字段类型 | 说明 |
	| ---- | ------- | ---- |
	| errorCode | Long | 返回码，成功为0，失败见响应码说明章节 |
	| message | String | 具体返回信息 |

5.	返回示例

	```json
	{
		"errorCode": 0,
		"message": "success"
	}
	```

### 6.1.4 BID密钥更换

1. 接口说明

	密钥更换接口主要完成对BID文档里的authentication和publicKey字段里内容的修改。更换密钥时proof字段的签名者需要是recovery字段里的公钥才有权限更换成功。

2.	请求参数

	| 参数 | 字段类型 | 说明 |
	| ---- | ------ | ----- |
	| id | String | 要创建的BID |
	| operation | String | “recovery” |
	| didDocument | Object | 更新后的BID文档 |

3.	请求示例

	```json
	{
		"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
		"operation": "update",
		"didDocument": {
			"@context": ["https://www.w3.org/ns/did/v1"],
			"version": "1.0.0",
			"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
			"publicKey": [{
				"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-3",
				"type": "Ed25519",
				"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
				"publicKeyHex": "b9906e1b50e81501369cc777979f8bcf27bd1917d794fa6d5e320b1ccc4f48bb"
			}, {
				"id": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2",
				"type": "Ed25519",
				"controller": "did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2",
				"publicKeyHex": "31c7fc771eba5b511b7231e9b291835dd4ebde51cc0e757a84464e7582aba652"
			}],
			"authentication": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-3"],
			"extension": {
				"recovery": ["did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2"],
				"ttl": 86400,
				"delegateSign ": {
					"signer": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG#key-1",
					"signatureValue": "eyJhbGciOiJSUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19"
				},
				"type": 206
			},
			"service": [{
				"id": "did:bid:ef24NBA7au48UTZrUNRHj2p3bnRzF3YCH#subResolve",
				"type": "DIDSubResolve",
				"version": "1.0.0",
				"serverType": 1,
				"protocol": 3,
				"serviceEndpoint": "192.168.1.23",
				"port": 8080
			}],
			"created": "2021-05-10T06:23:38Z",
			"updated": "2021-05-10T06:23:38Z",
			"proof": {
				"creator": " did:bid:efnVUgqQFfYeu97ABf6sGm3WFtVXHZB2#key-2",
				"signatureValue": "9E07CD62FE6CE0A843497EBD045C0AE9FD6E1845414D0ED251622C66D9CC927CC21DB9C09DFF628DC042FCBB7D8B2B4901E7DA9774C20065202B76D4B1C15900"
			}
		}
	}
	```

4. 返回参数

	| 参数 | 字段类型 | 说明 |
	| ---- | ------- | ---- |
	| errorCode | Long | 返回码，成功为0，失败见响应码说明章节 |
	| message | String | 具体返回信息 |

5.	返回示例

	```json
	{
		"errorCode": 0,
		"message": "success"
	}
	```

### 6.1.5 响应码说明

接口返回响应码的标准定义。


| 返回码 | 返回码解释 |
| ----- | --------- |
| 0 (success) |	成功 |
| 1 (without permission) | 无权限 |
| 2 (error) | 发生错误 |
| 3 (server too busy) | 服务器繁忙 |
| 4 (protocol error) | 协议错误 |
| 5 (operation not supported) | 不支持的操作 |
| 6 (not found) | 没有找到标识 |
| 7 (server not response) | 服务器没有响应 |
| 8 (field not found) | 不存在的字段 |
| 9 (verify failed)	| 签名验证失败 |
| 10 (nonexistent chain code info) | 不存在AC号信息 |

### 6.1.6 签名算法

1. 约束条件

    要序列化的BID文档(尤其是extension字段)必须适用于JSON[RFC7493]格式，这意味着以下内容：

    • JSON 对象不得显示重复的属性名称。

    • JSON 字符串数据必须可以表示为Unicode[UNICODE]。

    • JSON 数字数据必须可以表示为IEEE 754 [IEEE754]双精度值。对于需要比IEEE 754双精度提供更高精度或更长整数的应用程序，建议将此类数字表示为JSON字符串。

2. 签名规则

    * 基本数据类型处理

    	字面量、字符串和数字序列化特殊处理，需要满足JCS[RFC8785]的要求。

    * 排序

    	需要排序的对象数据依据属性key值转换为UTF-16的字节单元，字节单元的首字节进行简单的值比较来进行升序排序。如果首字节相同，则继续对后面的字节进行比对，直到产生不同大小的值来进行升序排列。

    * 递归
    
    	对所有的子对象数据也需要进行递归排序。

    * 数组
    
    	要求数组元素顺决不能被改变。

    * 数据压缩

    	去掉JSON符号间的空白，紧凑化处理。

    * 签名

    	对排序压缩后的字节数据使用签名算法进行签名。

    * Base58编码

    	签名数据进行Base58编码生成signatureValue字符串。

3. 签名步骤

    1. 将需要签名的凭证或者BID文档数据，去掉proof数据后按照规则进行排序。

    2. 压缩处理排序后的数据形成待签名字节数据(UTF-8字符集)。

    3. 使用私钥对待签名字节数据进行签名。

    4. 对签名数据进行Base58编码，生成signatureValue数据。

4. 示例

    1. 凭证签名

    	以下是一个凭证类型的签名示例：

    * 源数据：

		```json
		{
			"issuer": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG",
			"issuanceDate": "2021-01-20T12:01:20Z",
			"effectiveDate": "2021-01-20T12:01:20Z",
			"expirationDate": "2021-04-02T12:01:20Z",
			"revocationId": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG#revocation",
			"credentialSubject": {
				"id": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG",
				"type": 202,
				"name": "asc",
				"content": {
					"registerCapital": "1000.0",
					"status": "2"
				}
			},
			"proof": [{
				"creator": "did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx#key-1",
				"signatureValue": "4TWzvxXDgejyWK7syUeg68WFd6Kf5cGV8bnEYR35UaKX18VRwemnnBuuGkMHGrSP2qbDac9WwhTffLQhyzz2Vp5m"
			}]
		}
		```

    * 排序

	  ```json
	  {
	      "credentialSubject":{
	          "content":{
                  "registerCapital":"1000.0","status":"2"
	          },
              "id":"did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG",
	          "name":"asc",
	          "type":202
	      },
	      "effectiveDate":"2021-01-20T12:01:20Z",
	      "expirationDate":"2021-04-02T12:01:20Z",
          "issuanceDate":"2021-01-20T12:01:20Z",
	      "issuer":"did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG",
          "revocationId":"did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG#revocation"
	  }
      ```

	* 签名
	
	  	使用测试私钥priSPKp8oiiAXGZaXFBMKEAoL2b6J6UDQCw4x39ereXYtyAejM，运用Ed25519算法计算待签名字节数据(排序后的数据转为字节)并Base58编码。
	
		```
		4TWzvxXDgejyWK7syUeg68WFd6Kf5cGV8bnEYR35UaKX18VRwemnnBuuGkMHGrSP2qbDac9WwhTffLQhyzz2Vp5m
		```
	
	2.	BID文档签名
	
		BID文档类型的签名示例
	
	* 源数据：
	
	```json
	{
		"@context": ["https://w3.org/ns/did/v1"],
		"id": "did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx",
		"created": "2021-06-24T09:10:09Z",
		"updated": "2021-06-24T09:10:09Z",
		"extension": {
			"recovery": ["did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx#key-1"],
			"attributes": [{
				"key": "contract",
				"encrypt": 1,
				"format": "text",
				"desc": "contract address",
				"value": "did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG"
			}],
			"ttl": 86400,
			"type": 105
		},
		"authentication": ["did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx#key-1"],
    	"publicKey": [{
			"controller": "did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx",
			"id": "did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx#key-1",
			"publicKeyHex": "b06566f76733ae048fda721d47afe8780b572636496c93253db86dc8d5427fc54e9a06",
			"type": "Ed25519"
		}],
    	"proof": {
			"creator": "did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx #key-1",
    		"signatureValue": " 5jFX6UKMVTg73LCWamNdeZACCMftMjSrJvZpL86ULefr3216SKRfgH6YkrmHT5DACYSpVEeN9RcnNES8cAHBVsMw"
    	},
		"version": "1.1.0"
	}
	```
	
	* 排序
	
	```json
	{
	    "@context":["https://w3.org/ns/did/v1"],
	    "authentication":["did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx#key-1"],
	    "created":"2021-06-24T09:10:09Z",
	    "extension":{
	        "attributes":[{
	            "desc":"contract address",
	            "encrypt":1,
	            "format":"text",
	            "key":"contract",
	            "value":"did:bid:efJgt44mNDewKK1VEN454R17cjso3mSG"
	        }],
	        "recovery":["did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx#key-1"],
	        "ttl":86400,
	        "type":105
	    },
	    "id":"did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx",
	    "publicKey":[{
	        "controller":"did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx",
	        "id":"did:bid:ef18F9AVK4SQLZPRrPkrVWwp9kbpdXHx#key-1",
	        "publicKeyHex":"b06566f76733ae048fda721d47afe8780b572636496c93253db86dc8d5427fc54e9a06",
	        "type":"Ed25519"
	    }],
	    "updated":"2021-06-24T09:10:09Z",
	    "version":"1.1.0"
	}
	```
	
	* 签名
	
		使用测试私钥
		priSPKp8oiiAXGZaXFBMKEAoL2b6J6UDQCw4x39ereXYtyAejM，运用Ed2519算法计算待签名字节数据(排序后的数据转为字节)并Base58编码。
	
	```
	5jFX6UKMVTg73LCWamNdeZACCMftMjSrJvZpL86ULefr3216SKRfgH6YkrmHT5DACYSpVEeN9RcnNES8cAHBVsMw
	```

### 6.1.7 类型码表

#### 6.1.7.1 关联标识类型

| 类型码 | 描述 |
| ----- | -----|
|101 |	BID |
|102 |	其他DID |
|103 |	域名 |
|104 |	UID |
|105 |	HANDLE |
|…	 | 待扩展 |

#### 6.1.7.2 属性类型

| 类型码 | 描述 |
| ------ | ----|
|101 | 人 |
|102 | 企业 |
|103 | 节点 |
|104 | 智能设备 |
|105 | 智能合约 |
|201 | 图片 |
|202 | 视频 |
|203 | 文档 |
|204 | 资源数据 |
|205 | 凭证 |
|206 | AC号 |
|999 | 其他 |

#### 6.1.7.3 凭证类型

| 取值 | 类型 |
| ---- | ---- |
| 201 | 可信认证 |
| 202 | 学历认证 |
| 203 | 资质认证 |
| 204 | 授权认证 |
| …	 | 待扩展 |

#### 6.1.7.4 服务类型

| 类型 | 描述 |
| ---- | ----|
| DIDDecrypt | 加解密服务 |
| DIDStorage | 存储服务 |
| DIDRevocation | 凭证吊销服务 |
| DIDResolver | 解析服务 |
| DIDSubResolver | 子链解析服务 |
| … | 待扩展 |

#### 6.1.7.5 解析服务协议类型

| 取值 | 对应的协议 |
| ---- | --------- |
| 0	| UDP |
|1	| TCP | 
|2	| HTTP |
|3	| HTTPS |

#### 6.1.7.6 公钥算法类型

| 类型 | 描述 |
| ---- | ----|
|SM2 | Sm2国密算法，参数id采用默认值1234567812345678 |
| ED25519 | 扭曲爱德华曲线ED25519算法 |
| …	| 待扩展 |

# 7. 缺陷

暂无