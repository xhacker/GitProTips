# Git Pro Tips

This is *not* a tutorial for beginner. If you know `commit`, `push`, `pull`, and want to master more Git features, it’s for you. You can read it in any order, or use it as a handbook, or refer to it when you encounter problems.

## Commit

### How to configure the name and email in commits?

```shell
git config --global user.name "Dongyuan Liu"
git config --global user.email "liu.dongyuan@gmail.com"
```

### How to commit using another identity?

```shell
git commit --author "Xhacker <liu.dongyuan@gmail.com>"
```

or

```shell
GIT_COMMITTER_NAME="Xhacker" GIT_COMMITTER_EMAIL="liu.dongyuan@gmail.com" git commit --author "Xhacker <liu.dongyuan@gmail.com>"
```

What’s the difference? In fact, there are two kinds of information about the author, `committer` and `author`. The first command uses `Xhacker <liu.dongyuan@gmail.com>` as the `author` of the commit. The second one sets both `committer` and `author`.

About the difference, it’s mentioned in [Pro Git Chapter 2.3](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History):

> You may be wondering what the difference is between *author* and *committer*. The author is the person who originally wrote the work, whereas the committer is the person who last applied the work. So, if you send in a patch to a project and one of the core members applies the patch, both of you get credit – you as the author, and the core member as the committer. We’ll cover this distinction a bit more in [Distributed Git](https://git-scm.com/book/en/v2/1-distributed-git/_distributed_git).

### How to use different identities for different repos?

Sometimes you are working on both personal work and company work. You may want to set different committer information for them, it’s easy:

```shell
git config user.name "Dongyuan Liu"
git config user.email "xhacker@ela.build"
```

As you can see, when you `git config` you are modifying the config for the current repo. If you add `--global`, you can modify the global config.

### How to write commit message?

Commit messages describe what you have done in commits. They should be short and distinct. Please use *simple present tense*.

Good examples:

> Fix table view cell text overflow

<!-- -->
> Fix a memory leak

Bad examples:

> fixed some bugs

<!-- -->
> update file

Meanwhile, if you are using GitHub, you can close issues via commit messages ([official documentation](https://help.github.com/articles/closing-issues-via-commit-messages/)), like:

> [Close #40] Send Dribbble shots from XPC service in batches

### How to append modifications to last commit?
Say you realized there’s a mistake in the commit you’ve just made. Instead of creating a new commit, you may want to append the simple fix to the last commit. Just use `--amend`:

```shell
git commit --amend
```

Keep in mind that amending will not change the time information in the commit. If you want to update the time in the commit, use

```shell
git commit --amend --reset-author
```

Beware that `amend` actually modified the last commit. If you had already pushed the last commit, please don’t use `amend`.

If you insisted on amending a pushed commit, *please make sure that you are the only one using the branch* (or it’s a disaster for other contributors), then use `git push --force` after amending.

### How to commit part of a file?

```shell
git commit -p
```

Then, Git will prompt for each hunk of the modification, ask if you want to stage:

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

Use `y`/`n` to choose whether or not to commit this hunk, `?` to show help for other operations.

### What is `stage`?

Git has the concept of staging area. It’s like a temporary area. When you run `git commit`, only modifications in the staging area will be committed. If you are making a huge change, staging area could help you to commit a part of the change. You can stage a file via `git add <filename>`; You can also stage part of a file via `git add -p` (similar to `git commit -p`).

### How to check which files were modified?

`git status` shows the status of uncommitted files. It shows files in stating area (to be committed) at the top; modified but not stated at the middle; untracked files at the bottom:

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

`git diff` shows the unstated modifications. `git diff --staged`  shows the modifications in staging area.

### How to check which files had been modified in a certain commit?

To check the modifications in a commit, use `git show`:

```shell
# Show the modifications in the last commit
git show

# Show the modifications in the third commit before last
git show HEAD~3

# Show the modifications in commit ｀deadbeef｀
git show deadbeef
```

### What does HEAD~3 mean?
`HEAD` is a pointer to the last commit, `HEAD~3` is the third commit before the last.

### How long is commit’s hash?
In Git, hash is a SHA-1 checksum for the content of the commit. It usually represented by a 40-digit hex number, e.g. `a502950cd563f2ed210d6610bf5d82f72827ea19`. However, as long as there’s no conflicts, you can use a shorter prefix to represent a commit. For example, `git show a50295`, `git checkout a50295`.

## Branch

### How to create a new branch?
There are two ways to create a new branch:

```shell
# Create a new branch branch
git branch <branchname>

# Create and switch to the new branch (commonly used)
git checkout -b <branchname>
```

### How to remove a branch?

```shell
git branch -d <branchname>
```

### How to switch to the last branch?

```shell
git checkout <branchname>

# Switch to the last branch
git checkout -
```

Similar to `cd -`, isn’t it?

## Medicine for Regret

### How to reset a file to its unmodified status?

```shell
git checkout -- <filepath>
```

### How to reset all the files to their unmodified status?

```shell
git reset --hard
```

### How to revert a commit?

```shell
git revert <commit SHA>
```

You may ask what’s the difference between `revert` and `reset`. The essence of `revert` is to create a new commit which is the exact opposite of the reverted commit. For example, `A` commit added three lines in `main.c`, reverting `A` would create a new commit to remove the three lines. If we are very sure that a previous commit caused a bug, the best way is to revert it.

After running `git revert`, git will prompt you to write a commit message. You’d better briefly describe the reason for the revert.

```
Revert "Use DDT to kill insects" because it has so many side effects

This reverts commit 4281ac1c58e21194b80a327af93d47c5fefb786f.
```

But `reset` will modify the history. It usually used by local commits that haven’t been pushed.

### How to modify the last commit?

```shell
git reset --soft
```

## Collaboration and GitHub

### Some one else pushed a commit. I cannot push. What should I do?

```shell
git fetch
git rebase origin/master
```

### How to close GitHub issue via commits?
You can close GitHub issues via commit messages. You have to use  `close`, `fix`, or other special words in the commit message, see [Closing issues via commit messages](https://help.github.com/articles/closing-issues-via-commit-messages/) for details.

For example, issue 42 can be closed by either of the following two commit messages:

> [Fix #42] Fix table view cell text overflow

<!-- -->
> Fix table view cell text overflow, close #42

## Black Magic

### I found a regression. How can I know which commit caused the bug?

```shell
git bisect
```

## Appendix

### Awesome Git GUI Clients

The debate between CLI and GUI never ends. In my opinion, the most efficient way to use Git is the cooperation between a good GUI client and CLI. Before pushing the commit, carefully examine its content is a good habit. In a GUI client, you can see the modifications clearly. Another typical use case of GUI is to commit a part of a file. In this case, GUI is way more intuitive than `git add -p`.

* [GitHub Desktop](https://desktop.github.com) (free): Supports Mac and Windows. Highly recommended. It doesn’t have a lot of features, but it’s well-organized.
* [SourceTree](https://www.sourcetreeapp.com) (free): Supports Mac and Windows. Compared to GitHub Desktop, it’s more feature-rich. The UI is a bit messy in my point of view.
* [Tower](http://www.git-tower.com) (paid): The client I’m currently using. It’s expensive, but it’s feature-rich and very easy to use.
