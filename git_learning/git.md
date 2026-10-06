# Git 基础教程

## 第一章：Git 简介

### 核心概念
- **集中式 vs 分布式**：Git 是分布式版本控制系统，每个人电脑上都有完整的版本库，而 SVN 等是集中式，必须联网才能从中央服务器获取。
- **暂存区 (Stage/Index)**：Git 和其他版本控制系统如 SVN 的一个重要区别是 Git 有暂存区的概念。
- **工作区 (Working Directory)**：电脑里能直接看到的目录。
- **版本库 (Repository)**：工作区有一个隐藏目录 `.git`，这就是 Git 的版本库。
  - 版本库里存了很多东西，其中最重要的就是称为 `stage`（或者叫 `index`）的暂存区，还有 Git 为我们自动创建的第一个分支 `master`，以及指向 `master` 的一个指针叫 `HEAD`。


## 第二章：安装 Git

### 重点指令
Linux (Debian/Ubuntu):

```
sudo apt-get install git
```

Linux (Fedora/CentOS):

```
sudo yum install git
```

macOS：通过 Xcode Command Line Tools 安装

```
xcode-select --install
```

Windows：下载 [https://git-scm.com/downloads/win](https://git-scm.com/downloads/win) 安装包并安装。

全局配置（必须配置，标识你的身份）

```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

> 
> 作用：设置全局的用户名和邮箱，每次提交记录都会带上这些信息。`--global` 表示这台机器上所有的 Git 仓库都会使用这个配置。

---

## 第三章：创建版本库

### 重点指令

初始化仓库：

```
git init
```

> 
> 作用：把当前目录变成 Git 可以管理的仓库，会生成一个隐藏的 `.git` 目录。

把文件添加到暂存区：

```
git add <file>
```

> 
> 作用：将文件的修改添加到暂存区（Stage）。可以多次 add 不同的文件。

把暂存区的文件提交到当前分支：

```
git commit -m "message"
```

> 
> 作用：将暂存区的所有内容提交到当前分支。`-m` 后面输入的是本次提交的说明，方便从历史记录里找到改动。

---

## 第四章：时光机穿梭

### 重点指令

查看工作区状态：

```
git status
```

> 
> 作用：查看当前工作区和暂存区的状态，是否有文件被修改、未暂存或未提交。

查看文件具体修改内容：

```
git diff
```

> 
> 作用：查看工作区和暂存区之间的差异（即修改了什么内容）。

查看提交历史：

```
git log
```

> 
> 作用：显示从最近到最远的提交日志。加上 `--pretty=oneline` 可以简化输出，只显示 commit id 和说明。

版本回退：

```
git reset --hard HEAD^
```

> 
> 作用：回退到上一个版本。HEAD 指向当前版本，`HEAD^` 上一版本，`HEAD^^` 上上版本，`HEAD~100` 往上 100 个版本。
> 也可以回退到指定 commit id 的版本：`git reset --hard <commit_id>`

查看命令历史：

```
git reflog
```

> 
> 作用：记录你的每一次命令，用于找到未来的 commit id（当你回退后又想恢复到新版本时使用）。

撤销修改（丢弃工作区的修改）：

```
git checkout -- <file>
```

> 
> 作用：让文件回到最近一次 git commit 或 git add 时的状态。
> (注：Git 新版本推荐使用 `git restore --worktree <file>` 替代)

撤销暂存（把暂存区的修改退回工作区）：

```
git reset HEAD <file>
```

> 
> 作用：把暂存区的修改撤销掉（unstage），重新放回工作区。
> (注：Git 新版本推荐使用 `git restore --staged <file>` 替代)

删除文件：

```
git rm <file>
```

> 
> 作用：从版本库中删除文件，并且此删除操作会被添加到暂存区，之后需要 `git commit` 确认删除。如果误删，可以用 `git checkout -- <file>` 恢复。

---

## 第五章：分支管理

### 核心概念

分支是 Git 的杀手级特性。创建、切换、删除分支非常快。
master 分支是一条线，Git 用 master 指向最新的提交，再用 HEAD 指向 master，就确定了当前分支及当前分支的提交点。
合并分支时，Git 默认会使用 Fast forward 模式，这种模式下删除分支后会丢掉分支信息。

### 重点指令

创建与切换分支：

```
git branch <name>    # 创建分支
git checkout <name>  # 切换分支
git switch <name>    # Git 新版本推荐的切换分支命令
```

> 
> 作用：创建名为 `<name>` 的分支，或切换到 `<name>` 分支。

创建并切换到新分支：

```
git checkout -b <name>
git switch -c <name> # Git 新版本推荐命令
```

> 
> 作用：创建并立即切换到新分支，相当于 `git branch <name>` + `git checkout <name>`。

查看当前所有分支：

```
git branch
```

> 
> 作用：列出所有分支，当前分支前面会标一个 `*` 号。

合并指定分支到当前分支：

```
git merge <name>
```

> 
> 作用：将 `<name>` 分支的修改合并到当前所在的分支上。

禁用 Fast forward 模式合并：

```
git merge --no-ff -m "merge with no-ff" <name>
```

> 
> 作用：强制禁用 Fast forward 模式，Git 会在 merge 时生成一个新的 commit，这样从分支历史上就可以看出分支信息。

删除分支：

```
git branch -d <name>
```

> 
> 作用：删除已经合并过的分支。如果要强制删除一个没有被合并过的分支，使用 `git branch -D <name>`。

> 
> 解决冲突：当两个分支修改了同一个文件的同一处时，合并会产生冲突。需要手动打开冲突文件，修改为正确的内容，然后 `git add` 和 `git commit`。

储藏工作现场：

```
git stash
```

> 
> 作用：把当前工作区的修改（未提交的改动）储藏起来，让工作区变干净，以便切换到其他分支修复 Bug。

查看储藏列表：

```
git stash list
```

> 
> 作用：查看所有储藏的工作现场。

恢复储藏：

```
git stash apply # 恢复，但不删除 stash 内容
git stash pop   # 恢复，并删除 stash 内容
```

> 
> 作用：恢复之前储藏的工作现场。

复制特定的提交到当前分支：

```
git cherry-pick <commit_id>
```

> 
> 作用：把指定的提交复制到当前分支，避免重复劳动（常用于在主分支上修复 Bug 后，将修复提交同步到其他分支）。

---

## 第六章：远程仓库

### 核心概念

本地 Git 仓库和 GitHub/Gitee 等远程仓库之间的传输是通过 SSH 加密的，因此需要配置 SSH Key。
远程仓库的默认名称通常是 origin。

### 重点指令

关联远程仓库：

```
git remote add origin git@server-name:path/repo-name.git
```

> 
> 作用：将本地仓库与远程仓库关联，并把远程仓库命名为 origin。

推送分支到远程：

```
git push -u origin master
```

> 
> 作用：把本地 master 分支推送到远程 origin 的 master 分支。`-u` 参数会把本地的 master 分支和远程的 master 分支关联起来，以后推送或拉取就可以简化命令为 `git push origin master`。

克隆远程仓库：

```
git clone git@server-name:path/repo-name.git
```

> 
> 作用：从远程仓库克隆项目到本地，Git 会自动将远程仓库命名为 origin，并自动关联本地分支。

查看远程库信息：

```
git remote -v
```

> 
> 作用：显示更详细的远程库信息，包括抓取和推送的地址。如果没有推送权限，就看不到 push 地址。

抓取远程分支：

```
git fetch
```

> 
> 作用：从远程仓库获取最新版本到本地，但不会自动合并（比 git pull 更安全）。

拉取远程分支：

```
git pull
```

> 
> 作用：从远程仓库获取最新版本并自动合并到本地当前分支，相当于 `git fetch + git merge`。

推送其他分支到远程：

```
git push origin dev
```

> 
> 作用：推送本地的 dev 分支到远程 origin 的 dev 分支。master 是主分支，需要时刻与远程同步；dev 是开发分支，也需要同步；其他临时分支视情况决定是否推送。

创建本地分支并关联远程分支：

```
git checkout -b dev origin/dev
git switch -c dev origin/dev # 新版本推荐
```

> 
> 作用：在本地创建 dev 分支，并关联远程的 origin/dev 分支。

建立本地分支与远程分支的关联：

```
git branch --set-upstream-to=origin/dev dev
```

> 
> 作用：将本地的 dev 分支与远程的 origin/dev 分支建立链接。如果 git pull 提示 "no tracking information"，则需要用此命令设置关联。