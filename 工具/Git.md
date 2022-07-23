


## git命令汇总

- git config 配置
- git log 显示提交日志
- git log --graph 显示提交日志 图形化查看分支情况
- git log --abbrev-commit 只显示版本号和修改动作
- git log --pretty=oneline --abbrev-commit  只显示缩略版本号和修改动作
- git reflog 显示每一条命令的记录
- git reset --hard 版本号 
- git status 查看工作区状态(是否修改了文件, 是否add了, 是否commit了)
- git checkout -- file 将file退回到暂存区(没有添加到暂存区的话就回到和版本库一样的状态)
- git checkout -- file 恢复被删除的file
- git reset HEAD file 把暂存区的修改撤销掉
- `git init` 创建版本库
- `git add` 添加文件到缓冲区 all是`git add`的默认参数, 也可以用`git add .`
- `git commit -m ‘xxx’` 提交到本地仓库
- `git commit --amend -m 'Your message'`- helps re-write messages
- `git status` 查看当前目录状态
- `git diff`查看文件变化的内容
- `git reset --hard HEAD^` 回退到上一版本
- `git reset --hard HEAD^^` 回退到上上版本
- `git reset --hard HEAD~100` 回退到上100个版
- `git reset --hard <commit id前几位>`  回退到指定版本
- `git reset --source <hash code> index.html>`- removes commits and goes back to the commit for that hash code only for that particular file.
- `git reflog` 查看历史版本，如果有需要，随后可以根据显示的commit id进行回退
- `git add`后如果又修改了，此时git commit只会提交add过的版本，后面修改的不会提交
- `git checkout -- filename` 让文件到add或commit过最新的版本，先add的版本，再最新的版本
- `git reset HEAD <file>` 可以撤销git add的版本
- `git rm` 会同时删除仓库里的文件和本地文件，但是在提交之前，还是可以通过git reset HEAD && git checkout —- <file>恢复文件
- `git rm` 相当于本地rm <file> 再git add
- `git remote add origin git@server-name:path/repo-name.git` 关联一个远程库
- `git push -u origin master`第一次推送master分支的所有内容
- `git push origin master`推送最新修改(对远程库非第一次)
- `git branch` 查看分支
- `git branch <name>` 创建分支
- `git checkout <name>` 切换分支
- `git checkout -b <name>` 创建+切换分支
- `git merge <name>` 合并某分支到当前分支
- `git branch -d <name>` 删除分支
- `git log —graph` 查看分支合并图
- `git stash` 保存工作现场
- `git stash list` 查看保存的stash
- `git stash apply stash@{n}` 恢复指定的stash
- `git stash pop stash@{n}` 恢复现场并删除该stash
- 如果一个分支没有被合并过，`git branch -d feature` 会被友情提醒，需要通过`git branch -D feature` 强行删除
- `git tag <name>`新建一个标签，默认为HEAD，也可以指定一个commit id；
- `git tag -a <tagname> -m "blablabla..."`可以指定标签信息；
- `git tag -s <tagname> -m "blablabla..."`可以用PGP签名标签；
- `git tag`可以查看所有标签。
- `git push origin <tagname>`可以推送一个本地标签；
- `git push origin --tags`可以推送全部未推送过的本地标签；
- `git tag -d <tagname>`可以删除一个本地标签；
- `git push origin :refs/tags/<tagname>`可以删除一个远程标签。
- `git restore .` - restores the files to the previous commit/ undos all the local changes that haven't been commited.
- `git restore index.html` - restores only that particular file to the recent commit/ undos all the local/uncommited changes for that file.
- `git revert <hash code>`- helps to roll back to a previous commit by creating a new commit for it. Doesn't removes those commits from the `log` like `git reset` does.

#### 用户信息操作

配置

- git config --global user.name <yourname>
- git config --global user.email <youremail>

获取

- git config --get user.name
- git config --get user.email

#### 撤销修改

> 场景1: 修改后还没有放到暂存区, 要撤销的话

git checkout -- filename 直接丢弃工作区的修改

如果丢弃后又要捡回来, 在文本编辑器上用撤销快捷键

> 场景2: 修改了且添加到了暂存区, 要撤销这次add的话

- git reset HEAD filename 加add后的filenam文件退回到工作区
- git checkout -- filename 直接丢弃工作区的修改

> 场景3: 已经commit了, 想撤销的话

- git reset --hard HEAD^

> 场景4: 撤回最近一次 commit, 并将这次修改的地方放到暂存区

- git reset --soft HEAD^

> 场景5: 误删要恢复

- git checkout -- filename

> 场景6: 回退到之前的版本号之后, 再跳到最新的版本号

- git reflog
- 复制commit-id
- git reset --hard commit-id

>  场景7: 修改已经提交的 commit 的作者

-  git commit --amend --author "username  <wzq@gmail.com>"

>  场景8: 回退的版本要再提交

-  /*1.新建分支*/
  -  git checkout -b temp              //新建分支并切换到temp分支
  -  git push origin temp:temp         //将代码push到temp分支
-  /*2.删除主分支*/
  -  git push origin --delete master   //删除远端主分支
  -  git branch -d master              //删除本地主分支
-  /*3.新建主分支*/
  -  git checkout -b master            //新建主分支并切换到主分支
  -  git push origin master            //提交主分支
- /*4.删除暂存分支*/
  -  git branch -d temp
  -  git push origin --delete temp


#### 删除文件

> 场景1: 确实要删除

- git rm filename
- git commit -m""

>场景2: 误删要恢复

- git checkout -- filename
- 
#### 合并 commit 

> 场景1: 合并最新的三个commit

- `git rebase -i HEAD~~~` (要合并几个 commit 就几个波浪号)
- 此时进入vim, 第一行不动, 后面两行把 `pick` 改成 `s` ( s 代表squash), 保存退出
- 删掉默认的 commit 备注, 自己写一个commit 备注, 保存退出
- 参考链接: http://blog.csdn.net/zmyde2010/article/details/8603810

## 多人协作 

#### 添加单个SSH公钥

- 生成SSH: `ssh-keygen -t rsa -C "username@example.com"`(注册的邮箱)
- 添加公钥: `vi .ssh/id_rsa.pub`，复制其中全部内容，填写到SSH_RSA公钥key下的一栏, 然后点击添加
- 如果`git remote show origin`后还要输入密码, 用`git remote -v`查看是在用HTTPS还是用SSH方式访问仓库, 如果是用HTTPS方式访问仓库, 要修改成SSH方式
  - 执行`git remote remove origin`删除该远程路径
  - 执行`git remote add origin git@aaaaaa.github.com:aaaaaa/xxxxxx.git`加上正确的远程仓库。

#### 添加多个SSH公钥

[reference](https://blog.csdn.net/lyfqyr/article/details/87892271) 

1. 创建第二个密钥 `ssh-keygen -t rsa -C "$your_email"`
2. 此时命令行出现 `Enter file in which to save the key (Users/Spring/.ssh/id_rsa):`，此时自己输入第二个密钥名，例如 `id_rsa_meiyou` 
3. 在 `.ssh` 目录下新建 config 文件，示例：
  ```
  Host github.com
    HostName      github.com
    User  git
    IdentityFile  /Users/Spring/.ssh/id_rsa_github
  Host gitlab.meiyou.com
    HostName      gitlab.meiyou.com
    User  git
    IdentityFile  /Users/Spring/.ssh/id_rsa_meiyou
  ```
4. 清空 `.ssh/known_hosts` 文件内容
5. ssh命令验证结果 `ssh -T git@gitlab.meiyou.com`

#### 将已有git项目放到github上

- 在github上创建一个同名仓库, 例如:hello-world
- 在本地git仓库下
  - git remote add origin git@github.com:wuzhenquan/hello-world
  - git push -u origin master
  - "-u"了一次之后, 就不用再"-u"了, 直接 git push origin master就行了

#### 克隆github上的项目
> clone后, 只有默认的master是可见的, 要让dev可见, 需**建立远程origin的dev到本地dev**

- git clone git@github.com:wuzhenquan/learngit.git
- git checkout -b dev origin/dev

#### 与主干保存同步

- 第一种
  - git pull origin 分支名
- 第二种
  - git fetch origin master
    - git fetch origin表示取回所有分支的更新
    - git fetch origin master表示只取回origin主机的master分支
  - git log -p master..origin/master 比较本地的master分支和origin/master分支的差别
    - git merge origin/master 将本地master和远程master(origin/master)合并


#### 分支

> 使用分支完成某个任务, 合并后再删掉分支, 虽然和在master分支上工作效果一样, 但过程更为安全


- 创建并切换分支: git checkout -b branchname
  - 创建分支: git branch branchname
  - 切换分支: git checkout branchname
  - 从远程分支创建到本地分支: git checkout -b 本地分支名 origin/远程分支名  或者  git fetch origin 远程分支名:本地分支名
- 列出所有的分支并显示当前分支: git branch 
- 合并分支: git merge branchname
  - 在master上合并(fast forward模式): git merge branchname (merge后显示不出分支信息)
  - 在master上合并(禁用fast forward模式): git merge --no-ff -m"备注信息" branchname
- 删除分支: 
  - 删除本地分支: git branch -d branchname 
  - 删除远程分支: git push origin --delete branchname
如果在分支上修改没有提交就直接切换回master上的话, 文件是不会更改的


#### 解决分支冲突

> 两个分支(例如feature1和master)修改了同一处地方再merge之后, 会产生冲突, 冲突结果是在当前分支上合并都合并各自的内容

解决方法:

- git status 先查看状态
- 修改冲突标记的部分
- git add filename
- git commit -m"conflict fixed"

#### 推送分支 

> 将分支推送到远程仓库, 要注意哪些必须需要推送哪些不需要推送

- **master和dev分支必须推送**
  - git push origin master
  - git push origin dev
- 如果本地新建的分支不推送到远程, 对其他人就是不可见的
- 从本地推送分支

#### 抓取分支
> git pull(会自动merge), 把最新的提交从origin上抓下来

- git pull
  - 如果失败, 说明没有指定dev分支与远程origin/dev分支的链接, 需要`git branch --set-upstream dev origin/dev`
  - 如果提示有冲突, 要手动解决冲突
- 再git pull
- git commit -m"merge & fix dev"
- git push origin dev

#### 把已经提交的commit, 从一个分支放到另一个分支
> git cherry-pick <commit id> (这里的 commit id 指的是另外一个要合并的分支的 commt id)

## 标签管理

- 创建标签 `git tag v1.0 `
  - 在master上建一个带有备注信息的v1.0版本的标签 `git tag -a v1.0 -m "version 0.1 released"`
- 删除标签 `git tag -d v0.1`
- 推送标签到远程`git push origin tagname`
  - 一次性全部推送`git push origin --tags`
- 在历史提交的commit-id上打标签 `git tag v1.0 commit-id`
- 查看标签 `git tag` (列出的标签是按字母顺序的)
- 查看标签某一个标签信息 `git show v1.0`

## git和svn的不同之处
- git分布式, svn集中式
- git有暂存区, svn没有
- git有强大的分支管理, svn没有



## 场景

场景1:当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时

> 答: 用命令git checkout -- file。

场景2:当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改

> 答: 第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交

> 参考版本回退一节，不过前提是没有推送到远程库。

场景4: 在dev分支上, 其他人最新的push和你试图push的有冲突

> - git pull
	- 如果失败, 说明没有指定dev分支与远程origin/dev分支的链接, 需要`git branch --set-upstream dev origin/dev`
> - 在git pull
> - git commit -m"merge & fix dev"
> - git push origin dev


场景5: master主干上有一个bug, 急需修复.新功能还没开发完, 等解决完bug之后回到刚才开发新功能的工作状态

> 答: 用git stash 相关的命令操作

场景6: 想切换分支, 但是还有修改的代码没 commit

> 答: 用 git stash 相关命令暂存现在的工作区

###### 基础命令

- git stash 暂存工作区
- 切换到其他分支做其他的工作
- git stash pop 将之前暂存的工作区还原

###### stash命令汇总

- git stash          # save uncommitted changes
- git stash list     # list stashed changes in this git
- git show stash@{0} # see the last stash 
- git stash pop      # apply last stash and remove it from the list
- git stash pop stash@{0} 恢复指定的stash
- git stash --help   # for more info
- git stash clear 清空所有的 stash list

场景6: 查看远程master分支是否更新

> 答: `git remote origin master`后, 最后一句会显示up to date 还是local out of date

- git stash将工作现场藏匿
- 切换到bug 去修复后
- git stash list
- git stash pop 

- 