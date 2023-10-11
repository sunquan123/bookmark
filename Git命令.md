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
// 克隆时可以指定文件名：
git clone http://github.dandelion.com/demo/sample.git MySampleDir
```

4、stash相关

```shell
git stash //将改动内容暂存到stash
git stash pop //将stash的改动内容恢复到文件中
git stash list //查看stash的列表
```

5、proxychains代理github下载

```sh
proxychains4 git clone ssh地址
```

6、删除文件夹及文件夹中的文件

第一步，用鼠标右键删除掉你的文件夹
第二步，git bash 打开命令窗口，按顺序输入如下命令

```sh
git rm -r --cached [文件夹名]        #删除缓存区的文件目录         
git commit -m "Remove the now ignored directory some-directory"
git push
```

7、github令牌提交

应用令牌
将生成的令牌拷贝下来，修改现有项目的url

```
git remote set-url origin  https://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@github.com/sunquan123/sunquan123.github.io.git
```

8、git设置系统代理

```shell
# 注意修改成自己的IP和端口号
git config --global http.proxy http://127.0.0.1:7890 
git config --global https.proxy http://127.0.0.1:7890
```
