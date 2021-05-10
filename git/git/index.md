# 设置全局账号

```
# 显示当前的Git配置
$ git config --list

# 编辑Git配置文件
$ git config -e [--global]

#设置提交代码时的用户信息
$ git config --global user.name "DongGe"
$ git config --global user.email "donggefor2018@163.com"
$ git config --global credential.helper store #存储
```

# git stash 用法

```
# 执行存储时，添加备注，方便查找，只有git stash 也要可以的，但查找时不方便识别
$ git stash save "save message"

# 查看stash了哪些存储
$ git stash list

# 显示做了哪些改动，默认show第一个存储,如果要显示其他存贮，后面加stash@{$num}，比如第二个 git stash show stash@{1}
$ git stash show

# 显示第一个存储的改动，如果想显示其他存存储，命令：git stash show  stash@{$num}  -p ，比如第二个：git stash show  stash@{1}  -p
$ git stash show -p

# 应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即stash@{0}，如果要使用其他个，git stash apply stash@{$num} ， 比如第二个：git stash apply stash@{1} 
$ git stash apply

# 命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1}
$ git stash pop

# 丢弃stash@{$num}存储，从列表中删除这个存储
$ git stash drop stash@{$num}

# 删除所有缓存的stash
$ git stash clear
```

**注意**：==新增的文件，需要先 git add 后才能加入暂存区==

# git push -u 用法

```
$ git push -u origin release
```

> 把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。

> 由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

# 新建代码仓库

```
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [url]
```

# 增加/删除文件

```
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
$ git add -p

# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

# 代码提交 git commit

```
#不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
$ git commit --mixed HEAD^ 
 
#不删除工作空间改动代码，撤销commit，不撤销git add . 
$ git commit --soft HEAD^
 
#删除工作空间改动代码，撤销commit，撤销git add . ,注意完成这个操作后，就恢复到了上一次的commit状态 
$ git commit --hard HEAD^

#修改 commit 注释
$ git commit --amend 进入 VIM 编辑器修改保存

# 提交暂存区到仓库区
$ git commit -m [message]

# 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```

# 分支操作

```
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 新建一个分支，指向指定commit
$ git branch [branch] [commit]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 切换到上一个分支
$ git checkout -

# 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 选择一个commit，合并进当前分支
$ git cherry-pick [commit]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

# 标签

```
# 列出所有tag
$ git tag

# 新建一个tag在当前commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 查看tag信息
$ git show [tag]

# 提交指定tag
$ git push [remote] [tag]

# 提交所有tag
$ git push [remote] --tags

# 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```

# 查看信息

```
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示commit历史，以及每次commit发生变更的文件
$ git log --stat

# 搜索提交历史，根据关键词
$ git log -S [keyword]

# 显示某个commit之后的所有变动，每个commit占据一行
$ git log [tag] HEAD --pretty=format:%s

# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
$ git log [tag] HEAD --grep feature

# 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]
$ git whatchanged [file]

# 显示指定文件相关的每一次diff
$ git log -p [file]

# 显示过去5次提交
$ git log -5 --pretty --oneline

# 显示所有提交过的用户，按提交次数排序
$ git shortlog -sn

# 显示指定文件是什么人在什么时间修改过
$ git blame [file]

# 显示暂存区和工作区的差异
$ git diff

# 显示暂存区和上一个commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示两次提交之间的差异
$ git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"

# 显示某次提交的元数据和内容变化
$ git show [commit]

# 显示某次提交发生变化的文件
$ git show --name-only [commit]

# 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]

# 显示当前分支的最近几次提交
$ git reflog
```

# 撤销

```
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```

# 压缩

```
# 生成一个可供发布的压缩包
$ git archive
```

# git pull

```
在 config 中设置 rebase 选项，使得 git pull 使用 rebase 整合所做修改
git config --global pull.rebase true
git config --global branch.autoSetupRebase always
pull.rebase：git pull 时使用 rebase 而非 merge 来整合修改；
branch.autoSetupRebase：对所有的 tracking branches 都设置 rebase；
在 git config 时加上 --global 选项，对所有 repo 起作用；
```

# 分支命名规范

- master 分支

  - master 为主分支，也是用于部署生产环境的分支，确保master分支稳定性
  - master 分支一般由develop以及hotfix分支合并，任何时间都不能直接修改代码

- develop 分支

  - develop 为开发分支，始终保持最新完成以及bug修复后的代码
  - 一般开发的新功能时，feature分支都是基于develop分支下创建的

- feature 分支

  - 开发新功能时，以develop为基础创建feature分支
  - 分支命名: feature/ 开头的为特性分支， 命名规则: feature/user_module、 feature/cart_module

- release分支

  - release 为预上线分支，发布提测阶段，会release分支代码为基准提测

  - 当有一组feature开发完成，首先会合并到develop分支，进入提测时，会创建release分支。
  - 如果测试过程中若存在bug需要修复，则直接由开发者在release分支修复并提交。
  - 当测试完成之后，合并release分支到master和develop分支，此时master为最新代码，用作上线。

- hotfix 分支

  - 分支命名: hotfix/ 开头的为修复分支，它的命名规则与 feature 分支类似
  - 线上出现紧急问题时，需要及时修复，以master分支为基线，创建hotfix分支，修复完成后，需要合并到master分支和develop分支
  - 
  
# 当 git pull 碰到拒绝合并无关历史

> git pull origin master 时就出现了这样一条错误：

```
fatal: refusing to merge unrelated histories  // 拒绝合并无关历史
```

> 解决办法

```
git pull origin master --allow-unrelated-histories 
```

