# <img src="https://img.bitfsae.com/img/20260822231421897.png" height="32" style="vertical-align: middle; margin-right: 8px;" /> BITFSAE GitHub 组织新成员指南与协作手册

北京理工大学方程式赛车队（BITFSAE）GitHub 组织（[`BITFSAE`](https://github.com/BITFSAE)）汇聚了车队赛车的电控固件、硬件电路、通信契约、技术文档与信息化平台。为了保证工程代码的可靠性、安全性与协作效率，新入队成员在参与开发前请完整阅读本手册。

---

## 目录

1. [账号准备与本地环境配置](#1-账号准备与本地环境配置)
2. [组织架构与权限管理](#2-组织架构与权限管理)
3. [日常开发协作流程](#3-日常开发协作流程)
4. [工程规范与安全注意事项](#4-工程规范与安全注意事项)
5. [AI Agent 与车队技能包配置](#5-ai-agent-与车队技能包配置)
6. [常见问题排查 (FAQ)](#6-常见问题排查-faq)
7. [仓库速查列表](#7-仓库速查列表)

---

## 1. 账号准备与本地环境配置

在加入组织前，请完成以下基础设置：

### 1.1 修改 GitHub 个人资料姓名
- 进入 GitHub [Profile Settings](https://github.com/settings/profile)。
- 在 **Name** 项中填写**真实中文姓名**或**姓名全拼**（例如 `武理博` 或 `Libo Wu`）。
- 目的：便于在 Pull Request 审查、任务指派和代码提交记录中准确识别成员。

### 1.2 开启 2FA 两步验证
- 进入 GitHub [Account Security](https://github.com/settings/security)。
- 启用 **Two-factor authentication (2FA)**（支持使用 1Password、Bitwarden、Google Authenticator 或 Microsoft Authenticator）。
- 目的：防范账号密码泄露导致的代码与敏感工程数据风险。

### 1.3 配置本地 Git 基础信息
打开终端（Windows 推荐 Git Bash，macOS/Linux 使用系统终端），执行：

```bash
# 设置用户名和邮箱
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

### 1.4 配置 SSH 密钥（推荐使用 GitHub CLI 一键自动完成）
车队统一使用 SSH 协议进行代码克隆与推送。配置方式分为自动与手动两种：

#### 方式一：使用 GitHub CLI 自动配置（推荐）
若已安装 `gh`（参考 [`github cli使用说明.md`](https://github.com/BITFSAE/electrical-docs/blob/main/%E5%85%B6%E4%BB%96%E8%BD%AF%E4%BB%B6%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/github%20cli%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E.md)），在终端执行：

```bash
gh auth login
```
按照终端提示交互选择：
1. **What account do you want to log into?** -> `GitHub.com`
2. **What is your preferred protocol for Git operations on this host?** -> `SSH`
3. **Upload your SSH public key / Generate a new SSH key?** -> 回车确认（`gh` 会自动识别已有公钥或在本地生成新密钥并直接上传至 GitHub 账号，无需手动复制粘贴）
4. **How would you like to authenticate GitHub CLI?** -> `Login with a web browser`，在弹出的浏览器页面输入终端给出的 8 位验证码即可完成。

#### 方式二：手动生成与配置（备选）
若未安装 `gh`，可通过系统命令手动完成：
1. **生成 SSH 密钥对**：
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   # 一路回车使用默认路径即可
   ```
2. **复制公钥内容**（`~/.ssh/id_ed25519.pub`）。
3. **添加至 GitHub**：打开 GitHub [SSH and GPG keys](https://github.com/settings/keys)，点击 **New SSH key** 粘贴并保存。
4. **验证连接**：
   ```bash
   ssh -T git@github.com
   # 输出 "Hi <username>! You've successfully authenticated..." 即表示成功
   ```

---

## 2. 组织架构与权限管理

### 2.1 权限隔离机制
组织采用 `Base permissions: None` 安全策略：
- 加入 Organization 成为普通成员后，默认不分配任何私有仓库权限。
- 仓库读写权限由 **Team（团队）** 统一分发与继承。

```text
组织 (BITFSAE)
├── rookie (新入队队员预备组)
├── electrical (电控与硬件组)
│   ├── electrical-core (核心开发组) ────> 固件、硬件与文档仓库 Write 权限
│   └── electrical-junior (预备与日常维护组) ────> 基础开发与文档维护 Write 权限
├── vehicle-dynamics (动规组) ────> 整车控制与仿真仓库 Write 权限
└── operations (运营组) ────> 官网与服务器运维仓库 Write 权限
```

### 2.2 权限申请流程
1. 联系组长或技术方向负责人（Team Maintainer）。
2. 由负责人将 GitHub 账号添加进对应 Team（如 `electrical-junior` 或 `electrical-core`）。
3. 成员自动获得该 Team 所关联仓库的读写权限。
4. 跨组协作（如电控需协同整车通信接口）通过关联跨组仓库（如 `vehicle-interfaces`）获取权限。

---

## 3. 日常开发协作流程

组织内项目在多人协作或改动核心代码时，推荐采用**特性分支 + Pull Request（PR）**流程：

```text
1. 从 main 切出分支 ──> 2. 本地编码与测试 ──> 3. 提交 Commit
                                                    │
6. 清理本地分支 <── 5. Review 批准并合并 <── 4. Push 并发起 PR
```

### 3.1 详细操作步骤

#### 步骤 1：克隆仓库并同步主分支
```bash
# 以 BMS 主控仓库为例
git clone git@github.com:BITFSAE/BMS-MASTER-F405.git
cd BMS-MASTER-F405

# 确保处于 main 分支并拉取最新基线
git checkout main
git pull origin main
```

#### 步骤 2：创建特性分支
分支命名要求清晰表意：
- 新功能：`feat/<功能简述>`（如 `feat/can-precharge-timeout`）
- Bug 修复：`fix/<问题简述>`（如 `fix/ltc6804-pec-error`）
- 文档更新：`docs/<文档内容>`（如 `docs/update-pinout`）

```bash
git switch -c feat/precharge-timeout
```

#### 步骤 3：本地编码、验证与提交
- 完成编码并进行本地编译、台架测试或实车验证。
- Commit Message 推荐格式：`<type>(<scope>): <subject>`。

```bash
git add .
git commit -m "feat(bms): add timeout handling for precharge sequence"
```

#### 步骤 4：推送分支并创建 PR
```bash
# 推送至远程仓库
git push -u origin feat/precharge-timeout
```
前往 GitHub 仓库页面点击 **Compare & pull request**，填写变更说明并在右侧 **Reviewers** 栏指定相关负责人。

#### 步骤 5：审查与合并
- 负责人针对逻辑、规范与测试结果进行 Review。
- 确认无误并批准（`Approved`）后，通过 **Squash and merge** 合并入 `main` 分支。

#### 步骤 6：本地分支清理
```bash
git switch main
git pull origin main
git branch -d feat/precharge-timeout
```

---

## 4. 工程规范与安全注意事项

### 4.1 严禁提交凭证与敏感信息
- **禁止内容**：云服务器 SSH 私钥、数据库密码、第三方 API Secret、各类 Webhook Token。
- **处理方式**：敏感参数存放在本地环境变量或 `.env` 文件中，并确保该文件已加入 `.gitignore`。若不慎提交，需立即联系管理员重置凭证。

### 4.2 避免提交编译中间产物与无关大文件
- **单片机固件**：过滤 Keil MDK 生成的 `.axf`、`.hex`、`.o`、`.d`、`Listings/`、`Objects/`，以及 CubeIDE 的 `Debug/`、`Release/`。
- **前端与脚本**：过滤 `node_modules/`、`.output/`、`__pycache__/`、`venv/`。
- **系统文件**：过滤 `.DS_Store`、`Thumbs.db`。

### 4.3 协议变更唯一权威源 (Single Source of Truth)
- 车载 CAN 通信矩阵、DBC 文件及遥测 Protobuf 协议的权威源位于 [`BITFSAE/vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces)。
- 协议修改必须先在 `vehicle-interfaces` 仓库发起 PR 评审，合并后各固件与上位机再同步适配，禁止单端私自更改 CAN ID 或信号位。

### 4.4 文档撰写规范
- 技术文档使用客观平实的工程语言，记录实际参数、测量值与验证步骤。
- 严禁直接复制粘贴未经提炼的 AI 原始对话文本。

---

## 5. AI Agent 与车队技能包配置

车队维护了通用工程技能库 [`BITFSAE/bitfsae-skills`](https://github.com/BITFSAE/bitfsae-skills)。

- 推荐队员将该技能库引入自己的 AI 工具（如 Cursor、Claude Code、GitHub Copilot 等）。
- 技能库包含车队工程规范、CAN 协议校验与常用自动化脚本，可在对话中直接辅助完成规范检查与代码生成。

---

## 6. 常见问题排查 (FAQ)

### Q1: `Permission denied (publickey)`
- 检查本地公钥（`~/.ssh/id_ed25519.pub`）是否已正确复制并添加至 GitHub SSH Keys 设置中。
- 运行 `ssh -T git@github.com` 检验鉴权状态。

### Q2: `remote: Permission to BITFSAE/<repo> denied to user`
- 确认当前账号已被加入对应 Team（如 `electrical-core` 或 `electrical-junior`）。
- 检查仓库权限列表中是否已关联该 Team。

### Q3: 远程存在新提交导致 Push 被拒
- 执行 `git pull origin <branch-name>` 将远程最新变更拉取合并后，再行推送。

### Q4: 撤销未 push 的本地提交
- 撤销最近一次 commit 并保留本地修改：
  ```bash
  git reset HEAD~1
  ```
- 撤销暂存区文件：
  ```bash
  git restore --staged <file-path>
  ```

---

## 7. 仓库速查列表

| 仓库 | 用途 | 适用环境 / 工具 |
|---|---|---|
| [`BMS-MASTER-F405`](https://github.com/BITFSAE/BMS-MASTER-F405) | BMS 主控固件 | STM32CubeIDE / Keil MDK |
| [`BMS-SLAVE`](https://github.com/BITFSAE/BMS-SLAVE) | BMS 从控采集固件 | Keil MDK / STM32CubeMX |
| [`CANRS485_G473`](https://github.com/BITFSAE/CANRS485_G473) | 四路 CAN 遥测网关 | STM32CubeIDE / PlatformIO |
| [`vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces) | 整车 CAN 协议与 DBC | VS Code / Python / DBC Tools |
| [`Hardware`](https://github.com/BITFSAE/Hardware) | 嘉立创 PCB 工程归档 | 嘉立创 EDA 专业版 |
| [`electrical-docs`](https://github.com/BITFSAE/electrical-docs) | 电气组技术文档库 | Obsidian / Markdown 编辑器 |
| [`operations-docs`](https://github.com/BITFSAE/operations-docs) | 运营组文档与视觉资产 | Obsidian / Markdown 编辑器 |
| [`bitfsae-com`](https://github.com/BITFSAE/bitfsae-com) | 官方网站代码库 | Node.js 20+ / pnpm / VS Code |
| [`bitfsae-skills`](https://github.com/BITFSAE/bitfsae-skills) | 车队 AI Agent 技能库 | AI 开发环境 |
