---
title: Git分支版本管理
hidden: false
description: Git分支版本管理
categories: 
  - 学习记录
    - 其他
tags:
  - Git
  - 版本管理
abbrlink: 1e31206d
date: 2026-03-31 17:23:23
---

### main分支和dev分支

- `main` 分支用于存放**稳定且可发布**的代码，每次提交（或合并）到main分支时，都应该使用**tag**标记正式版本。

- `dev` 分支作为**开发集成分支**，所有的新功能、改进和Bug修复都在这个分支上进行。这个分支上的代码可以不稳定，但应该保持可编译/可运行的状态。

### 日常工作流

##### 日常开发

- 所有的开发工作都在`dev`分支上进行：

```bash
git checkout dev
# 编写代码，多次提交
git add .
git commit -m "添加功能X"
```

- 如果某个功能比较复杂，也可以从 `dev` 创建临时功能分支（例如 `feature/login`），开发完成后再合并回 `dev`：

```bash
git checkout -b feature/login dev
# 开发...
git checkout dev
git merge --no-ff feature/login   # 保留合并记录
git branch -d feature/login
```

##### 定期同步远程仓库

- 建议经常将分支推送到 GitHub/GitLab 等远程仓库，以防本地丢失：

```bash
git push origin main dev
```

##### 发布版本

- 当 `dev` 分支累积了足够的功能，并经过测试达到可发布状态时，将其合并到 `main` 并打上版本标签：

```bash
git checkout main
git merge --no-ff dev          # 确保合并历史清晰
git tag -a v1.0.0 -m "版本1.0.0正式发布"
git push origin main --tags
```

- 发布后，如果 `main` 有新的提交（比如通过热修复产生的提交），需要将这些变更同步回 `dev`：

```bash
git checkout dev
git merge main                 # 将 main 的更新合并到 dev
git push origin dev
```

### 紧急热修复流程

如果线上版本（`main`）发现严重 Bug，需要立即修复，不能等待 `dev` 的常规发布周期。此时可以从 `main` 创建热修复分支：

```bash
git checkout main
git checkout -b hotfix/v1.0.1
# 修复 Bug，提交
git commit -m "修复紧急Bug"
```

修复完成后，将热修复分支合并回 `main` 和 `dev`：

```bash
# 合并到 main 并打标签
git checkout main
git merge --no-ff hotfix/v1.0.1
git tag -a v1.0.1 -m "紧急修复版本"

# 合并到 dev，确保开发分支也包含修复
git checkout dev
git merge --no-ff hotfix/v1.0.1

# 删除临时分支
git branch -d hotfix/v1.0.1

# 推送到远程
git push origin main --tags
git push origin dev
```

### 多设备并行开发流程

在多设备并行开发时，一个设备完成开发并合并到远程仓库后，另一个设备上未完成的功能分支会出现“落后”的情况。你需要将远程的最新变更（比如 `dev` 分支的更新）同步到本地，并将当前功能分支基于最新代码进行调整。

##### 先同步主开发分支（`dev`）

在尚未完成开发的设备上，首先更新本地 `dev` 分支，使其与远程保持一致。

```bash
# 切换到 dev 分支
git checkout dev

# 拉取远程最新代码（推荐使用 rebase 保持历史线性）
git pull --rebase origin dev
```

##### 将功能分支基于最新的 `dev` 进行变基（rebase）

切换到你的功能分支（假设叫 `feature/xxx`），并将它“移植”到最新的 `dev` 之上：

```bash
git checkout feature/xxx
git rebase dev
```

如果出现冲突，Git 会暂停并提示你解决。

- 解决冲突后，执行 `git add .` 然后 `git rebase --continue`。

- 如果想放弃变基，可以用 `git rebase --abort`。

现在你的功能分支已经基于最新的 `dev` 代码，可以继续开发、提交。完成后按正常流程合并回 `dev` 即可。
