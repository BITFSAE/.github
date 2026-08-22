# BITFSAE GitHub 组织新成员指南与协作手册

欢迎加入北京理工大学方程式赛车队（BITFSAE）！

本组织（[`BITFSAE`](https://github.com/BITFSAE)）汇聚了车队赛车的**电控固件、硬件电路、通信契约、技术文档与信息化平台**。为了保证赛车工程代码的可靠性、安全性与团队协作的高效性，每位新入队成员在开始参与开发前，**必须完整阅读并遵守本手册**。

---

## 目录

1. [第一天：账号准备与本地环境配置](#1-第一天账号准备与本地环境配置)
2. [组织架构与权限认知](#2-组织架构与权限认知)
3. [标准日常开发工作流（6 步链路）](#3-标准日常开发工作流6-步链路)
4. [四大红线与关键注意事项](#4-四大红线与关键注意事项)
5. [常见故障排查 (FAQ)](#5-常见故障排查-faq)
6. [车队仓库速查导航](#6-车队仓库速查导航)

---

## 1. 第一天：账号准备与本地环境配置

在被拉入组织之前，请在 GitHub 个人账号及本地电脑上完成以下 4 项设置：

### 1.1 修改 GitHub 个人资料姓名（便于识别）
- 进入 GitHub [Profile Settings](https://github.com/settings/profile)。
- 在 **Name** 输入框中填写你的**真实中文姓名**或**姓名全拼**（例如 `武理博` 或 `Libo Wu`）。
- **原因**：避免仅凭形形色色的网名无法在 PR 审查与任务指派时定位到具体队员。

### 1.2 开启 2FA 两步验证（必须开启）
- 进入 [Account Security](https://github.com/settings/security)。
- 启用 **Two-factor authentication (2FA)**（推荐使用 1Password、Bitwarden、Google Authenticator 或 Microsoft Authenticator）。
- **原因**：组织涉及核心赛车代码与硬件资料，2FA 是防止账号被盗导致代码泄露的基础安全防线。

### 1.3 配置本地 Git 身份
打开终端（macOS/Linux 为 Terminal，Windows 推荐 Git Bash），执行：

```bash
# 设置你的真实姓名和常用邮箱
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"

# 推荐：拉取代码时默认使用 rebase 保持历史线整洁
git config --global pull.rebase true
```

### 1.4 配置 SSH 密钥（免密安全推送）
车队统一使用 SSH 协议进行代码 Clone 与 Push，不使用 HTTPS 账号密码：

1. **生成 SSH 密钥对**（若已有则跳过）：
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   # 按回车保持默认路径即可
   ```
2. **复制公钥内容**：
   - macOS: `pbcopy < ~/.ssh/id_ed25519.pub`
   - Linux: `cat ~/.ssh/id_ed25519.pub`
   - Windows (Git Bash): `clip < ~/.ssh/id_ed25519.pub`
3. **添加至 GitHub**：
   - 打开 GitHub [SSH and GPG keys](https://github.com/settings/keys)。
   - 点击 **New SSH key**，Title 填写你的电脑名称（如 `MacBook-Pro` 或 `ThinkPad-P1`），将公钥粘贴到 Key 框中并保存。
4. **测试连接**：
   ```bash
   ssh -T git@github.com
   # 看到 "Hi <username>! You've successfully authenticated..." 即表示配置成功
   ```

---

## 2. 组织架构与权限认知

### 2.1 为什么刚加入组织看不到私有仓库？
车队 GitHub 组织采用了最小权限安全策略（`Base permissions: None`）：
- **加入 Organization** 仅代表你成为车队的一员，**默认不具备任何私有仓库的读写权限**。
- 权限通过 **Team（团队）** 统一分发与继承。

```text
组织 (BITFSAE)
 ├── Team: electrical (电控与硬件) ────> 自动获得 BMS/CAN/PDM/Hardware 等仓库 Write 权限
 ├── Team: vehicle-dynamics (动规) ───> 自动获得 控制策略/整车仿真 等仓库 Write 权限
 └── Team: operations (运营与运维) ────> 自动获得 官网/云服务器运维 等仓库 Write 权限
```

### 2.2 如何获取具体项目权限？
1. 联系你的组长/方向负责人（Team Maintainer）。
2. 由负责人将你的 GitHub 账号添加进对应 Team（如 `electrical`）。
3. 添加完成后，你将**自动继承**该 Team 关联的所有仓库的访问与推送分支权限。
4. 如需跨组协作（例如电控组同学需修改整车通信接口），由对应负责人将权限关联至跨组仓库（如 `vehicle-interfaces`）。

---

## 3. 标准日常开发工作流（6 步链路）

> [!IMPORTANT]
> **车队内部统一采用“直接 Clone + 特性分支 + Pull Request”模式，组织内成员严禁使用个人 Fork 模式开发。**  
> **`main` 分支为受保护的唯一稳定主分支，严禁任何人直接 `git push origin main`！**

```text
[1. 本地 main] ──拉取最新──> [2. 切出特性分支] ──> [3. 编码与 Commit]
                                                        │
[6. 清理本地分支] <── [5. Squash & Merge] <── [4. Push 并发起 PR]
```

### 详细步骤与命令指南：

#### 第一步：克隆仓库并同步主分支
```bash
# 使用 SSH 地址克隆组织仓库（以 BMS 主控为例）
git clone git@github.com:BITFSAE/BMS-MASTER-F405.git
cd BMS-MASTER-F405

# 确保处于 main 分支并拉取最新基线
git checkout main
git pull origin main
```

#### 第二步：创建特性分支 (Feature Branch)
分支命名必须清晰表达意图，格式规范：
- 新功能：`feat/<功能简述>`（如 `feat/can-precharge-timeout`）
- Bug 修复：`fix/<问题简述>`（如 `fix/ltc6804-pec-checksum`）
- 文档更新：`docs/<文档内容>`（如 `docs/update-pinout`）
- 重构优化：`refactor/<模块名>`（如 `refactor/relay-statemachine`）

```bash
# 创建并切换到新分支
git switch -c feat/precharge-timeout
```

#### 第三步：本地编码、测试与提交 (Commit)
- 编码并做好本地编译与台架/实车验证。
- Commit 提交信息推荐采用 Conventional Commits 格式：`<type>(<scope>): <subject>`。
  - 示例：`feat(bms): add timeout handling for precharge sequence`
  - 示例：`fix(can): correct baudrate setting for can2`

```bash
git add .
git commit -m "feat(bms): add timeout handling for precharge sequence"
```

#### 第四步：推送分支到远程
```bash
# 首次推送时使用 -u 建立远程跟踪
git push -u origin feat/precharge-timeout
```

#### 第五步：在 GitHub 页面发起 Pull Request (PR)
1. 访问对应仓库页面，点击顶部黄色横幅中的 **Compare & pull request**。
2. **分支确认**：`base: main` ← `compare: feat/precharge-timeout`。
3. **填写描述**：简要列出修改内容、改动原因及测试验证结果。
4. **指派审查人 (Reviewers)**：在右侧栏选择你的**组长或相关方向学长**。
5. **代码审查 (Code Review)**：
   - 审查人会针对代码逻辑、规范与安全进行检查并提出修改建议。
   - 若有修改意见，直接在本地分支修改、commit 并 push，PR 会自动更新。
   - 获得审查人批准（`Approved`）后方可合并。
6. **合并代码**：由负责人执行 **Squash and merge**（将分支上的零碎 commit 压缩为一个干净的 commit 合入 `main`）。

#### 第六步：合并后清理本地分支
```bash
# 切换回 main 分支并拉取合并后的最新代码
git switch main
git pull origin main

# 删除已完成使命的本地特性分支
git branch -d feat/precharge-timeout
```

---

## 4. 四大红线与关键注意事项

在车队日常开发中，以下 4 条为**绝对不可触碰的工程红线**：

### 🔴 红线 1：严禁向仓库提交敏感信息与密钥凭证
- **禁止项**：车队云服务器 SSH 登录私钥、数据库密码、阿里云/腾讯云 API Secret、各类 Webhook Token、未公开的高压核心计算私密数据。
- **规范做法**：
  - 密码与 Token 存放在本地环境变量或不受版本控制的 `.env` 文件中。
  - 任何包含凭证的文件必须提前写入 `.gitignore`。
  - **若不慎提交了密钥，即使后续 commit 删除，Git 历史中仍可被提取！必须立即联系管理员重置失效密钥并彻底清理历史记录。**

### 🔴 红线 2：严禁提交编译生成物与无意义大体积二进制文件
- **禁止项**：
  - 单片机编译产物：Keil MDK 生成的 `.axf`、`.hex`、`.bin`、`.crf`、`.o`、`.d`、`Listings/`、`Objects/`；
  - IDE 临时配置与工程垃圾：`.vscode/`、`.idea/`、`*.uvoptx`（除必须共享的工程文件外）；
  - Web/前端依赖：`node_modules/`、`.nuxt/`、`.output/`、`dist/`；
  - 操作系统垃圾：`.DS_Store`、`Thumbs.db`；
  - 大体积软件包、驱动安装包、未经压缩的测试大视频。
- **规范做法**：每个仓库根目录下必须维护完善的 `.gitignore`。可参考 [Git 速查与 .gitignore 规范](./GIT_WORKFLOW_CHEATSHEET.md)。

### 🔴 红线 3：整车通信协议变更权威性 (Single Source of Truth)
- 车载 CAN 通信矩阵、DBC 文件及遥测 Protobuf 协议的**唯一权威定义源**在 [`BITFSAE/vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces)。
- **严禁**任何人在各自的单片机固件或上位机中私自篡改 CAN ID、波特率、信号位或字节序。
- 任何协议修改必须先在 `vehicle-interfaces` 仓库提交 PR，经电控组与动规组共同审查合并后，各模块方可同步更新代码。

### 🔴 红线 4：技术文档撰写必须严谨客观
- 所有技术文档（`electrical-docs`、`operations-docs` 等）统一使用客观、平实的工程技术语言。
- **严禁直接堆砌未经提炼的 AI 原始对话文本**（如包含 `👤 User`、`🤖 Assistant`、“作为一个 AI 模型”等未整理碎屑）。
- 技术判断优先给出具体可测量的物理量（如“电压 > 60V”、“低电平 0V”），未经验证的方案必须注明“待验证”。

---

## 5. 常见故障排查 (FAQ)

### Q1: 克隆或推送时提示 `Permission denied (publickey)`
- **原因**：本地 SSH 密钥未正确配置，或未将公钥添加到 GitHub 账户。
- **解决步骤**：
  1. 运行 `ssh -T git@github.com` 查看详细输出。
  2. 检查 `~/.ssh/` 目录下是否存在 `id_ed25519.pub` 或 `id_rsa.pub`。
  3. 将公钥内容完整复制并粘贴至 GitHub [SSH Keys 设置](https://github.com/settings/keys)。

### Q2: 提示 `remote: Permission to BITFSAE/<repo> denied to user`
- **原因**：你的账号尚未被加入对应 Team，或者该仓库未给该 Team 授权 Write 权限。
- **解决步骤**：联系组长或主管理员（`@totok22`），确认你已被拉入正确的 Team（如 `electrical`）。

### Q3: 提示 `Updates were rejected because the remote contains work that you do not have locally`
- **原因**：远程分支有其他同学合入的新提交，本地分支落后。
- **解决步骤**：
  ```bash
  # 拉取远程最新提交并在本地重新应用你的提交（保持线性历史）
  git pull --rebase origin <your-branch-name>
  # 解决可能出现的冲突后
  git push origin <your-branch-name>
  ```

### Q4: 本地特性分支开发周期长，如何同步 `main` 分支最新代码？
- **推荐做法（变基同步）**：
  ```bash
  git fetch origin
  git rebase origin/main
  # 若有冲突，按照提示修改冲突文件后执行：
  git add <resolved-file>
  git rebase --continue
  ```

### Q5: 不小心 `git add` 了不该提交的文件怎么办？
- **尚未 commit 时**：
  ```bash
  git restore --staged <file-path>
  ```
- **刚 commit 尚未 push 时**：
  ```bash
  # 撤销上一次 commit 但保留代码修改
  git reset HEAD~1
  ```

---

## 6. 车队仓库速查导航

| 仓库名称 | 说明 | 推荐工具 / 环境 |
|---|---|---|
| [`BMS-MASTER-F405`](https://github.com/BITFSAE/BMS-MASTER-F405) | 现役 BMS 主控固件 | STM32CubeIDE / Keil MDK / VSCode |
| [`BMS-SLAVE`](https://github.com/BITFSAE/BMS-SLAVE) | BMS 从控采集固件 | Keil MDK / STM32CubeMX |
| [`CANRS485_G473`](https://github.com/BITFSAE/CANRS485_G473) | 四路 CAN 遥测网关 | STM32CubeIDE / PlatformIO |
| [`vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces) | 整车 CAN 协议与 DBC | DBC Editor / VS Code / Python |
| [`Hardware`](https://github.com/BITFSAE/Hardware) | 嘉立创 PCB 工程合集 | 嘉立创 EDA 专业版 (EasyEDA Pro) |
| [`electrical-docs`](https://github.com/BITFSAE/electrical-docs) | 电气组技术文档库 | Obsidian / Markdown 编辑器 |
| [`operations-docs`](https://github.com/BITFSAE/operations-docs) | 运营组文档与视觉资产 | Obsidian / Markdown 编辑器 |
| [`bitfsae-com`](https://github.com/BITFSAE/bitfsae-com) | 官方网站正式仓库 | Node.js 20+ / pnpm / VS Code |
| [`tidoc`](https://github.com/BITFSAE/tidoc) | 报账票据整理桌面应用 | Python 3.10+ / PySide6 |
