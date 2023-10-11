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

7、for循环打印指定字符串

```shell
#!/bin/bash
for i in $(seq 1 98)
do
echo "- http://106.14.127.79:8848/images/img/img("$i").webp"
done
```

8、grep查找文件

```shell
在查找字符串时，有时需要指定文件后缀方式查找，命令如下：
grep -rn --include="*.properties" 'tableIsSharding' ./  
```

9、使用less命令检索文件内容

```shell
less
less 工具也是对文件或其它输出进行分页显示的工具，应该说是linux正统查看文件内容的工具，功能极其强大。less 的用法比起 more 更加的有弹性。 在 more 的时候，我们并没有办法向前面翻， 只能往后面看，但若使用了 less 时，就可以使用 [pageup] [pagedown] 等按 键的功能来往前往后翻看文件，更容易用来查看一个文件的内容！除此之外，在 less 里头可以拥有更多的搜索功能，不止可以向下搜，也可以向上搜。

-m  显示类似more命令的百分比
-N  显示每行的行号
/字符串：向下搜索“字符串”的功能
?字符串：向上搜索“字符串”的功能
n：重复前一个搜索（与 / 或 ? 有关）
N：反向重复前一个搜索（与 / 或 ? 有关）
b  向前翻一页
d  向后翻半页
Q  退出less 命令
G - 移动到最后一行
g - 移动到第一行

我经常使用 less -mN 来查看文件，然后使用上面命令基本够用
```