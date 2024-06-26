# 下载 Git
- https://git-scm.com/downloads

## 常用命令
```sh
git config --global init.defaultbranch main
git config --global user.name BYDylan
git config --global user.email 921644606@qq.com
git config --global user.password By96o122

git config --list

# 创建本地分支
git branch local_branch
# 切换分支
git branch local_branch
git switch local_branch
# 查看分支
git branch
# 创建本地分支并切换
git checkout -b local_branch
# 合并分支
git merge local_branch
# 修改分支名分支
git branch -m master main
# 删除分支
git branch -d local_branch
```

## 推送分支
```sh
git init
# 设置远程仓库
git remote add github https://gitee.com/fuqiangma/demo.git

git add README.md
# 提交全部
git add .

git commit -m "first commit"

# 修改分支名
git branch -M main

# 推送
git push -u github master
```

## 拉取分支
```sh
# 拉取远程主机 remote_repository_name,remote_branch 分支与本地 local_branch 合并
git pull remote_repository_name remote_branch:local_branch
# 如果已经切换了分支,可以省略 :local_branch
git pull github master:main
```

## 提交流程
```sh
# 添加当前目录下所有文件到缓存区,最好指定具体文件
git add .
git add filename
# 如果要丢弃
git rm --cached 全路径文件名
git rm -r --cached 全路径目录名
# 提交缓存区
git commit -m "代码说明"
# 如果要修改,此命令会进入vim编辑状态
git commit -amend
# 回退修改了但还未提交的文件
checkout .
# 查看目前状态
git status
# 提交代码
git push remote_repository_name local_branch:remote_branch
# 如果本地分支名与远程分支名相同,则可以省略:
git push remote_repository_name local_branch
# 强行覆盖提交
git push --force by remote_branch
# 删除 remote_repository_name 主机的 remote_branch 分支
git push remote_repository_name --delete remote_branch
```sh
