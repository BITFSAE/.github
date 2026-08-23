# Git 日常协作与常用操作速查

本文档为 BITFSAE 全体队员提供日常开发常用的 Git 命令速查，涵盖首次配置、克隆仓库、分支管理、提交同步、撤销恢复与 VS Code 图形界面对照。组织权限申请与完整协作流程见 [《BITFSAE GitHub 组织新成员指南与协作手册》](./NEW_MEMBER_GUIDE.md)。

---

## 0. B站教程视频

按顺序观看：
[给傻子的Git教程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Hkr7YYEh8/?buvid=XUF9506F9F3ED6035923058512A505AAD76E9&from_spmid=search.search-result.0.0&is_story_h5=false&mid=sMmNNEBoaEZ11J3ib5%2BYPA%3D%3D&p=1&plat_id=116&share_from=ugc&share_medium=android&share_plat=android&share_session_id=8ca8eec7-2269-4a3f-a064-44b18d70764c&share_source=WEIXIN&share_tag=s_i&spmid=united.player-video-detail.0.0&timestamp=1787449507&unique_k=ClxuVzX&up_id=337242418)
[Visual Studio Code自带Git工具使用教程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1FYaAzgEsk?buvid=XUF9506F9F3ED6035923058512A505AAD76E9&from_spmid=search.search-result.0.0&mid=5J1rFWvLbnJr2mtgH6k2Hg%3D%3D&p=1&plat_id=120&share_from=ugc&share_medium=android&share_plat=android&share_session_id=4a41ac70-7df1-4cb1-b260-5a97b82149ee&share_source=WEIXIN&share_tag=s_i&timestamp=1787450047&unique_k=uAvGIBB&up_id=5129395&vd_source=1d0206161c1ea47fb147a364b9cf7982)

## 1. 首次使用：身份配置与克隆仓库

### 1.1 配置 Git 身份
首次使用需设置全局用户名与邮箱（建议使用真实姓名与常用邮箱，便于提交记录识别）：

```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

### 1.2 克隆组织仓库
车队仓库统一使用 SSH 协议（密钥配置见 [新成员指南 §1.4](./NEW_MEMBER_GUIDE.md#14-配置-ssh-密钥推荐使用-github-cli-一键自动完成)）：

```bash
# 进入本地工作目录并克隆（以 BMS 主控固件仓库为例）
cd /path/to/your/workspace
git clone git@github.com:BITFSAE/BMS-MASTER-F405.git

# 已安装 GitHub CLI 时可免复制长链接直接克隆
gh repo clone BITFSAE/BMS-MASTER-F405
```

克隆后用 VS Code 打开工程目录：`code BMS-MASTER-F405`。

## 2. 分支管理与日常同步

### 2.1 查看与切换分支
```bash
# 查看所有本地分支
git branch

# 查看所有本地与远程分支
git branch -a

# 从 main 分支创建并切换到新特性分支
git checkout main
git pull origin main
git switch -c feat/<feature-name>

# 切换到已有分支
git switch <branch-name>
```

### 2.2 日常拉取与推送
```bash
# 查看工作区文件状态
git status -s

# 拉取远程分支更新并合并至本地
git pull origin <branch-name>

# 首次推送新分支并建立远程跟踪
git push -u origin <branch-name>

# 后续常规推送
git push
```

## 3. 暂存、提交与贮藏

### 3.1 暂存与提交
```bash
# 暂存所有已修改文件
git add .

# 暂存指定文件
git add <file-path>

# 提交并附带说明
git commit -m "feat(module): description of changes"

# 修改最近一次提交信息（未 push 前可用）
git commit --amend -m "fix(module): updated description"

# 漏加了文件时：先暂存，再并入最近一次提交
git add <missed-file>
git commit --amend --no-edit
```

### 3.2 贮藏临时修改 (Stash)
当需要临时切换分支但当前修改尚未完成时使用：

```bash
# 保存当前修改到贮藏堆栈
git stash

# 查看贮藏列表
git stash list

# 恢复最近一次贮藏内容并从堆栈中移除
git stash pop
```

## 4. 撤销与修改恢复

```bash
# 放弃工作区某个文件的未暂存修改（还原文件，不可恢复）
git restore <file-path>

# 将已暂存的文件移出暂存区（保留代码修改）
git restore --staged <file-path>

# 撤销最近一次 commit 并保留代码修改
git reset HEAD~1
```

## 5. VS Code 源代码管理界面对照

VS Code 左侧活动栏的分叉树枝图标（快捷键 `Ctrl + Shift + G` / `Cmd + Shift + G`）为**源代码管理（Source Control）**面板，图形按钮与命令的对应关系如下：

| 操作 | 对应图标 / 按钮 | 等价命令与说明 |
|---|---|---|
| **查看修改对比 (Diff)** | 点击文件名 | 双栏对比视图，左侧为修改前原文件，右侧为当前修改，修改行高亮。 |
| **暂存更改 (Stage)** | 文件右侧 **`+`** 号 | 相当于 `git add <file>`，进入“暂存的更改”列表。 |
| **放弃更改 (Restore)** | 文件右侧 **`↩`** 箭头 | 相当于 `git restore <file>`，**丢弃未暂存修改，不可恢复**。 |
| **取消暂存 (Unstage)** | 文件右侧 **`-`** 号 | 相当于 `git restore --staged <file>`，修改不会丢失。 |
| **提交 (Commit)** | 蓝色 **`✓ 提交`** 按钮 | 相当于 `git commit -m "..."`，需先在输入框填写修改简述。 |
| **同步更改 (Sync)** | 底部状态栏 **`🔄 0↓ 1↑`** | `↓` 为远程未拉取 commit 数，`↑` 为本地未推送数，点击一键拉取并推送。 |
| **分支切换** | 左下角状态栏分支名 | 弹出分支列表快速切换，或 **“+ 创建新分支”**。 |
| **贮藏 (Stash)** | 面板右上角 **`...`** 菜单 | `贮藏 (Stash)` 封存当前未完成修改；`弹出贮藏 (Pop Stash)` 恢复。 |
| **修改上次提交 (Amend)** | **`...`** → `提交` 菜单 | 相当于 `git commit --amend`，用于补交漏掉的文件或修正提交信息。 |

## 6. AI Agent 辅助 Git 操作

在熟悉 Git 状态模型后，可让 AI Agent（VS Code / Cursor / Claude Code 等）根据工作区状态自动检查差异、生成规范提交信息并执行操作：

```text
# 示例 1：自动检查修改并提交
“请帮我查看当前工作区的修改内容，确认无多余垃圾文件后，创建一个 feat/bms-timeout 分支并提交，提交信息请遵循团队规范。”

# 示例 2：推送到远程
“请把当前分支推送到远程仓库，并给出对应的 GitHub PR 创建链接。”
```

注意事项：即使由 AI 执行命令，也需通过 `git diff` 或 Source Control 面板核对改动，确保不夹带机密凭证或编译临时文件。车队技能库 [`BITFSAE/bitfsae-skills`](https://github.com/BITFSAE/bitfsae-skills) 可集成至个人 AI 工具辅助规范检查。

## 7. .gitignore 核心原则

各代码仓库根目录均已预置对应的 `.gitignore` 规则：
- 编译生成的二进制与中间产物（如 `.axf`、`.hex`、`.o`、`node_modules` 等）由 `.gitignore` 自动忽略，不纳入版本控制。
- 严禁强行 `git add -f` 被忽略的临时编译文件或机密配置（如 `.env`）。
