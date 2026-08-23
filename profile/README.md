# <img src="https://img.bitfsae.com/img/20260822231421897.png" height="32" style="vertical-align: middle; margin-right: 8px;" /> 北京理工大学方程式赛车队 (BITFSAE)

> **Beijing Institute of Technology Formula SAE Team**  
> 本组织为车队电控固件、硬件设计、通信契约、技术文档与信息化平台的集中代码与知识资产库。

---

## 新队员指引

新加入车队的成员请首先查阅以下文档完成账号配置与权限申请：

- [《BITFSAE GitHub 组织新成员指南与协作手册》](./docs/NEW_MEMBER_GUIDE.md)：涵盖环境准备（SSH/2FA）、组织权限继承、推荐开发工作流与注意事项。
- [《Git 日常协作与常用操作说明》](./docs/GIT_WORKFLOW_CHEATSHEET.md)：日常 Git 分支管理、同步提交、撤销操作与 VS Code 图形界面对照。
- [《GitHub CLI 安装与使用指南》](./docs/GITHUB_CLI_GUIDE.md)：`gh` 安装、SSH 一键配置与 PR / Issue 常用命令。

---

## 组织团队架构

组织通过 Team 层级分发与管理仓库访问权限：

```text
BITFSAE (Organization)
├── rookie (新入队队员预备组)
├── electrical (电控与硬件组)
│   ├── electrical-core (主力 member组)
│   └── electrical-junior (预备 member组)
├── vehicle-dynamics (车辆动力学与整车控制组)
└── operations (运营与信息化运维组)
```

---

## 核心仓库资产列表

### 1. 电控固件

| 仓库                                                              | 说明                           | 核心主控 / 芯片             | 可见性             |
| --------------------------------------------------------------- | ---------------------------- | --------------------- | --------------- |
| [`BMS-MASTER-F405`](https://github.com/BITFSAE/BMS-MASTER-F405) | 现役 BMS 电池管理系统主控固件            | STM32F405RGT6         | Private         |
| [`BMS-SLAVE`](https://github.com/BITFSAE/BMS-SLAVE)             | BMS 从控与单体电压/温度采集固件           | STM32F103 + LTC6804-1 | Private         |
| [`BMS_MASTER`](https://github.com/BITFSAE/BMS_MASTER)           | 早期 STM32F105 BMS 主控固件（历史归档）  | STM32F105             | Private         |
| [`CANRS485_G473`](https://github.com/BITFSAE/CANRS485_G473)     | 四路车载 CAN 转 RS485 遥测网关        | STM32G473             | Private         |
| [`CAN2RS485`](https://github.com/BITFSAE/CAN2RS485)             | 早期 双路 CAN 转 RS485/DTU 遥测转接固件 | STM32F405             | Private         |
| [`PDM`](https://github.com/BITFSAE/PDM)                         | 低压配电与电能监测模块固件                | STM32F103 + INA226    | Private         |
| [`FanController`](https://github.com/BITFSAE/FanController)     | 水冷/风冷散热风扇 CAN 控制固件           | STM32F103             | Private         |

### 2. 硬件存档与电路设计
| 仓库                                                                  | 说明                                | 格式 / 工具            | 可见性     |
| ------------------------------------------------------------------- | --------------------------------- | ------------------ | ------- |
| [`Hardware`](https://github.com/BITFSAE/Hardware)                   | 全车 PCB 工程汇总归档（25E / 26E）          | 嘉立创 EDA (`.epro2`） | Private |
| [`fsae-hv-indicator`](https://github.com/BITFSAE/fsae-hv-indicator) | 电池箱高压指示灯（TSAL/HV Indicator）硬件电路设计 | 嘉立创 EDA / 开放设计     | Public  |

### 3. 通信契约与跨组协同
| 仓库                                                                    | 说明                               | 协同 Team                            | 可见性    |
| --------------------------------------------------------------------- | -------------------------------- | ---------------------------------- | ------ |
| [`vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces) | 整车 CAN DBC 与遥测 Protobuf 协议唯一权威定义 | `electrical`<br>`vehicle-dynamics` | Public |

### 4. 技术文档
| 仓库 | 说明 | 维护 Team | 可见性 |
|---|---|---|---|
| [`electrical-docs`](https://github.com/BITFSAE/electrical-docs) | 电气与硬件技术文档、调试记录与比赛交检知识库 | `electrical` | Private |
| [`operations-docs`](https://github.com/BITFSAE/operations-docs) | 运营组交接资料、服务器运维手册与品牌视觉资产 | `operations` | Private |

### 5. 门户网站与运维部署
| 仓库 | 说明 | 技术栈 | 可见性 |
|---|---|---|---|
| [`bitfsae-com`](https://github.com/BITFSAE/bitfsae-com) | 官方门户生产环境代码库 | Nuxt 4 + Vue 3 | Private |
| [`bitfsae_web`](https://github.com/BITFSAE/bitfsae_web) | 线上服务器遥测链路与部署脚本 | Docker + Shell | Private |
| [`BITFSAE-team-portal`](https://github.com/BITFSAE/BITFSAE-team-portal) | 车队门户公开展示版代码 | Nuxt.js | Public |

### 6. 开源工具与工程技能
| 仓库 | 说明 | 适用场景 | 可见性 |
|---|---|---|---|
| [`tidoc`](https://github.com/BITFSAE/tidoc) | 报账发票、支付凭证与查验单自动排版打印工具 | Python / 桌面应用 | Public |
| [`excel-migrator`](https://github.com/BITFSAE/excel-migrator) | 跨赛季 Excel 表格（如 ESF 申报表）数据自动迁移工具 | Python CLI | Public |
| [`bitfsae-skills`](https://github.com/BITFSAE/bitfsae-skills) | 面向车队工程与 AI Agent 的自动化工作流技能库（推荐安装至个人 AI 工具） | Markdown / Python | Public |

---

## 核心协作原则

1. **分支与 PR 协作**：多人协作或修改共享核心模块时，推荐从 `main` 切出特性分支开发，完成后发起 Pull Request 进行审查合并。
2. **保护主分支**：`main` 分支保持稳定可运行状态，避免在未经验证的情况下直接向 `main` 分支推送非琐碎修改。
3. **接口协议单一权威源**：任何 CAN ID、报文或遥测字段变更，以 [`vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces) 的 PR 评审为准，避免单端私改。
4. **安全与仓库整洁**：严禁提交 API 密钥、服务器凭证或私钥；避免提交可由源码生成的中间编译产物（如 `.axf`、`.hex`、`node_modules` 等）。
5. **AI 工具赋能**：推荐组员在 VS Code / Cursor / Claude 等 AI Agent 中引入 [`bitfsae-skills`](https://github.com/BITFSAE/bitfsae-skills)，提升代码编写与文档处理效率。

---

## 组织信息

- **官方网站**：[bitfsae.com](https://bitfsae.com)
- **组织主管理员**：[@totok22](https://github.com/totok22)
- **技术咨询与反馈**：请在对应仓库提交 Issue 或联系方向负责人。
