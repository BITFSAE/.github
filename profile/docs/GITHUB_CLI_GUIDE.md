# GitHub CLI (gh) 安装与使用指南

GitHub CLI（命令行程序为 `gh`）是 GitHub 官方命令行工具，将账号认证、SSH 密钥自动配置、Pull Request、Issue、仓库管理及 API 调用整合至终端，可大幅简化新队员环境配置与日常协作步骤。

---

## 1. 多平台安装方法

### 1.1 Windows 安装
推荐使用系统自带包管理器 `winget` 或 `scoop`：

```powershell
# 使用 winget 安装（推荐）
winget install --id GitHub.cli

# 或使用 Scoop 安装
scoop install gh
```
也可从 [GitHub CLI 官方发布页](https://github.com/cli/cli/releases) 下载 `.msi` 安装包双击安装。

### 1.2 macOS 安装
```bash
brew install gh
```

### 1.3 Linux 安装
- **Debian / Ubuntu**：
  ```bash
  (type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
    && sudo mkdir -p -m 755 /etc/apt/keyrings \
    && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
    && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
    && sudo apt update \
    && sudo apt install gh -y
  ```
- **Arch Linux**：
  ```bash
  sudo pacman -S github-cli
  ```

安装后在终端验证：
```bash
gh --version
```

## 2. 账号登录与 SSH 密钥自动配置

`gh` 最实用的功能之一是**全自动生成并绑定本地 SSH 密钥**，免去手动 `ssh-keygen`、复制公钥与网页设置的繁琐步骤。

### 2.1 一键认证
在终端运行：
```bash
gh auth login
```
按交互提示依次操作：
1. **What account do you want to log into?** → `GitHub.com`
2. **What is your preferred protocol for Git operations on this host?** → `SSH`
3. **SSH 密钥自动处理**：
   - 本地无密钥：提示 `Generate a new SSH key?`，回车确认后自动生成密钥对并上传至 GitHub 账号；
   - 本地已有密钥：提示 `Upload your public SSH key?`，回车即自动上传绑定。
4. **How would you like to authenticate GitHub CLI?** → `Login with a web browser`，在浏览器中输入终端给出的 8 位授权码并点击 **Authorize github** 完成。

### 2.2 验证登录与 SSH 状态
```bash
gh auth status
```
正常输出示例：
```text
github.com
  ✓ Logged in to github.com account <username> (ssh_key)
  - Active account: true
  - Git operations protocol: ssh
  - Scopes: 'repo', 'read:org', 'gist'
```

### 2.3 手动上传已有公钥（备选）
```bash
gh ssh-key add ~/.ssh/id_ed25519.pub --title "My-Laptop"
```

## 3. 仓库管理 (Repository)

```bash
# 1. 快速克隆组织仓库（无需复制长链接，直接 组织/仓库名）
gh repo clone BITFSAE/BMS-MASTER-F405
gh repo clone BITFSAE/vehicle-interfaces

# 2. 查看当前组织下所有有权访问的仓库列表
gh repo list BITFSAE

# 3. 在浏览器中打开当前工程对应的 GitHub 网页
gh repo view --web

# 4. 查看指定仓库的详情与 README 说明
gh repo view BITFSAE/vehicle-interfaces
```

> 注：克隆私有仓库前，需先由组长将账号加入对应 Team（见 [新成员指南 §2](./NEW_MEMBER_GUIDE.md#2-组织架构与权限管理)），否则会提示无权限。

## 4. Pull Request (PR) 协作

```bash
# 1. 在当前特性分支直接发起 PR
gh pr create --title "feat(bms): add timeout handling" --body "测试记录已验证" --reviewer <username>

# 2. 交互式创建 PR（终端逐步填写标题、描述与 Reviewer）
gh pr create

# 3. 查看当前仓库所有开启中的 PR
gh pr list

# 4. 查看指定 PR 详细改动与状态
gh pr view <pr-number>

# 5. 一键在本地检出队友发起的 PR 分支进行调试与实车测试
gh pr checkout <pr-number>

# 6. 审查人批准 PR
gh pr review <pr-number> --approve -b "实测通过，同意合并"

# 7. 合并 PR 并采用 Squash 模式（负责人操作）
gh pr merge <pr-number> --squash --delete-branch
```

## 5. 任务与问题跟踪 (Issue)

```bash
# 1. 快速创建 Issue
gh issue create --title "[电检] TSAL 高压指示灯阈值需重新标定" --body "描述具体现象与数据"

# 2. 查看当前仓库 Issue 列表
gh issue list

# 3. 查看指定 Issue 详情
gh issue view <issue-number>

# 4. 关闭已解决的 Issue
gh issue close <issue-number>
```

## 6. 组织与团队查询 (API 调用)

```bash
# 查看 BITFSAE 组织下所有团队列表
gh api orgs/BITFSAE/teams

# 查看电控组下属成员
gh api orgs/BITFSAE/teams/electrical/members
```

## 7. AI Agent 与自动化集成

`gh` 提供完全非交互式的命令行接口（`--json` 格式化输出），可无缝集成至 VS Code、Cursor 及各类 AI Agent：

1. **AI Agent 直接调用**：AI 可直接执行 `gh pr view`、`gh repo list` 等命令获取上下文，无需在网页与终端间复制粘贴。
2. **配合车队技能库**：结合 [`BITFSAE/bitfsae-skills`](https://github.com/BITFSAE/bitfsae-skills)，AI Agent 能自动校验分支状态、生成规范 PR 描述并调用 `gh pr create` 发起审查。

## 8. 常见问题排查

### 8.1 提示 `authentication token expired` 或权限不足
```bash
gh auth login --refresh -h github.com
```

### 8.2 访问组织私有仓库提示无权限
确保登录时为 `gh` 授予了读取组织数据的权限：
```bash
gh auth refresh -s read:org,repo
```

### 8.3 默认编辑器配置
若 `gh pr create` 唤起了不熟悉的编辑器（如 nano / vim），可配置为 VS Code：
```bash
gh config set editor "code --wait"
```
