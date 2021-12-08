---
title: 图解 Git 各种用法，简单明了，一目了然！
urlname: vfv2ou
date: '2021-09-09 16:28:17 +0800'
tags: git
categories: git图解
---

## 基本用法

![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176101608-a7f494c9-dd3e-40f1-a241-31a695f2c99b.png#clientId=ue81bae5b-8541-4&from=paste&id=u3dfc8039&margin=%5Bobject%20Object%5D&name=image.png&originHeight=340&originWidth=794&originalType=url∶=1&size=24792&status=done&style=none&taskId=ue8ceaf50-4e6c-4e0c-8deb-ca04c0ea0d6)
上面的四条命令在工作目录、暂存目录(也叫做索引)和仓库之间复制文件。

- git add _files_ 把当前文件放入暂存区域。
- git commit 给暂存区域生成快照并提交。
- git reset -- _files_ 用来撤销最后一次 git add _files_，你也可以用 git reset 撤销所有暂存区域文件。
- git checkout -- _files_ 把文件从暂存区域复制到工作目录，用来丢弃本地修改。

你可以用 git reset -p, git checkout -p, or git add -p 进入交互模式。
也可以跳过暂存区域直接从仓库取出文件或者直接提交代码。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176102432-f7afcafc-3b9c-4ff8-8d98-ab649727dd61.png#clientId=ue81bae5b-8541-4&from=paste&id=uf0d0f783&margin=%5Bobject%20Object%5D&name=image.png&originHeight=340&originWidth=945&originalType=url∶=1&size=27435&status=done&style=none&taskId=u31c3d441-6a8e-4ea4-901d-fa13b771847)
img

- git commit -a 相当于运行 git add 把所有当前目录下的文件加入暂存区域再运行。git commit.
- git commit _files_ 进行一次包含最后一次提交加上工作目录中文件快照的提交。并且文件被添加到暂存区域。
- git checkout HEAD -- _files_ 回滚到复制最后一次提交。

## 约定

后文中以下面的形式使用图片。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176102515-69b4e326-e307-4bb4-84a6-b19dd7d697b0.png#clientId=ue81bae5b-8541-4&from=paste&id=ub544f45d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=529&originWidth=907&originalType=url∶=1&size=50161&status=done&style=none&taskId=u8d98f284-e093-48e7-83de-13544d3a4e5)
[绿色的 5 位字符表示提交的 ID，分别指向父节点。分支用橘色显示，分别指向特定的提交。当前分支由附在其上的 HEAD 标识。这张图片里显示最后 5 次提交，ed489 是最新提交。main 分支指向此次提交，另一个 stable 分支指向祖父提交节点。](http://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==∣=2247531800&idx=2&sn=fbf496a2626d9d550c4ae95bb4f60c11&chksm=eb50f82edc2771386229a5bb81d865eee375f8ecbec665494417beb8c933260114d67b34c3a0&scene=21#wechat_redirect)

## 命令详解

### Diff

有许多种方法查看两次提交之间的变动。下面是一些示例。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176102164-cd673fbd-d2ab-464b-ba47-ece7b90c038d.png#clientId=ue81bae5b-8541-4&from=paste&id=udc5ccc89&margin=%5Bobject%20Object%5D&name=image.png&originHeight=529&originWidth=907&originalType=url∶=1&size=49032&status=done&style=none&taskId=ud227aa0b-c127-44a1-add2-5d741379219)

### Commit

提交时，git 用暂存区域的文件创建一个新的提交，并把此时的节点设为父节点。然后把当前分支指向新的提交节点。下图中，当前分支是*main*。在运行命令之前，*main*指向*ed489*，提交后，*main*指向新的节点*f0cec*并以*ed489*作为父节点。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176102659-1e486b54-9042-4c9f-b68e-0ddc8bce33a7.png#clientId=ue81bae5b-8541-4&from=paste&id=u9c0f2015&margin=%5Bobject%20Object%5D&name=image.png&originHeight=529&originWidth=907&originalType=url∶=1&size=37303&status=done&style=none&taskId=u09cafa52-c7b7-4638-bff8-efb9d444d39)
即便当前分支是某次提交的祖父节点，git 会同样操作。下图中，在*main*分支的祖父节点*stable*分支进行一次提交，生成了*1800b*。这样，*stable*分支就不再是*main*分支的祖父节点。此时，合并 (或者 衍合) 是必须的。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176102757-3b8feb81-e9bb-4571-b37a-6c53c6b55efe.png#clientId=ue81bae5b-8541-4&from=paste&id=uba04764e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=529&originWidth=907&originalType=url∶=1&size=40145&status=done&style=none&taskId=u2d9449a5-8c21-4d05-8252-e55c06f30ea)
如果想更改一次提交，使用 git commit --amend。git 会使用与当前提交相同的父节点进行一次新提交，旧的提交会被取消。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176103367-1d622952-2628-4cda-901d-b255f5d29136.png#clientId=ue81bae5b-8541-4&from=paste&id=u990f6504&margin=%5Bobject%20Object%5D&name=image.png&originHeight=529&originWidth=907&originalType=url∶=1&size=39718&status=done&style=none&taskId=uc6a75827-1740-47f6-a3d7-c4dfedac56c)
另一个例子是分离 HEAD 提交,后文讲。另外，Git 系列面试题整理好了，微信搜索 Java 技术栈，在后台发送：面试，面试题非常全。

### Checkout

checkout 命令用于从历史提交（或者暂存区域）中拷贝文件到工作目录，也可用于切换分支。
当给定某个文件名（或者打开-p 选项，或者文件名和-p 选项同时打开）时，git 会从指定的提交中拷贝文件到暂存区域和工作目录。比如，git checkout HEAD~ foo.c 会将提交节点*HEAD~*(即当前提交节点的父节点)中的 foo.c 复制到工作目录并且加到暂存区域中。（如果命令中没有指定提交节点，则会从暂存区域中拷贝内容。）注意当前分支不会发生变化。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176103354-4f713b4a-8a6b-40ad-9e44-21cfa70999bb.png#clientId=ue81bae5b-8541-4&from=paste&id=u41d6de0d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u21ab8567-ccbc-4c42-8d3b-7b6d04f7c12)
当不指定文件名，而是给出一个（本地）分支时，那么*HEAD*标识会移动到那个分支（也就是说，我们“切换”到那个分支了），然后暂存区域和工作目录中的内容会和*HEAD*对应的提交节点一致。新提交节点（下图中的 a47c3）中的所有文件都会被复制（到暂存区域和工作目录中）；只存在于老的提交节点（ed489）中的文件会被删除；不属于上述两者的文件会被忽略，不受影响。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176103456-1fb6ffdb-a8a5-4b0a-8a93-92ca0eaa8680.png#clientId=ue81bae5b-8541-4&from=paste&id=u4c6b4574&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=uf9658378-33e5-4519-b3e0-b28bdb9e51f)
如果既没有指定文件名，也没有指定分支名，而是一个标签、远程分支、SHA-1 值或者是像*main~3*类似的东西，就得到一个匿名分支，称作*detached HEAD*（被分离的*HEAD*标识）。
这样可以很方便地在历史版本之间互相切换。比如说你想要编译 1.6.6.1 版本的 git，你可以运行 git checkout v1.6.6.1（这是一个标签，而非分支名），编译，安装，然后切换回另一个分支，比如说 git checkout main。然而，当提交操作涉及到“分离的 HEAD”时，其行为会略有不同，详情见在下面。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176103601-c8c4df19-fd6d-484c-91e2-81ccb312cee7.png#clientId=ue81bae5b-8541-4&from=paste&id=u9a2db190&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u03112e09-110e-455d-bd51-ae1184d9d8f)

### HEAD 标识处于分离状态时的提交操作

当*HEAD*处于分离状态（不依附于任一分支）时，提交操作可以正常进行，但是不会更新任何已命名的分支。(你可以认为这是在更新一个匿名分支。)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176103848-82749fdc-fcfe-4aea-afd1-e021f63f628b.png#clientId=ue81bae5b-8541-4&from=paste&id=u49ff2dba&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u38e9fa82-9572-4b68-b4cb-afd21df210a)
一旦此后你切换到别的分支，比如说*main*，那么这个提交节点（可能）再也不会被引用到，然后就会被丢弃掉了。注意这个命令之后就不会有东西引用*2eecb*。分享给你：[Spring Boot 学习笔记](http://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==∣=2247531800&idx=2&sn=fbf496a2626d9d550c4ae95bb4f60c11&chksm=eb50f82edc2771386229a5bb81d865eee375f8ecbec665494417beb8c933260114d67b34c3a0&scene=21#wechat_redirect)。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176103955-0fee38c5-a3b0-40b9-8c31-22dd6c551588.png#clientId=ue81bae5b-8541-4&from=paste&id=u21e809fc&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=uac6d2e80-fcf3-4743-9f81-8ae34bd9790)
但是，如果你想保存这个状态，可以用命令 git checkout -b *name*来创建一个新的分支。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104045-80c3fc5e-f54a-47c4-aef6-f2f4b7af973e.png#clientId=ue81bae5b-8541-4&from=paste&id=uef5a452c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u68f1fc11-6e22-4845-83f9-aa6f7d6001a)

### Reset

reset 命令把当前分支指向另一个位置，并且有选择的变动工作目录和索引。也用来在从历史仓库中复制文件到索引，而不动工作目录。
如果不给选项，那么当前分支指向到那个提交。如果用--hard 选项，那么工作目录也更新，如果用--soft 选项，那么都不变。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104160-8487aeac-bb81-4858-80b1-588b6cfeaec4.png#clientId=ue81bae5b-8541-4&from=paste&id=u16342c63&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u44149ff3-450e-43f1-932d-bfbce045a4f)
如果没有给出提交点的版本号，那么默认用*HEAD*。这样，分支指向不变，但是索引会回滚到最后一次提交，如果用--hard 选项，工作目录也同样。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104349-6a1f2180-6bde-4193-8102-de1468564ff6.png#clientId=ue81bae5b-8541-4&from=paste&id=u4e67063c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u72100c88-b175-497f-9506-16e266dd8ea)
如果给了文件名(或者 -p 选项), 那么工作效果和带文件名的 checkout 差不多，除了索引被更新。Java 核心技术教程和示例源码：<https://github.com/javastacks/javastack>
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104342-c920fcd5-072c-4e0f-b0b8-997917154f36.png#clientId=ue81bae5b-8541-4&from=paste&id=u880dc6b7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u803dfb5b-1a2b-4397-a5ff-018b7781a55)

### Merge

[merge 命令把不同分支合并起来。合并前，索引必须和当前提交相同。如果另一个分支是当前提交的祖父节点，那么合并命令将什么也不做。另一种情况是如果当前提交是另一个分支的祖父节点，就导致 fast-forward 合并。指向只是简单的移动，并生成一个新的提交。](http://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==∣=2247529043&idx=2&sn=ccbb24c037bbe434e86779c4206f0040&chksm=eb50f765dc277e7328851c2d884e557c362c8cbfdfea922fd49b2cbacffc4ab10034c784b82c&scene=21#wechat_redirect)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104526-cb06c7ec-a5d1-4397-b4f2-3b057adef8e9.png#clientId=ue81bae5b-8541-4&from=paste&id=u78296f2b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u2e723ee9-e70c-440c-82df-2d15135df57)
否则就是一次真正的合并。默认把当前提交(_ed489_ 如下所示)和另一个提交(_33104_)以及他们的共同祖父节点(_b325c_)进行一次三方合并。结果是先保存当前目录和索引，然后和父节点*33104*一起做一次新提交。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104549-ee6aa7e5-491d-4480-a364-ea6d9e119722.png#clientId=ue81bae5b-8541-4&from=paste&id=u7a7ae37a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=uab94da9e-fc40-4231-95c0-76dab77a23a)

### Cherry Pick

cherry-pick 命令"复制"一个提交节点并在当前分支做一次完全一样的新提交。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104739-24c887ef-026f-44dd-b2c8-2aaa19658a5f.png#clientId=ue81bae5b-8541-4&from=paste&id=u5170ec65&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u66d367fe-16a4-4623-b772-ccc6b570cac)

### Rebase

[衍合是合并命令的另一种选择。合并把两个父分支合并进行一次提交，提交历史不是线性的。衍合在当前分支上重演另一个分支的历史，提交历史是线性的。本质上，这是线性化的自动的 cherry-pick](http://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==∣=2247531800&idx=2&sn=fbf496a2626d9d550c4ae95bb4f60c11&chksm=eb50f82edc2771386229a5bb81d865eee375f8ecbec665494417beb8c933260114d67b34c3a0&scene=21#wechat_redirect)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104736-8bba8067-f186-4524-9428-937e6723b8e6.png#clientId=ue81bae5b-8541-4&from=paste&id=ud583a3a4&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=ub005a18e-27b2-4baa-a77e-722fd3bb97b)
上面的命令都在*topic*分支中进行，而不是*main*分支，在*main*分支上重演，并且把分支指向新的节点。注意旧提交没有被引用，将被回收。
要限制回滚范围，使用--onto 选项。下面的命令在*main*分支上重演当前分支从*169a6*以来的最近几个提交，即*2c33a*。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104893-7ad9b45c-dba2-4b4c-8653-79007385d2b2.png#clientId=ue81bae5b-8541-4&from=paste&id=u7b9446ac&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=u6634deaa-a4fd-43a0-8bcc-e5e4f052428)
同样有 git rebase --interactive 让你更方便的完成一些复杂操作，比如丢弃、重排、修改、合并提交。

## 技术说明

[文件内容并没有真正存储在索引(.git/index)或者提交对象中，而是以 blob 的形式分别存储在数据库中(.git/objects)，并用 SHA-1 值来校验。索引文件用识别码列出相关的 blob 文件以及别的数据。对于提交来说，以树(tree)的形式存储，同样用对于的哈希值识别。树对应着工作目录中的文件夹，树中包含的 树或者 blob 对象对应着相应的子目录和文件。每次提交都存储下它的上一级树的识别码。](http://mp.weixin.qq.com/s?__biz=MzI3ODcxMzQzMw==∣=2247529043&idx=2&sn=ccbb24c037bbe434e86779c4206f0040&chksm=eb50f765dc277e7328851c2d884e557c362c8cbfdfea922fd49b2cbacffc4ab10034c784b82c&scene=21#wechat_redirect)
如果用 detached HEAD 提交，那么最后一次提交会被 the reflog for HEAD 引用。但是过一段时间就失效，最终被回收，与 git commit --amend 或者 git rebase 很像。

\*\*

![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104882-f5314989-c8cc-436a-b2d7-2e703d44f962.png#clientId=ue81bae5b-8541-4&from=paste&id=uff744f07&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=uea95b21f-fd48-4ca5-a268-b76d2ab00bd)
\*\*

![image.png](https://cdn.nlark.com/yuque/0/2021/png/12838787/1631176104940-a79386a1-8079-4187-8448-2cecf36a7ce3.png#clientId=ue81bae5b-8541-4&from=paste&id=ud0df427e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1&originWidth=1&originalType=url∶=1&size=68&status=done&style=none&taskId=ubabd9673-e2cf-46e7-9812-c1f1bbd6ef1)
