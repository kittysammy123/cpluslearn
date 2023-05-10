### 本地仓库与远程仓库关联

1、在本地仓库中使用命令 git remote add origin [远程仓库URL] 

```
执行这个命令后，Git 就会将远程仓库的 URL 存储在本地 Git 仓库的配置文件中，并以指定的 remote_name 命名该远程仓库。这样，在以后的操作中，就可以使用 remote_name 来代替远程仓库的 URL。
关联的意思就是本地仓库名origin与远程url仓库相关联
```

2、git clone  url  默认会创建一个名为origin的远程仓库

3、git remote -v  查看所有远程仓库的信息

4、git branch 列出当前本地仓库中存在的所有分支 

5、git checkout xxx  切换分支

6、git push  默认就是提交到origin  的当前分支

7、git push  origin name   本地的name 分支，推送到远程的name分支

8、git push origin name:xxx  本地的name分支推送到远程的xxx分支

9、merge合并分支

```
假设你想将 develop 分支合并到 master 分支上，可以先切换到 master 分支
git checkout master  # 切换到 master 分支
git merge develop    # 将 develop 分支合并到当前分支（即 master 分支）上
假设此时提示了有冲突，你可以选择是在master还是develop来解决冲突，可以使用文本编辑器打开文件，查看和解决冲突
git add <conflicted-file>    # 标记文件为已解决
git commit    # 提交修改

然后再重新执行合并

```

10、git pull origin develop   拉去远程的develop分支

```
也可以使用git pull  url develop，此时不会默认创建origin分支，仅仅是从远程拉取代码
```

