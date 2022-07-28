# BIF-RFCs

“星火·链网”共识意见征集稿（Xinghuo Request for Consensus），简称“星火·链网”RFC。

“星火·链网”RFC是一系列以编号排定的文件，收集整理了与“星火·链网”相关的信息，包括标准、协议、意见和建议等，以及它们的发展记录。
“星火·链网”的使用者和开发者（以下简称“用户”）可以通过“星火·链网”RFC列表，获取“星火·链网”上的详细标准、协议及其功能、特性等；也能提交新的“星火·链网”RFC标准，贡献自己的想法和技术，与业内同行进行探讨意见和建议。

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


已提交的RFC会依据《星火链网RFC-001：星火链网RFC撰写指南》中的生命周期标准，依次完成提出、演示、接受、采纳、撤回等流程阶段。

![生命周期](https://user-images.githubusercontent.com/90955034/181481745-e4038200-9fdf-4049-b280-b87ba4646b1d.png)


期待与全球开发者们共同努力，共同推动“星火·链网”的开放与开源，共同建设“星火·链网”开发者社区，共同完善“星火·链网”治理规则，建立一个数字镜像的零信任世界。

## 项目结构
```
├── RFCs
   ├── 1-PROPOSED
   ├── 2-DEMONSTRATED
   ├── 3-ACCEPTED
   ├── 4-ADOPTED
   ├── 5-RETIRED
   └── ChangeLog.md

```
根据文档的生命周期，将不同文档归类：
```
PROPOSED - 提出
DEMONSTRATED - 演示
ACCEPTED - 接受
ADOPTED - 采纳
RETIRED - 撤回
```
在文档的生命周期改变后，管理员移动文档至相应的路径下。
在ChangeLog.md中，标注每篇文档的当前状态、当前路径，和生命周期变更时间。

## 讨论流程
希望提出RFC的用户，在本项目中新建issue进行讨论。issue标题不限，后续将由管理用统一管理标题。
用户在issue上更新想法、提出问题、做出回复。
生命周期改变有管理员在issue上回复。
issue链接需贴在RFC正文。
