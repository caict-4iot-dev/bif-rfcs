# BIF-RFCs

星火链网的意见征集分为两部分：

- 星火链网改进建议提案（Xinghuo Improvement Proposals），简称“星火链网XIPs”，缩写XIP；

- 星火链网意见征集稿（Xinghuo Request for Comments），简称“星火链网RFCs”，缩写XRC。

XIP和XRC都负责收集用户对星火链的改进建议。

其中，提交的XIP可以不提供具体的方案和实现方案，只需提出个人想法、改进建议、思路等；

提交的XRC要求提出者必须依据提出XRC的动机，给出详细的设计标准和实现方案。

星火链网的意见征集适用于星火链网的使用者和开发者（以下简称“用户”），用户可以提交新的星火链网RFC标准，贡献自己的想法和技术，与业内同行进行技术探讨，同时用户也能通过星火链网RFCs列表，获取星火链网上的详细标准、协议、功能、特性等。

用户可提出的意见、改进建议包括但不限于：

- 星火链网底层区块链平台的改进建议；

- 星火链网应用或设计的新功能、新特性的建议；

- 星火链网标准规范的建议。

## 项目结构
```
├── XFCs
│  ├── 1-PROPOSED
│  ├── 2-DEMONSTRATED
│  ├── 3-ACCEPTED
│  ├── 4-ADOPTED
│  ├── 5-RETIRED
│  └── ChangeLog.md
└── XIPs
    ├── 1-PROPOSED
    ├── 2-ACCEPTED
    ├── 3-ADOPTED
    ├── 4-RETIRED
    └── ChangeLog.md
```
根据文档的生命周期，将不同文档归类：
```
PROPOSED - 提出
DEMONSTRATED - 演示
ACCEPTED - 接受
ADOPTED - 通过
RETIRED - 撤回
```
在文档的生命周期改变后，管理员移动文档至相应的路径下。
在ChangeLog.md中，标注每篇文档的当前状态、当前路径，和生命周期变更时间。

## 讨论流程
希望提出XIP/XRC的用户，在本项目中新建issue进行讨论。issue标题不限，后续将由管理用统一管理标题。
用户在issue上更新想法、提出问题、做出回复。
生命周期改变有管理员在issue上回复。
issue链接需贴在XIP/XRC正文。
