Git

1、版本相关

```shell
git log //查看commit日志
git show xxxx //查看某次commit提交的内容
git reset --hard xxxx //git回退到某次xxxx版本上（也可以前进到某个版本）
git status //查看当前文件的修改信息
git diff xx.log //查看某个文件改动了什么内容
```

2、提交相关

```shell
git add xx.java xx.log //往git缓存区新增指定文件
git rm --cached xx.java //删除git缓存区的指定文件（此操作还会将本来在git版本里的文件给移除出去）
git commit -o xx.java xx.log -m '指定文件提交了' //指定文件进行commit
```

3、分支相关

```shell
git push origin master //直接推上远程分支
git pull //直接拉取远程分支
```

4、stash相关

```shell
git stash //将改动内容暂存到stash
git stash pop //将stash的改动内容恢复到文件中
git stash list //查看stash的列表
```

5、proxychains代理github下载

```bash
proxychains4 git clone ssh地址
```

