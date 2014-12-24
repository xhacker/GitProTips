# Git 进阶技巧

本文尚未完成。

本文**不**适用于初学者，适合了解 Git 的基本使用，知道 commit、push、pull，希望掌握 Git 更多功能的人阅读。你可以以任何顺序阅读，或者作为一个手册，在遇到问题的时候查询。如果你遇到没有涉及的问题，欢迎[新建 issue](https://github.com/xhacker/GitProTips/issues) 反馈。我会持续扩充本文。

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
committer vs. author

### Commit log 应该怎么写？

### 如何将修改追加到上一个 commit？
```shell
git commit --amend
git commit --amend --reset-author
```
already pushed?

### 如何 commit 文件的一部分？
```shell
git commit -p
```

### 什么是 stage？

### 如何查看修改了哪些文件？
```shell
git status
git diff
git diff --staged
```

### 如何查看某个特定的 commit 修改了哪些文件？
```shell
git show
git show HEAD~1
git show 9790eff
```

### Commit 的 hash 到底有多长？

### 如何 push 一部分 commit？
```shell
git push <remotename> <commit SHA>:<remotebranchname>
git push origin 9790eff:master
```

### 如何 commit 一个空目录？
```shell
git add output/.keep
```

### 如何忽略文件？
.gitignore

### 什么文件应该 commit，什么文件不应该？

### 如何仅在本地忽略文件？

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
