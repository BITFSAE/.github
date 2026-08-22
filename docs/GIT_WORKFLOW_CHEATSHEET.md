# Git 日常协作常用命令速查

本文档供 BITFSAE 队员日常开发速查使用，汇总常用分支管理、提交、同步与撤销操作。

---

## 1. 分支管理与日常同步

### 1.1 查看与切换分支
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

### 1.2 日常拉取与推送
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

---

## 2. 暂存、提交与贮藏

### 2.1 暂存与提交
```bash
# 暂存所有已修改文件
git add .

# 暂存指定文件
git add <file-path>

# 提交并附带说明
git commit -m "feat(module): description of changes"

# 修改最近一次提交信息（未 push 前可用）
git commit --amend -m "fix(module): updated description"
```

### 2.2 贮藏临时修改 (Stash)
当需要临时切换分支但当前修改尚未完成时使用：

```bash
# 保存当前修改到贮藏堆栈
git stash

# 查看贮藏列表
git stash list

# 恢复最近一次贮藏内容并从堆栈中移除
git stash pop
```

---

## 3. 撤销与修改恢复

```bash
# 放弃工作区某个文件的未暂存修改（还原文件）
git restore <file-path>

# 将已暂存的文件移出暂存区（保留代码修改）
git restore --staged <file-path>

# 撤销最近一次 commit 并保留代码修改
git reset HEAD~1
```

---

## 4. .gitignore 核心原则

各代码仓库根目录均已预置对应的 `.gitignore` 规则：
- 编译生成的二进制与中间产物（如 `.axf`、`.hex`、`.o`、`node_modules` 等）由 `.gitignore` 自动忽略，不纳入版本控制。
- 严禁强行 `git add -f` 被忽略的临时编译文件或机密配置（如 `.env`）。
