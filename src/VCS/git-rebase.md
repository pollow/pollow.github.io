Title: [Brief Note] Git rebase 小结
Tags: brief,git

## Githug

前几天再 Timeline 上看到有人推荐了一款叫做 Githug 的小游戏，通过解决任务学习 Git 的操作使用。由于感觉自己对于 Git 的使用一直停留在比较初级的阶段，有比较多的知识盲点，所以决定下载下来玩一玩，顺便把[题解]()和学习笔记分别记录一下。

## Pro Git

开始的内容比较简单也比较顺利，在 level 28 第一次遇到了问题——涉及到了rebase。这个功能虽然之前也有使用，但是对于概念一直有所困惑，正巧同时再 Startup News 上看到[《Pro Git 2ed》中文版](http://git-scm.com/book/zh)翻译校对完成，就顺手上去看了一下有关章节，在这里简略记录一些内容。

## rebase

推荐阅读：[3.6 Git 分支 - 变基](http://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)

Git 的分支合并有 `merge` 和 `rebase` 两种操作。区别在于 merge 只简单的完成代码合并，而 rebase 则是提取两个不同分支（源分支与目标分支）的公共祖先作为基底，生成源分支的 patch 再应用到目标分支上，同时清理冗余的提交。

概念其实比较简单，只要注意应用到合适的场合，也就是清理冗余提交信息时，就比较合适，原文讲解的十分清晰。

有一点要注意的是，rebase 操作应当**仅应用到本地仓库的提交记录整理**上，如果 rebase 了远程的提交和分支，则会导致所有参与开发的人得分支依赖变得一片混乱，**『人民群众会仇恨你，你的朋友和家人也会嘲笑你，唾弃你』。**

当然也有解决方案，当公共仓库的提交已经发生 rebase，也就是说本地分支依赖的提交可能已经被删除的时候，不要直接 `git pull`，而是取到本地后执行 rebase 操作，或者直接：

```
git pull --rebase
```

Git 的操作规范推荐每次`git fetch`下来以后视情况决定 merge 和 pull 操作，而我实际上习惯每次直接执行`git pull`操作。对于这种情况，有一个全局设定可以将`git pull`操作默认执行`--rebase`：

```
git config --global pull.rebase true
```

## merge or rebase

今天还在阮一峰的博客上看到了[《Commit message 和 Change log 编写指南》](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)这篇文章，介绍了 commit 信息的编写规范，和利用 commit 信息直接生成 Change log 的技术和相应地工具。个人认为这种规范的制定十分合理，在大型系统维护时，追溯版本管理记录是检阅 bug 的最佳方式，而清晰明了富有意义的提交信息无疑提供了更丰富的线索。

但是本地分支开发时，通常要频繁的进行提交，确保工作记录可以追溯，方便测试和调试，产生的提交记录往往十分散乱并且意义不明，在开发时这些提交有积极地意义，但却是后期的维护时沉重的负担。所以我个人倾向于在分支合并时进行 rebase 操作，时提交信息简单易读又清晰明了。

## Solution for level 28

Githug level 28 的任务提示如下：

> Your local master branch has diverged from the remote origin/master branch. Rebase your commit onto origin/master and push it to remote.

这就是最不应该发生的 **rebase 了公共仓库提交**的情况，按照前文所述的

```
git pull --rebase
```

拉取数据，之后直接`git push`即可。
