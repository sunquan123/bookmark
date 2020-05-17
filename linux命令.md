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

