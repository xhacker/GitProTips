# Git 进阶技巧

本文尚未完成。

本文**不**适用于初学者，适合了解 Git 的基本使用，知道 commit、push、pull，希望掌握 Git 更多功能的人阅读。你可以以任何顺序阅读，或者作为一个手册，在遇到问题的时候查询。如果你遇到没有涉及的问题，欢迎 [新建 issue](https://github.com/xhacker/GitProTips/issues) 反馈。我会持续扩充本文。

## Commit

### 如何设置 commit 中的姓名和 email？
```shell
git config --global user.name "Dongyuan Liu"
git config --global user.email "liu.dongyuan@gmail.com"
```

### 如何以其他身份 commit？
```shell
git commit --author "Xhacker <liu.dongyuan@gmail.com>"
```

或

```shell
GIT_COMMITTER_NAME="Xhacker" GIT_COMMITTER_EMAIL="liu.dongyuan@gmail.com" git commit --author "Xhacker <liu.dongyuan@gmail.com>"
```
两者有什么区别呢？事实上，Git 中有两个关于作者的信息，`committer` 和 `author`。第一条命令将使用 ``Xhacker <liu.dongyuan@gmail.com>`` 作为 commit 的 `author`，第二条命令则同时设置 `committer` 和 `author`。

关于 `committer` 和 `author` 的区别，[Pro Git 2.3 章](http://git-scm.com/book/zh/v1/Git-基础-查看提交历史) 中提到：

> 你一定奇怪作者（author）和提交者（committer）之间究竟有何差别，其实作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。所以，当你为某个项目发布补丁，然后某个核心成员将你的补丁并入项目时，你就是作者，而那个核心成员就是提交者。我们会在第五章再详细介绍两者之间的细微差别。

### 如何针对每个 repo 单独设定姓名和 email？
有时，你可能想为不同的 repo（比如公司的项目和个人项目）设置不同的 committer 信息，很简单：

```shell
git config user.name "Dongyuan Liu"
git config user.email "xhacker@ela.build"
```

可以看出，在使用 `git config` 时，默认修改的是当前 repo 的配置，如果添加 `--global` 则会修改全局的配置。

### Commit message 应该怎么写？
Commit message 应简短、清晰地描述这个 commit 中做了什么。如果所有协作者都能阅读中文，则可以使用中文。

正确示例：

> Fix table view cell text overflow

<!-- -->
> 修复了内存泄漏的问题

错误示例：

> fixed some bugs

<!-- -->
> 修改文件

同时，如果你使用 GitHub 管理代码，还可以通过 commit message 关闭 issue，详见 /* TODO: 链接 */。

### 如何将修改追加到上一个 commit？
假设你发现刚刚完成的 commit 中有一处错误，你一定希望把修改追加到上一个 commit 中，而不是创建一个新的 commit。很简单，只需要用 `--amend`：

```shell
git commit --amend
```

在 amend 之后，commit 的时间是不会变的。如果你想更新一下 commit 时间，可以用

```shell
git commit --amend --reset-author
```

需要注意的是，amend 实际上修改了上一个 commit。所以如果已经 push 了上一个 commit，请尽量不要 amend。

如果一定要 amend 已经 push 了的 commit，**请确保这个 commit 所在的 branch 只有你一个人使用**（否则会给其他人带来灾难），然后在 amend 之后使用 `git push --force`。

### 如何 commit 文件的一部分？
```shell
git commit -p
```

之后，Git 会对每块修改弹出一个提示，询问你是否 stage：
```diff
diff --git a/Source/Player.swift b/Source/Player.swift
index af1cb7f..6ee0213 100644
--- a/Source/Player.swift
+++ b/Source/Player.swift
@@ -323,9 +323,7 @@ public class Player: UIViewController {
     }
 
     private func setupPlayerItem(playerItem: AVPlayerItem?) {
-        let item = playerItem
-
-        if item == nil {
+        if self.playerItem != nil {
             self.playerItem?.removeObserver(self, forKeyPath: PlayerEmptyBufferKey, context: &PlayerItemObserverContext)
             self.playerItem?.removeObserver(self, forKeyPath: PlayerKeepUp, context: &PlayerItemObserverContext)
             self.playerItem?.removeObserver(self, forKeyPath: PlayerStatusKey, context: &PlayerItemObserverContext)
Stage this hunk [y,n,q,a,d,/,j,J,g,e,?]?
```
按 `y`/`n` 来选择是否 commit 这块修改，`?` 可以查看其他操作的说明。

### 什么是 stage？
在 Git 中，有一个 staging area 的概念，你可以理解为一个暂存区。在运行 `git commit` 时，只有在 staging area 里的修改会被 commit。在工程量比较大时，staging area 可以帮助你提交一部分修改。通过 `git add 文件名` 可以 stage 一个文件；`git add -p` 可以 stage 文件的一部分，用法和之前介绍的 `git commit -p` 类似。

### 如何查看当前修改了哪些文件？
通过 `git status`，你可以查看所有未提交文件的状态。最上面显示的是在 staging area，即将被 commit 的文件；中间显示没有 stage 的修改了的文件，最下面是新的还没有被 Git track 的文件：
```
On branch fix-kvo-crash
Your branch is behind 'origin/fix-kvo-crash' by 1 commit, and can be fast-forwarded.
  (use "git pull" to update your local branch)
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   Source/InboardHelper.swift

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   Source/InboardHelperDelegate.swift

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    SyncManager.swift
```

`git diff` 可以查看当前没有 stage 的修改，`git diff --staged` 可以查看在 staging area 中的修改。

### 如何查看某个特定的 commit 修改了哪些文件？
要查看某个 commit 的修改，可以用 `git show`：
```shell
# 查看最后一个 commit 的修改
git show

# 查看倒数第三个 commit 的修改
git show HEAD~3

# 查看 hash 为 deadbeef 的 commit 的修改
git show deadbeef
```

### Commit 的 hash 到底有多长？
Git commit 的 hash 是对 commit 内容的 SHA-1 checksum，通常用 40 位 16 进制数表示，比如 `a502950cd563f2ed210d6610bf5d82f72827ea19`。然而，只要没有冲突，你通常可以用一个比较短的前缀来表示一个 commit。比如 `git show a50295`，`git checkout a50295`。

### 如何 push 一部分 commit？
有时你在本地做了很多个 commit，却只想 push 其中的前几个，这时只要：

```shell
git push <remotename> <commit SHA>:<remotebranchname>

# push 9790eff 之前的所有 commit 到 master
git push origin 9790eff:master
```

### 如何 commit 一个空目录？
Git 本身不支持 track 空目录。然而，你可以建一个空的隐藏文件来解决这个问题。比如，要想 commit 一个空的 output 目录，可以：

```shell
mkdir output
touch output/.keep
git add output/.keep
```

### 如何忽略文件？
在项目根目录建立 `.gitignore` 可以忽略文件：

```ini
# 忽略所有名为 .DS_Store 的文件
.DS_Store

# 忽略所有扩展名为 .o 的文件
*.o
```

`.gitignore` 中还支持很多其他语法，具体可以参见 [gitignore 文档](http://git-scm.com/docs/gitignore)。

### 什么文件应该忽略，什么文件不应该？

一般来说，临时文件和后期生成的文件应该忽略掉。比如 OS X 上的 `.DS_Store`、Windows 上的 `Thumbs.db`、C 语言项目中的 `*.o` 等等。在 GitHub 新建项目时，可以选择自动添加合适的 `.gitignore`。

### 如何仅在本地忽略文件？

有时你想忽略一些自己的文件，却不想污染 repo 中的 `.gitignore`，也可以修改 `.git/info/exclude` 文件。语法和 `.gitignore` 一样。

## Branch

### 如何新建 branch？
```shell
git branch <branchname>
git checkout -b <branchname>
```

### 如何删除 branch？
```shell
git checkout -d <branchname>
```

### 如何切换到上一个 branch？
```shell
git checkout <branchname>
git checkout -
```

### 如何应用其他 branch 的某个 commit？
```shell
git cherry-pick 5130058
```

### merge 和 rebase 有什么区别？

### 如何重置到某个 commit？
```shell
git reset HEAD~1
```

### 如何查看已经 merge 的 branch？
```shell
git branch --merged
git branch --no-merged
git branch --merged | xargs git branch -d
```

## 后悔药

### 如何重置某个文件到未修改的状态？
```shell
git checkout -- <filepath>
```

### 如何重置所有文件到未修改的状态？
```shell
git reset --hard
```

### 如何删除所有新增的文件？

### 如何还原某个 commit？
```shell
git revert
```

### 如何修改最后一个 commit？
```shell
git reset --soft
```

### 如何删除某个 commit？如何修改某个 commit 的 message？如何变换两个 commit 的顺序？如何把一个 commit 拆成多个？如何追加内容到以前的 commit？
删除和还原有什么区别？
```shell
git rebase -i master
git rebase -i 22e21f2
git rebase -i HEAD~3
```
[more explaination]

already pushed?
mention git push -f

### 我把一个 commit 彻底删掉了，还能恢复吗？
```shell
git reflog
git log HEAD@{6}\n: 1415211713:0;git log HEAD@{6}
git reflog HEAD@{6}
git reset --hard HEAD@{6}
```

## 协作与 GitHub

### 别人 push 了修改，我无法 push 了，怎么办？
```shell
git fetch
git rebase origin/master
```

### 如何用 commit 关闭 GitHub issue？

### 如何给开源项目提交 pull request？

### 如何跟进开源项目上游的更改？

## 黑魔法

### 发现了一个 bug，如何知道是哪个 commit 导致的？
```shell
git biselect
```

### 如何让 Git 的命令更短？
.gitconfig
.zshrc

### 如何在 shell 中更好地使用 Git？

## 附录

### 好用的 Git 客户端

### .gitconfig

```ini
[alias]
    co = checkout
    st = status
    lg = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
    remove-untracked-files = clean -f -d
    ignore = update-index --assume-unchanged
    unignore = update-index --no-assume-unchanged
    ignored = !git ls-files -v | grep "^[[:lower:]]"
[diff]
    algorithm = patience
[color]
    ui = on
```
