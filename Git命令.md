Git

1、版本相关

```
git log //查看commit日志
git show xxxx //查看某次commit提交的内容
git reset --hard xxxx //git回退到某次xxxx版本上（也可以前进到某个版本）
```

2、提交相关

```
git add xx.java xx.log //往git缓存区新增指定文件
git rm --cached xx.java //删除git缓存区的指定文件（此操作还会将本来在git版本里的文件给移除出去）
git commit -o xx.java xx.log -m '指定文件提交了' //指定文件进行commit
```

3、分支相关

```
git push origin master //直接推上远程分支
git pull //直接拉取远程分支
```

4、stash相关

```
git stash //将改动内容暂存到stash
git stash pop //将stash的改动内容恢复到文件中
git stash list //查看stash的列表
```

