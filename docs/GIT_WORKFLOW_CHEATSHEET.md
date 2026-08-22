# Git 日常协作命令速查与 .gitignore 规范

本文档供 BITFSAE 队员日常开发速查使用，包含分支管理常用命令、撤销与冲突解决，以及各专业方向的标准 `.gitignore` 模板。

---

## 1. 常用 Git 命令速查

### 1.1 分支与同步
```bash
# 查看所有本地与远程分支
git branch -a

# 从远程拉取最新 main 并切换创建新特性分支
git checkout main
git pull origin main
git switch -c feat/<feature-name>

# 切换已有分支
git switch <branch-name>

# 查看当前修改状态（简洁模式）
git status -s

# 推送新分支并建立远程跟踪
git push -u origin <branch-name>

# 同步远程已删除的分支（清理本地失效远程引用）
git fetch -p
```

### 1.2 提交与暂存 (Stash)
```bash
# 暂存当前未完成的工作（切换分支救急时使用）
git stash save "temp-wip"

# 查看暂存列表
git stash list

# 恢复暂存的工作并删除该暂存记录
git stash pop

# 修改上一次提交的 message（仅限尚未 push 时）
git commit --amend -m "fix(bms): revised error message"
```

### 1.3 变基与主干同步 (Rebase)
在特性分支开发期间，建议使用 `rebase` 定期同步 `main` 分支最新提交：

```bash
# 1. 获取远程最新更新
git fetch origin

# 2. 将当前分支的基线变基到最新的 origin/main
git rebase origin/main

# 3. 若有冲突：手动编辑冲突文件，解决后执行：
git add <resolved-files>
git rebase --continue

# 4. 若想放弃变基：
git rebase --abort
```

### 1.4 撤销与救急操作
```bash
# 丢弃某个文件的未暂存修改
git restore <file-path>

# 将已 git add 的文件移出暂存区
git restore --staged <file-path>

# 撤销最近一次 commit，但保留工作区修改（代码还在）
git reset --soft HEAD~1

# 彻底丢弃本地未提交的所有代码（慎用！）
git reset --hard HEAD
```

---

## 2. 标准 `.gitignore` 模板规范

为防止垃圾文件污染仓库，各工程必须在根目录配置 `.gitignore`。以下为车队典型工程模板：

### 2.1 STM32 / Keil MDK 固件工程 `.gitignore`
```gitignore
# Keil MDK 中间文件与编译生成物
*.axf
*.hex
*.bin
*.crf
*.o
*.d
*.dep
*.lst
*.map
*.htm
*.lnp
*.sct
*.build_log.htm

# Keil 输出与列表目录
Listings/
Objects/
Debug/
Release/

# Keil 用户配置（个人本地路径，避免冲突）
*.uvopt
*.uvoptx
*.uvgui.*
*.uvguix.*
JLinkLog.txt

# OS & 编辑器垃圾
.DS_Store
Thumbs.db
.vscode/
.idea/
```

### 2.2 STM32CubeIDE / GCC 工程 `.gitignore`
```gitignore
# 编译输出目录
Debug/
Release/
build/
*.elf
*.bin
*.hex
*.map
*.o
*.d

# CubeIDE 个人工作区配置
.metadata/
.settings/
*.launch
.gdb_history

# OS 临时文件
.DS_Store
Thumbs.db
```

### 2.3 Python 工具 / 桌面应用工程 `.gitignore`
```gitignore
# Python 字节码与缓存
__pycache__/
*.py[cod]
*$py.class
*.so
.pytest_cache/

# 虚拟环境
venv/
.venv/
env/
ENV/

# 打包构建产物
build/
dist/
*.egg-info/
*.spec

# 个人配置与环境变量
.env
.env.local
.DS_Store
.vscode/
.idea/
```

### 2.4 Node.js / Vue / Nuxt 前端工程 `.gitignore`
```gitignore
# 依赖
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*

# 构建产物
.output/
.nuxt/
dist/
.nitro/
.cache/

# 环境变量与机密
.env
.env.*
!.env.example

# 系统与 IDE
.DS_Store
.vscode/*
!.vscode/extensions.json
```

---

## 3. Pull Request 提交与审查 Checklist

在发起 PR 之前，请逐项核对：

- [ ] 本地代码已编译通过，且无明显 Warning。
- [ ] 实车 / 台架 / 仿真测试已验证通过。
- [ ] 检查 `git status` 与 `git diff`，确认未夹带任何编译垃圾文件或 IDE 配置文件。
- [ ] 确认未包含任何明文密码、API Key 或私钥。
- [ ] 若涉及 CAN ID 或报文改动，已在 [`BITFSAE/vehicle-interfaces`](https://github.com/BITFSAE/vehicle-interfaces) 先行发起对应 PR。
- [ ] PR 标题格式符合 `<type>(<scope>): <description>` 规范。
- [ ] 已在 GitHub 右侧 **Reviewers** 栏添加对应方向负责人。
