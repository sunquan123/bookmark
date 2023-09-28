# Linux常见命令

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

6、top查看内存占用

```shell
# 查看某个进程id
ps -ef | grep nginx
# 查看指定进程id信息，cpu占用，内存占用
top -p xxxxxx
PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
1172601 root      20   0   97688   7100   1788 S   0.0   0.4   0:00.02 nginx
# 也可以查看指定进程id信息，cpu占用，内存占用
ps -aux | grep nginx
root     1172601  0.0  0.3  97688  7100 ?        S    Aug23   0:00 nginx: master process /usr/sbin/nginx
33       1172604  0.0 23.7 532116 441436 ?       S    Aug23   0:00 nginx: worker process
# 0.0 0.3是cpu和内存占用，7100是内存消耗，7100k=7m，441436k=440m
```