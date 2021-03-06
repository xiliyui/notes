# git

## git同步远程分支信息
`git remote prune origin】


## git fetch
### git取回所有分支的更新
`git fetch <远程主机名>`

### git取回特定分支的更新
`git fetch <远程主机名> <分支名>`

### git fetch和git pull的区别
`git fetch`是将远程主机的最新内容拉到本地，用户在检查了以后决定是否合并到工作本机分支中。  
而`git pull`则是将远程主机的最新内容拉下来后直接合并，即：`git pull` = `git fetch` + `git merge`，这样可能会产生冲突，需要手动解决。
```
git fetch origin master //从远程主机的master分支拉取最新内容 
git merge FETCH_HEAD    //将拉取下来的最新内容合并到当前所在的分支中
```
![流程图](./resources/git_fetch_pull.png "流程图")


## git生成一个新的文件分支
- 最简单的写法
> `git worktree add <新路径>`  
将基于当前分支，新建一个worktree目录，新的分支名就是新建目录的名称。

- 新建一个指定分支
> `git worktree add <新路径> -b <新分支名>`  
将基于当前分支名，新建一个worktree目录，新的分支名是指定的分支名。

- 基于指定分支新建一个指定分支
> `git worktree add <新路径> -b <新分支名> <指定分支名>`


## git撤销commit
`git reset --soft HEAD^`  
HEAD^的意思是上一个版本，也可以写成HEAD~1  
如果你进行了两次commit，想都撤回，可以使用HEAD~2  
`git reset --hard <commitHash>` 退到/进到，指定commitHash的commit  
`git push origin HEAD --force` 强推到远程


## git修改上一次commit的information
`git commit --amend`


## git从指定commitHash拉取代码
`git cherry-pick <commitHash>`


## git生成ssh公钥
1. 进入.ssh文件  
`cd ~/.ssh`  
2. 查看是否有公钥目录  
`ls`  
3. 查看公钥  
`cat id_rsa.pub`  
以ssh-rsa开头的就是公钥
4. 删除旧的公钥   
```
mmkdir key_backup
cp id_rsa* key_backup
rm id_rsa*
```
5. 生成新的公钥   
`ssh-keygen -t rsa -C "邮箱"`  
输入命令后会有几个设置密码的提示，如果不需要设置密码，直接一直按回车键到结束。
6. 完成公钥的生成后查看公钥，参考第2步查看生成的公钥。

解决每次git pull/push都需要输入密码的问题  
```
ssh-add -L
ssh-add
```
输入密码。


## git修改本地分支的分支名
`git branch -m <newBranchName>`