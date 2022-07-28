# 星火链网RFC-010：星火链网DNA协议标准

## 1.序言

编号：RFC-010

类型：标准

标题：星火链网DNA协议标准

作者：金健，[jinjian1@caict.ac.cn](mailto:jinjian1@caict.ac.cn)；谢家贵，[xiejiagui@caict.ac.cn](mailto:xiejiagui@caict.ac.cn)；李志平，[lizhiping@caict.ac.cn](mailto:lizhiping@caict.ac.cn)；郭世杰，[guoshijie@caict.ac.cn](mailto:guoshijie@caict.ac.cn)；聂凡杰[niefanjie@caict.ac.cn](mailto:niefanjie@caict.ac.cn)

发布时间：2022-04-29

状态：演示

更新时间：2022-04-29

讨论地址：https://github.com/CAICT-DEV/BIF-RFCs/issues/11

依赖RFC：无

## 2.摘要

本文介绍星火链网数字资产的协议标准和规范流程。

## 3.动机

目前星火应用生态处于起步阶段，随着业内区块链应用生态的发展，数字藏品发展迅速。为了将诸类应用嫁接到星火链网，需要在星火链网上实现诸类应用的智能合约。为了保证资产创建后的规范性和通用性，参照非常成熟的以太坊ERC721，我们为开发人员提供星火链网的数字资产协议标准。

## 4.标准设计

### 4.1 总体设计

在以太坊ERC721基础上，DNA（digital native assets，数字原生资产）根据星火链网的应用场景进行以下改进：

- 钱包地址和合约地址为BID

  DNA协议中用于接收、发送NFT的钱包地址和合约地址为BID，与星火链网钱包相互兼容。最终实现用户注册一次钱包，连接星火链网主链所有应用。

- 监管功能

  监管方作为去中心化的第三方对NFT的合法合规进行监管，监管规则写入“监管合约”中，监管过程全部公平透明。违反监管的NFT资产将被“冻结”，“冻结”后不能进行转让。但是监管方无权“销毁”或“转让”用户的NFT资产。

- 每个NFT生成一个BID身份

  每个NFT都会生成一个BID身份，用户可以通过解析主链BID，获取相关信息，如创建NFT的合约地址、NFT名称、tokenId、tokenURI等。

- metadata格式

  DNA协议中规范了元数据（metadata）的格式内容，避免不同应用方展示NFT的信息不一致而混乱。

### 4.2 协议标准

DNA协议中定义了如下接口，为了保持合约的规范性，用户在使用的过程中，必须实现全部接口，但允许实现内容为空。

下面对该协议的具体接口进行介绍，该协议支持solidity、js等多种编程语言的实现。

文中出现的BID地址，指的是did:bid地址类型。关于did:bid地址，请参考[星火链网RFC-002：星火链网分布式标识BID](https://github.com/caict-4iot-dev/bif-rfcs/blob/main/RFCs/4-ADOPTED/星火链网RFC-002：星火链网分布式标识BID.md)

DNA165是实现DNA721协议的一部分，IDNA165实现了检查合约接口定义的功能。如下：

```
interface IDNA165 {
  // 接口
}
```

#### supportsInterface()

**功能说明**

检查其他合约对接口的支持情况，如果合约实现了`interfaceId`代表的接口，则返回true。

**接口描述**

- 接口名

```
supportsInterface(string interfaceId) 
```

- 参数说明

| 参数名称    | 类型   | 必要性 | 参数描述                                            |
| ----------- | ------ | ------ | --------------------------------------------------- |
| interfaceId | string | 是     | 参数类型以solidity语言为例，interfaceId代表某个接口 |

   * 返回值

| 参数名称 | 类型 | 参数描述       |
| -------- | ---- | -------------- |
| result   | bool | 是否实现了接口 |

- 事件

​       无

以下是 DNA721协议的事件以及接口规范：

```
interface IDNA721 is IDNA165 {
  // 事件
  // 接口
}
```


#### 事件

DNA协议中有5个事件，分别是：

1）转移DNA时：

`event Transfer(string from, string to, string tokenId);`

当`tokenId` 所指的DNA从 `from` 转移到 `to` 时触发此事件，或者当一个DNA被创建出来的时候，触发此事件。

2）授权DNA时：
`event Approval(string owner, string approved, string tokenId);`

当 `owner` 使用 `approved()` 接口将 `tokenId` 所指的DNA授权给`approved`时，触发此事件。

3）授权所有DNA时：

`event ApprovalForAll(string owner, string operator, bool approved);`

当 `owner` 使用 `setApprovalForAll()` 接口将自己所有的DNA授权给`operator`时，触发此事件。

4）建立tokenId和BID的映射关系时：
`event bindDNA(string tokenId，string bid);`

当建立起`tokenId`和`BID`的映射关系时，触发此事件。

5）设置DNA的可用状态时：

`event setState(string tokenId，string state);`

当设置DNA的可用状态时，触发此事件。



接口如下：

#### create()

**功能说明**

创建一个DNA。

**接口描述**

- 接口名

```
create(string to)
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述               |
| -------- | ------ | ------ | ---------------------- |
| to       | string | 是     | DNA的接收者，用BID表示 |

   * 返回值

     无

- 事件

​       触发{Transfer} 事件。



#### balanceOf()

**功能说明**

查询用户所持有的DNA数量。

**接口描述**

- 接口名

```
balanceOf(string owner)
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述               |
| -------- | ------ | ------ | ---------------------- |
| owner    | string | 是     | DNA的所有人，用BID表示 |

   * 返回值

| 参数名称 | 类型   | 参数描述 |
| -------- | ------ | -------- |
| balance  | string | 余额数量 |

- 事件

 无



#### ownerOf()

**功能说明**

查询tokenId所指的DNA的所有者。

**接口描述**

- 接口名

```
ownerOf(string tokenId)
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述              |
| -------- | ------ | ------ | --------------------- |
| tokenId  | string | 是     | 某一个DNA的唯一标识符 |

   * 返回值

| 参数名称 | 类型   | 参数描述                 |
| -------- | ------ | ------------------------ |
| owner    | string | tokenId所指的DNA的所有者 |

- 事件

   无



#### approve()

**功能说明**

授权操作，A将自己的某一个DNA授权给其他账户B，B获得权限：B可以对该DNA进行转移操作。

一次只能授权一个帐户。授权另一个账户时，会取消对上一个账户的授权。

**接口描述**

- 接口名

```
approve(string to, string tokenId)
```

- 参数说明

  合约调用者必须是DNA的所有者，或者是DNA所有者已经对合约调用者进行了全部授权的操作。

| 参数名称 | 类型   | 必要性 | 参数描述              |
| -------- | ------ | ------ | --------------------- |
| to       | string | 是     | 目标账户              |
| tokenId  | string | 是     | 某一个DNA的唯一标识符 |

   * 返回值

     无

- 事件

​       触发 {Approval} 事件



#### getApproved()

**功能说明**

 查询某个DNA授权给了哪个帐户。

**接口描述**

- 接口名

```
getApproved(string tokenId)
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述              |
| -------- | ------ | ------ | --------------------- |
| tokenId  | string | 是     | 某一个DNA的唯一标识符 |

   * 返回值


| 参数名称 | 类型   | 参数描述           |
| -------- | ------ | ------------------ |
| operator | string | 获得授权的账户地址 |

+ 事件

​       无



#### setApprovalForAll()

**功能说明**

 该授权操作与approve操作无直接联系。

 授权（取消授权）操作，A将自己的所有DNA授权给其他账户B，B获得权限：B可以对该DNA进行转移操作。

**接口描述**

- 接口名

```
setApprovalForAll(string operator, bool approved)
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述                            |
| -------- | ------ | ------ | ----------------------------------- |
| operator | string | 是     | 获得授权的账户地址                  |
| approved | bool   | 是     | true：授权；<br />false：取消授权； |

   * 返回值

     无

- 事件

   触发{ApprovalForAll} 事件



#### isApprovedForAll()

**功能说明**

 查询某个账户A是否将自己所有的DNA授权给另一个账户B。

**接口描述**

- 接口名

```
isApprovedForAll(string owner, string operator)
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述    |
| -------- | ------ | ------ | ----------- |
| owner    | string | 是     | DNA的所有者 |
| operator | string | 是     | 授权的账户  |

   * 返回值

| 参数名称 | 类型 | 参数描述       |
| -------- | ---- | -------------- |
| result   | bool | 是否获得了授权 |

+ 事件

​        无



#### transferFrom()

**功能说明**

 转移DNA给其他账户。转移的时候需要满足以下情况：

 1） `from` 是`tokenId` 所指的DNA的所有者。

 2） 合约调用者是`from`，如果不是`from`，则合约调用者必须通过 {approve} 或 {setApprovalForAll} 得到了授权，才能转移此DNA。

**接口描述**

- 接口名

```
transferFrom(
    string from,
    string to,
    string tokenId
) 
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述              |
| -------- | ------ | ------ | --------------------- |
| from     | string | 是     | DNA的所有者账户地址   |
| to       | string | 是     | 接收DNA的账户地址     |
| tokenId  | string | 是     | 某一个DNA的唯一标识符 |

   * 返回值

     无

- 事件

​       触发{Transfer} 事件。



#### safeTransferFrom()

**功能说明**

 安全地转移DNA给其他账户。转移的时候需要满足以下情况：

 1） `from` 是`tokenId` 所指的DNA的所有者。

 2） 合约调用者是`from`，如果不是`from`，则合约调用者必须通过 {approve} 或 {setApprovalForAll} 得到了授权，才能转移此DNA。

安全转移的含义是指： 当接收方`to` 是智能合约时，首先根据DNA165的接口，检查该合约是否实现 {IDNA721Receiver-onDNA721Received}，若实现了，则代表该合约在接收到DNA之后，有一套确保该DNA继续流通的机制；若没有实现，则中止操作，防止DNA丢失。

- 如果 `to` 指的是智能合约，

**接口描述**

- 接口名

```
safeTransferFrom(
    string from,
    string to,
    string tokenId
) 
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述              |
| -------- | ------ | ------ | --------------------- |
| from     | string | 是     | DNA的所有者账户地址   |
| to       | string | 是     | 接收DNA的账户地址     |
| tokenId  | string | 是     | 某一个DNA的唯一标识符 |

   * 返回值

     无

- 事件

​       触发{Transfer} 事件。



#### bindDNA()

**功能说明**

 建立tokenId和BID的映射关系。

**接口描述**

- 接口名

```
bindDNA(string tokenId, string bid);
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述              |
| -------- | ------ | ------ | --------------------- |
| tokenId  | string | 是     | 某一个DNA的唯一标识符 |
| bid      | string | 是     | 账户地址              |

   * 返回值

     无

- 事件

​       触发{bindDNA} 事件。



#### setState()

**功能说明**

 设置DNA的可用状态。为了便于监管资产的合规性，设置DNA的状态，锁定状态的DNA不能被转移。

**接口描述**

- 接口名

```
setState(string tokenId，string state);
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述                     |
| -------- | ------ | ------ | ---------------------------- |
| tokenId  | string | 是     | 某一个DNA的唯一标识符        |
| state    | string | 是     | 0：锁定、不可用<br />1：可用 |

   * 返回值

     无

- 事件

​       触发{setState} 事件。



#### getState()

**功能说明**

 获取唯一标识为tokenId的DNA的可用状态。

**接口描述**

- 接口名

```
getState(string _tokenId) returns (string)
```

- 参数说明

| 参数名称 | 类型   | 必要性 | 参数描述              |
| -------- | ------ | ------ | --------------------- |
| tokenId  | string | 是     | 某一个DNA的唯一标识符 |

   * 返回值

| 参数名称 | 类型   | 必要性 | 参数描述      |
| -------- | ------ | ------ | ------------- |
| DNAstate | string | 是     | DNA的可用状态 |

- 事件

​       无

### 4.3 metadata格式

<table>
<caption>DNA协议中规范了元数据（metadata）的格式内容，具体字段如下：</caption>
    <tr> 
     <th rowspan="1"> 内容 </th>
        <th rowspan="1"> 描述 </th>
   </tr>
   <tr> 
  <td> seriesId </td>
     <td>集合ID</td>
   </tr>
   <tr> 
  <td> seriesIssuer </td>
     <td>发行方</td>
   </tr>
   <tr> 
  <td> dnaName </td>
     <td>数字资产名称</td>
   </tr>
   <tr> 
  <td>dnaNumber</td>
     <td>数字资产编号</td>
   </tr>
   <tr> 
  <td>dnaDes</td>
     <td>数字资产描述</td>
   </tr>
 <tr> 
  <td>url</td>
     <td>数字资产url，建议图片尺寸350*350</td>
   </tr>
 <tr> 
  <td>hash</td>
     <td>数字资产原文件哈希值</td>
   </tr>
 <tr> 
  <td>dnaType</td>
     <td>数字资产类型</td>
   </tr>
 <tr> 
  <td>extension</td>
     <td>扩展字段，用户自定义</td>
   </tr>

</table>

