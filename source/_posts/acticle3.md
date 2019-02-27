---
title: 学习Git的记录过程(不断更新)
date: 2018-05-02 20:45:05
tags:
    web前端
---
##### 创建
git init   （创建.git文件夹：将这个目录dev变成git可以管理的仓库）
git add .  (. 表示添加dev中的所有文件夹：添加到仓库)
git commit -m "这是注释"  （把文件提交到仓库：提交到仓库）
* add与commit 区别：add可以添加多个文件放置在暂存区stage，commit一次性添加add中的文件提交到master(其他分支)分支上
git remote add origin 仓库地址  （关联远程仓库）
git push -u origin master （把dev中的文件夹所有内容推送到远程库中，-u指的是上流分支，这个概念有点模糊，大概是上流(远程仓库)和下流(本地仓库)做的一个关联吧）
<!-- more -->

##### 删除
git rm --cached 删除的文件名  (有cached的话只会删除远程仓库不会删除本地文件)
* 若尚未放入暂存区：
git checkout -- filename
git checkout .  取消所有修改
git reset HEAD 文件名（当已经放入暂存区，git reset HEAD 文件名，重新放回工作区，在暂存区找不到，工作区不会发生变化）
* 若放入暂存区，但是还没有commit
git reset commit_id  （commit_id 可以用git log查看）
git reset --hard commit_id


##### 分支
git checkout -b 分支名 （创建分支，然后切换到分支上）
--> 这是加上参数-b 相当于git branch 分支名 ；git checkout 分支名；先创建再切换
git push -u origin fenzhi  将本地分支添加到远程分支上，加上-u效果同下
git push --set-upstream origin fenzhi  为分支制定一个上游分支（在远程仓库上的分支）
git branch 查看当前分支 * 表示当前分支
git checkout master （回归到master分支）
git merge --no--ff -m "这是注释" 分支名 
git branch -d 分支名 （合并后删除分支）
git branch -D 分支名 （强制删除未合并的分支）

##### 其他命令
git reset HEAD filename  取消某个文件add
git reset HEAD .  取消所有add

##### 绑定github名字和邮箱
git config --global user.name 'yourname'
git config --global user.email 'youremail'

##### 给github设置ssh key
cd ~/.ssh
ls
若出现id_rsa id_rsa.pub说明已经有ssh key，直接在github中设置即可
若没有
ssh -keygen -t rsa -C 'youremail'来生成，然后直接回车回车

##### 遇到的问题
```
! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:jingrushen/canvas.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
分析：这种情况，表示在github中远程仓库中有一些文件在本地库中没有(新创建的仓库一般是 README.md 文件缺失) 
解决办法：git pull --rebase origin master    （把远程仓库中的文件添加到本地库中）

关于密钥生成错误解决
1.查询没有密钥:在github中删除原密钥重新生成密钥
2.生成密钥过程中出现    Saving key "/path/to/key" failed: No such file or directory
 ssh-keygen -t rsa -b 4096 -C 'xxx@gmail.com' -f ~/.ssh/id_rsa -P ""

 参考资料：
 http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html
 还有廖雪峰的教程
 Git官网