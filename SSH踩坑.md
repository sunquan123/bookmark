## SSH踩坑

### 1、指定端口访问ssh

ssh root@192.168.0.110 -p 22

### 2、使用root登陆，输入完密码显示

```shell
[root@localhost ~]# ssh root@192.168.0.110 -p 22  //指定端口
root@192.168.0.110's password:
Permission denied, please try again.
root@192.168.0.110's password:
Authentication failed.
```

原因：ssh的密码登录权限未开

解决：

- vim /etc/ssh/sshd_config

  将PasswordAuthentication前面的#号去掉
  将PasswordAuthentication 设为yes

- 重启sshd服务
  systemctl restart sshd(centos7)

### 3、当前是root用户，去编辑`/etc/ssh/sshd_config` 却提示没有权限修改。报错如`"/etc/ssh/sshd_config" E212: 无法打开并写入文件`。

原因：既然提示没有权限，又是root用户，肯定是给该文件设置了特殊的权限。检查特殊权限设置

解决：

```shell
root@pts/1 $ chattr -i /etc/ssh/sshd_config
iZ25zjpz5pqZ [~] 2017-12-16 10:59:14
root@pts/1 $ lsattr  /etc/ssh/sshd_config
-------------e- /etc/ssh/sshd_config
iZ25zjpz5pqZ [~] 2017-12-16 10:59:17
root@pts/1 $ vim /etc/ssh/sshd_config
iZ25zjpz5pqZ [~] 2017-12-16 10:59:26
root@pts/1 $
```

### 4、开启密钥登陆

在客户端生成sshkey：

```shell
ssh-keygen
```

生成公钥 /User/sunquan/.ssh/id_rsa.pub

私钥 /User/sunquan/.ssh/id_rsa

自动上传公钥到服务器：ssh-copy-id 命令

```shell
ssh-copy-id -i /Users/sunquan/.ssh/id_rsa.pub -p 36666 root@172.93.45.189
```

验证一下连接：

```shell
ssh -v root@172.93.45.189 -p 36666
```

使用ssh-config进行ssh登录配置

```
vim /Users/sunquan/.ssh/config

Host banwagong-sun
  HostName 172.93.45.189
  Port 36666
  User root
  IdentityFile /Users/sunquan/.ssh/id_rsa
```

配置参数说明

```
Host : 可以看作是一个你要识别的名称，对识别的名称，进行配置对应的的主机名和ssh文件
HostName : 要登录主机的IP地址、或者域名
User : 登录名
port : 端口
IdentityFile : 指明上面User对应的identityFile路径 也就是 私钥的路径
ProxyCommand : 代理命令(不常用) , 通俗解释: 就是当你连接上目标服务器后，首先执行的命令，例如：ProxyCommand tail -f /backend/logs/info.log 表示登录目标主机后，执行此命令查看日志
```

下次登录使用，即可

```
ssh banwagong-sun
```



### 5、弃用shadow socks，启用v2ray

首先关闭原先的shadowsocks，相关命令如下：

```shell
/etc/init.d/shadowsocks start # 开启
/etc/init.d/shadowsocks stop # 关闭
/etc/init.d/shadowsocks restart # 重启
/etc/init.d/shadowsocks status # 状态
systemctl disable shadowsocks # 取消开机自启动
systemctl stop shadowsocks # 也可以从系统层面关闭shadowsocks
```

安装v2ray脚本：

```shell
[root@instance-1 ~]# bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 21613  100 21613    0     0   114k      0 --:--:-- --:--:-- --:--:--  114k
info: Installing V2Ray v4.34.0 for x86_64
Downloading V2Ray archive: https://github.com/v2fly/v2ray-core/releases/download/v4.34.0/v2ray-linux-64.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   626  100   626    0     0   2858      0 --:--:-- --:--:-- --:--:--  2871
100 11.8M  100 11.8M    0     0  24.8M      0 --:--:-- --:--:-- --:--:-- 82.7M
Downloading verification file for V2Ray archive: https://github.com/v2fly/v2ray-core/releases/download/v4.34.0/v2ray-linux-64.zip.dgst
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.hostduplex.com
 * elrepo-kernel: dfw.mirror.rackspace.com
 * extras: repos-lax.psychz.net
 * updates: mirror.shastacoe.net
正在解决依赖关系
--> 正在检查事务
---> 软件包 unzip.x86_64.0.6.0-21.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

================================================================================
 Package          架构              版本                  源               大小
================================================================================
正在安装:
 unzip            x86_64            6.0-21.el7            base            171 k

事务概要
================================================================================
安装  1 软件包

总下载量：171 k
安装大小：365 k
Downloading packages:
unzip-6.0-21.el7.x86_64.rpm                                | 171 kB   00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : unzip-6.0-21.el7.x86_64                                     1/1
  验证中      : unzip-6.0-21.el7.x86_64                                     1/1

已安装:
  unzip.x86_64 0:6.0-21.el7

完毕！
info: unzip is installed.
info: Extract the V2Ray package to /tmp/tmp.Rvx7x2dItK and prepare it for installation.
rm: 无法删除"/etc/systemd/system/v2ray.service.d/10-donot_touch_multi_conf.conf": 没有那个文件或目录
rm: 无法删除"/etc/systemd/system/v2ray@.service.d/10-donot_touch_multi_conf.conf": 没有那个文件或目录
info: Systemd service files have been installed successfully!
warning: The following are the actual parameters for the v2ray service startup.
warning: Please make sure the configuration file path is correctly set.
~~~~~~~~~~~~~~~~
[Unit]
Description=V2Ray Service
Documentation=https://www.v2fly.org/
After=network.target nss-lookup.target

[Service]
User=nobody
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
NoNewPrivileges=true
ExecStart=/usr/local/bin/v2ray -config /usr/local/etc/v2ray/config.json
Restart=on-failure
RestartPreventExitStatus=23

[Install]
WantedBy=multi-user.target
# In case you have a good reason to do so, duplicate this file in the same directory and make your customizes there.
# Or all changes you made will be lost!  # Refer: https://www.freedesktop.org/software/systemd/man/systemd.unit.html
[Service]
ExecStart=
ExecStart=/usr/local/bin/v2ray -config /usr/local/etc/v2ray/config.json
~~~~~~~~~~~~~~~~
warning: The systemd version on the current operating system is too low.
warning: Please consider to upgrade the systemd or the operating system.

installed: /usr/local/bin/v2ray
installed: /usr/local/bin/v2ctl
installed: /usr/local/share/v2ray/geoip.dat
                    "type": "field",
installed: /usr/local/share/v2ray/geosite.dat
installed: /usr/local/etc/v2ray/config.json
installed: /var/log/v2ray/
installed: /var/log/v2ray/access.log
installed: /var/log/v2ray/error.log
installed: /etc/systemd/system/v2ray.service
installed: /etc/systemd/system/v2ray@.service
removed: /tmp/tmp.Rvx7x2dItK
info: V2Ray v4.34.0 is installed.
You may need to execute a command to remove dependent software: yum remove curl unzip
Please execute the command: systemctl enable v2ray; systemctl start v2ray

```

启动脚本：

```shell
[root@instance-1 ~]# sudo bash go.sh
Installing V2Ray v4.22.1 on x86_64
Downloading V2Ray: https://github.com/v2ray/v2ray-core/releases/download/v4.22.1/v2ray-linux-64.zip
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   608    0   608    0     0    591      0 --:--:--  0:00:01 --:--:--   592
100 11.6M  100 11.6M    0     0  3177k      0  0:00:03  0:00:03 --:--:-- 4593k
Extracting V2Ray package to /tmp/v2ray.
Archive:  /tmp/v2ray/v2ray.zip
  inflating: /tmp/v2ray/config.json
   creating: /tmp/v2ray/doc/
  inflating: /tmp/v2ray/doc/readme.md
  inflating: /tmp/v2ray/geoip.dat
  inflating: /tmp/v2ray/geosite.dat
   creating: /tmp/v2ray/systemd/
  inflating: /tmp/v2ray/systemd/v2ray.service
   creating: /tmp/v2ray/systemv/
  inflating: /tmp/v2ray/systemv/v2ray
  inflating: /tmp/v2ray/v2ctl
 extracting: /tmp/v2ray/v2ctl.sig
  inflating: /tmp/v2ray/v2ray
 extracting: /tmp/v2ray/v2ray.sig
  inflating: /tmp/v2ray/vpoint_socks_vmess.json
  inflating: /tmp/v2ray/vpoint_vmess_freedom.json
PORT:18021
UUID:98f3795b-2712-406f-8c13-7f33257f6001
Created symlink from /etc/systemd/system/multi-user.target.wants/v2ray.service to /etc/systemd/system/v2ray.service.
V2Ray v4.22.1 is installed.
```

v2ray的配置文件usr/local/etc/v2ray/config.json:

在线生成器：https://intmainreturn0.com/v2ray-config-gen/#

控制v2ray的命令：

```shell
# sudo systemctl start v2ray    启动
# sudo systemctl stop v2ray     停止
# sudo systemctl restart v2ray  重启
```

配置文件在/etc/v2ray/config.json

不写了，谷歌云太垃圾了。还根本连不上。浪费时间的东西！

2020.02.16买了搬瓦工

安装完需要确认firewall是否开启了此端口

```shell
firewall-cmd --zone=public --list-ports  //查看开启了哪些端口
firewall-cmd --zone=public --add-port=80/tcp --permanent //永久开启80端口，需要重启firewall
systemctl restart firewalld
service status firewalld
```

### 6、SSH连接卡顿

1. 关闭DNS反向解析。
在linux中，默认就是开启了SSH的反向DNS解析,这个会消耗大量时间，因此需要关闭。

```shell
vi /etc/ssh/sshd_config
UseDNS=no
```


在配置文件中，虽然UseDNS yes是被注释的，但默认开关就是yes

2. 关闭SERVER上的GSS认证。
在authentication gssapi-with-mic有很大的可能出现问题，因此关闭GSS认证可以提高ssh连接速度。

```shell
vi /etc/ssh/sshd_config
GSSAPIAuthentication no
```

3.重启sshd服务，使配置生效。

```shell
service sshd restart
```

### 7、搬瓦工常用命令

```
ssh root@172.93.45.189 -p 36666
pn56aqJQU0OT
```

### 8、解决SSH一段时间不操作就退出的问题

##### 修改SSH服务端的配置

sudo vim /etc/ssd/sshd_config

把下面这两行的注册打开，然后修改参数：

ClientAliveInterval 30 # 表示每30秒服务器向客户端发起一次心跳，如果客户端响应就保持连接
ClientAliveCountMax 5 # 如果连续5服务器收不到心跳，就断开连接

以上两个参数，可以根据自己的情况来设置。

最后，配置要生效，需要重启sshd服务：

service sshd restart

正常情况，客户端不会不响应服务器的心跳，因此SSH客户端就不会再自动退出了。

##### 修改SSH客户端的配置

修改客户端的配置的好处是，不需要重启服务端，如果你没有权限修改SSH服务器，也只能修改客户端的配置了。

sudo vim /etc/ssh/ssh_config / vim /Users/sunquan/.ssh/config

增加：

TCPKeepAlive yes # 据说这个配置项默认是开启的
ServerAliveInterval 30  #客户端主动向服务端请求响应的间隔
ServerAliveCountMax 5 # 连续5此客户端收不到服务器的响应，就是退出链接





