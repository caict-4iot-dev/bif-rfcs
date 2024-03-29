# 星火链网RFC-001：星火链网RFC撰写指南

# 1. 序言

编号：RFC-001

类型：标准

标题：星火链网RFC撰写指南

作者：金键，[jin.jian@caict.ac.cn](mailto:jin.jian@caict.ac.cn)；谢家贵，[xiejiagui@caict.ac.cn](mailto:xiejiagui@caict.ac.cn)；李志平，[lizhiping@caict.ac.cn](mailto:lizhiping@caict.ac.cn)；马若龙，[maruolong@caict.ac.cn](mailto:maruolong@caict.ac.cn)；张波，[zhangbo3@caict.ac.cn](mailto:zhangbo3@caict.ac.cn)

发布时间：2021-11-20

状态：采纳

更新时间：2021-12-03

讨论地址：https://github.com/caict-4iot-dev/BIF-RFCs/issues/1

依赖RFC：无

# 2. 摘要

本文介绍“星火·链网”RFC的编写标准和规范流程。

# 3. 动机

随着“星火·链网”的发展和相关软件的开放开源，现需要一套意见征集机制，目标是面向广大“星火·链网”的使用者和开发者，鼓励提交“星火·链网”RFC标准，积极提出对“星火·链网”的改进意见，促进“星火·链网”生态的蓬勃发展。

# 4. 原理

“星火·链网”共识意见征集稿（Xinghuo Request for Consensus），简称“星火·链网”RFC。
“星火·链网”RFC是一系列以编号排定的文件，收集整理了与“星火·链网”相关的信息，包括标准、协议、意见和建议等，以及它们的发展记录。

**(1)** **适用对象**

“星火·链网”的使用者和开发者（以下简称“用户”）可以通过“星火·链网”RFC列表，获取“星火·链网”上的详细标准、协议及其功能、特性等；也能提交新的“星火·链网”RFC标准，贡献自己的想法和技术，与业内同行进行探讨意见和建议。

**(2)** **共识意见征集**

用户提交的RFC的内容形式包括：
- 标准类：给出详细的设计标准和实现方案；
- 建议类：提出个人想法、改进建议、思路等；

其中，标准类RFC包括但不限于：
- “星火·链网”协议标准；
- “星火·链网”应用规范；
- “星火·链网”治理方案。

建议类RFC包括但不限于：
- “星火·链网”底层区块链平台的改进建议；
- “星火·链网”应用或设计的新功能、新特性的建议；
- “星火·链网”现有标准规范的建议。

**(3)** **生命周期**

RFC文档的生命周期分为以下五个阶段：

- 提出（proposed）：用户提出一个新的RFC建议，代表一个值得探索讨论的合理想法；

- 演示（demonstrated）：针对提出的建议，RFC作者需要给出一个可用的实现；

- 接受（accepted）：在社区对RFC中提出的标准或功能进行讨论达成共识后，接受该建议的实现方案；

- 采纳（adopted）：采纳该RFC提议，并将此提议加入到已被承认采纳的集合中且生效。

- 撤回（retired）：当RFC作者认为RFC需要停用或者标准有重大改进需要作为新RFC提交时，此RFC被撤回，停用此RFC。

> 注：因建议类RFC不需要提供实现，其生命周期不包括演示阶段。

用户在使用“星火·链网”相关内容时，必须格外关注已采纳的“星火·链网”RFC和相关更新。

# 5. 规范

**(1)** **星火链网RFC文档模板**

- 序言：文档的基本数据格式；

- 摘要：提出改进建议的简短描述；

- 动机：描述已有RFC中的不足，解释本RFC要解决的问题；

- 原理：描述RFC的建议细节或设计原理；

- 规范：详细描述RFC中提到新标准、新功能的语法语义规范；

- 实现：提出本RFC的参考实现，标准类RFC若无实现部分将不会被接受；

- 缺陷：描述本RFC存在的缺陷和值得改进的空间。

> 注：建议类RFC文档可以无“规范”、“实现”部分。

 

其中，序言部分数据格式如下：

- 编号：RFC的唯一编号（提交时无需填写，由社区指定）；

- 类型：建议或标准；

- 标题：RFC的标题；

- 作者：作者的姓名及联系方式；

- 发布时间：RFC的提出时间；

- 状态：RFC当前处于生命周期的哪一状态下；

- 更新时间：RFC生命周期更新的时间；

- 讨论地址：开发者社区中关于本RFC的讨论网址；

- 依赖、更新或替代的RFC编号。

**(2)** **生命周期状态更新流程**

**a.  如何提出RFC**

讨论：在提出RFC之前，建议作者在开发者社区上讨论相关想法，并在讨论中详尽地考虑RFC中的细节；

命名：以新标准为RFC命名，编号待定；

编写：以RFC模板作为参考，编写RFC文档内容。一篇易被接受的RFC需要拥有详尽的描述，包括提出RFC的动机、设计原理、规范、实现、缺陷及改进等。支持在描述中添加图表和数据等；

提交：将完整RFC文档提交至“星火·链网”GitHub/Gitee，从次日起在GitHub/Gitee上进行至少为期三个月的讨论；

评审：最终由“星火·链网”核心开发者组成的评审委员会（以下简称“评审委员会”）判定文档是否通过；

编号：文档通过后，由评审委员会给此RFC分配RFC编号，并更新文档；

结束：文档通过即标志提出阶段完成，进入演示阶段；若文档未通过，则流程结束。

**b.  如何演示RFC**

实现：在演示阶段，需要RFC作者根据提出的新内容编写相应的代码或相关实现，并将代码或实现提交至GitHub/Gitee；

结束：代码或实现一经上传发布，则认为演示阶段完成，进入接受阶段。

**c.  如何接受RFC**

测试：“星火·链网”核心开发者和用户在GitHub/Gitee上对该RFC进行至少为期三个月的讨论，对代码或实现进行功能性测试，并可以提出修改意见；

反馈：由评审委员会筛选出需RFC作者应答的修改意见，每月汇总反馈一次至RFC作者；

应答：若RFC被提出修改意见，则RFC作者应对RFC进行修改或给出合理应答；

共识：该RFC在GitHub/Gitee上达成共识，评审委员会参考GitHub/Gitee上的讨论反馈和投票结果，宣布是否接受RFC；RFC被接受标志着演示阶段完成，进入接受阶段；若RFC未被接受，则流程结束。

**d.  如何采纳RFC**

研发：若RFC涉及到“星火·链网”相关系统研发，则在更新版本前，RFC处于接受阶段，待更新版本后，RFC进入采纳阶段；若RFC未涉及系统研发，如协议标准、应用规范、治理方案等，则RFC被接受后直接进入采纳阶段；

采纳：评审委员会将该RFC归入已采纳RFCs集合，并公示生效，所有“星火·链网”开发者遵循此新RFC标准。

**e.  如何撤回RFC**

申请：当RFC作者认为此RFC需要被撤回，则提交RFC撤回申请，需要在撤回申请中详细说明撤回理由，例如，有在此RFC基础上的新标准已采纳、RFC已过时或有重大缺陷；

审核：评审委员会在审核批准后将该RFC状态置为撤回，并公示撤回原因；如因新RFC导致旧RFC的撤回，则需要在撤回原因中附上新RFC链接，在新RFC的正文中注明旧RFC的编号名称和链接；

结束：RFC被撤回后，只更改其状态为撤回，原文档仍保留在GitHub/Gitee上。

**(3)** **状态更新反馈**

RFC提出并上传后，“星火·链网”核心开发者对该RFC进行“生命周期”状态维护，在文档序言中更新RFC的状态进度；

RFC状态更新后，“星火·链网”核心开发者向RFC作者邮箱发送提醒邮件，提示其已进入下一阶段，并要求作者准备下阶段所需材料和内容；

“星火·链网”如有新版本发布，需要注明已采纳的RFCs，重点突出新增的RFCs。

# 6. 实现

本文即“星火链网RFC-001”实现。

# 7. 缺陷

暂无。

 