1、统计某一个文件中的 牛客网 出现了几次

```shell
grep "牛客网" 文件名 | wc -l
```

2、crontab命令

```shell
* * * * * command
分钟 小时 天 月 星期 具体命令
```

3、开启/关闭命令进程

```shell
jobs //查看当前有哪些命令进程
bg %jobnumber //将一个前台命令进程放到后台执行
fg %jobnumber //将一个后台命令进程放到前台执行
```

4、给某个文件增加：所有人可执行，同组可写的权限

```shell
chmod a+x g+w filename
```


权限对应数字：

```
u-g-o：用户-组内用户-其他用户
r-w-x：用4-2-1表示。
组外成员o的权限为只读：r-- = 4
所有者a有全部权限：rwx; = 7
组内g的权限为读与写:rw- = 6
```


5、合并两个文件

```shell
cat file1 file2 > newfile
```