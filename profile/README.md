# 北京理工大学方程式赛车队 (BITFSAE)

> **Beijing Institute of Technology Formula SAE Team**  
> 致力于打造高性能纯电动与无人驾驶方程式赛车。本 GitHub 组织为车队电控固件、硬件设计、通信契约、技术文档与信息化平台的集中代码与知识资产库。

---

## 🚀 新队员第一站 (Start Here)

如果你是刚加入车队的新队员，请首先阅读以下文档完成账号配置与权限申请：

- 📖 [**《BITFSAE GitHub 组织新成员指南与协作手册》**](./docs/NEW_MEMBER_GUIDE.md)：涵盖环境准备（SSH/2FA）、组织权限继承、标准 Clone-Branch-PR 工作流与四大红线注意事项。
- 🛠️ [**《Git 日常协作与命令速查表》**](./docs/GIT_WORKFLOW_CHEATSHEET.md)：常用 Git 分支管理、提交与冲突排查命令。

---

## 📦 核心仓库资产矩阵 (Repositories)

车队代码与文档按专业方向划分，通过 Team 矩阵进行协同维护：

### 1. 🏎️ 电控固件与低压系统 (Firmware)
| 仓库 | 说明 | 核心主控 / 芯片 | 访问类型 |
|---|---|---|---|
| [`BMS-MASTER-F405`](https://github.com/BITFSAE/BMS-MASTER-F405) | 现役 BMS 电池管理系统主控固件 | STM32F405RGT6 | Private |
| [`BMS-SLAVE`](https://github.com/BITFSAE/BMS-SLAVE) | BMS 从控与单体电压/温度采集固件 | STM32F103 + LTC6804-1 | Private |
| [`BMS_MASTER`](https://github.com/BITFSAE/BMS_MASTER) | 早期 STM32F105 BMS 主控固件（历史归档） | STM32F105 | Private |
| [`CANRS485_G473`](https://github.com/BITFSAE/CANRS485_G473) | 四路车载 CAN 转 RS485 遥测网关 | STM32G473 | Public |
| [`CAN2RS485`](https://github.com/BITFSAE/CAN2RS485) | 双路 CAN 转 RS485/DTU 遥测转接固件 | STM32F405 | Public |
| [`PDM`](https://github.com/BITFSAE/PDM) | 低压配电与电能监测模块固件 | STM32F103 + INA226 | Private |
| [`FanController`](https://github.com/BITFSAE/FanController) | 水冷/风冷散热风扇 CAN 控制固件 | STM32F103 | Private |
| [`SerialFlash`](https://github.com/BITFSAE/SerialFlash) | STM32 串口 Bootloader 烧录工具 | Node.js / Web / CLI | Private |

### 2. ⚡ 硬件与电路设计 (Hardware)
| 仓库 | 说明 | 格式 / 工具 | 访问类型 |
|---|---|---|---|
| [`Hardware`](https://github.com/BITFSAE/Hardware) | 全车 PCB 工程汇总归档（25E / 26E） | 嘉立创 EDA (`.epro2` / `.epro`) | Private |
| [`fsae-hv-indicator`](https://github.com/BITFSAE/fsae-hv-indicator) | 电池箱高压指示灯（TSAL/HV Indicator）纯硬件电路设计 | 嘉立创 EDA / 开放设计 | Public |

### 3. 📡 通信契约与跨组协同 (Interfaces)
| 仓库 | 说明 | 协同 Team | 访问类型 |
|---|---|---|---|
| [`vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces) | **唯一权威**的整车 CAN DBC 与遥测 Protobuf 协议定义 | `electrical`<br>`vehicle-dynamics` | Public |

### 4. 📚 知识库与技术文档 (Team Docs)
| 仓库 | 说明 | 维护 Team | 访问类型 |
|---|---|---|---|
| [`electrical-docs`](https://github.com/BITFSAE/electrical-docs) | 电气与硬件技术文档、调试记录与比赛交检知识库 | `electrical` | Private |
| [`operations-docs`](https://github.com/BITFSAE/operations-docs) | 运营组交接资料、服务器运维手册与品牌视觉资产 | `operations` | Private |

### 5. 🌐 门户网站与运维部署 (Operations)
| 仓库 | 说明 | 技术栈 | 访问类型 |
|---|---|---|---|
| [`bitfsae-com`](https://github.com/BITFSAE/bitfsae-com) | 官方门户生产环境代码库 | Nuxt 4 + Vue 3 | Private |
| [`bitfsae_web`](https://github.com/BITFSAE/bitfsae_web) | 线上服务器遥测链路 (MQTT/InfluxDB/Grafana) 与部署脚本 | Docker + Shell | Private |
| [`BITFSAE-team-portal`](https://github.com/BITFSAE/BITFSAE-team-portal) | 车队门户公开展示版代码 | Nuxt.js | Public |

### 6. 🛠️ 开源工具 (Tools)
| 仓库 | 说明 | 适用场景 | 访问类型 |
|---|---|---|---|
| [`tidoc`](https://github.com/BITFSAE/tidoc) | 报账发票、支付凭证与查验单自动排版打印工具 | Python / 跨平台桌面应用 | Public |
| [`excel-migrator`](https://github.com/BITFSAE/excel-migrator) | 跨赛季 Excel 表格（如 ESF 申报表）数据自动迁移工具 | Python CLI | Public |
| [`bitfsae-skills`](https://github.com/BITFSAE/bitfsae-skills) | 面向车队工程与 AI Agent 的自动化工作流技能包 | Markdown / Python | Public |

---

## 🛡️ 核心协作原则 (Principles)

1. **直接 Clone + 特性分支开发**：组织内队员不使用 Fork 模式，直接从主仓库切出 `feat/<name>` 或 `fix/<name>` 分支。
2. **保护主分支（`main`）**：`main` 代表可上车或可发布的稳定基线，**严禁任何人直接 push 或 force-push 到 `main`**。
3. **强制 Code Review 与 PR 机制**：所有代码修改必须经过至少 1 位方向负责人审阅批准后，通过 **Squash and Merge** 合入。
4. **接口协议单一权威源**：任何 CAN ID、报文或遥测字段变更，必须以 [`vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces) 的 PR 评审为准，严禁单端私改。
5. **严守安全与整洁红线**：严禁提交 API Key、服务器私钥等机密数据；严禁提交 `.axf`/`.o`/`node_modules` 等中间编译垃圾。

---

## 📮 组织与联系方式

- **官方网站**：[bitfsae.com](https://bitfsae.com)
- **组织主管理员**：[@totok22](https://github.com/totok22)
- **技术咨询与反馈**：请在对应仓库提交 Issue 或联系组内负责人。
