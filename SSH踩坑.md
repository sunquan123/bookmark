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

在服务器端生成sshkey：

```shell
[root@instance-1 ~]# ssh-keygen
```

拷贝公钥与私钥到客户端：

```shell
[root@instance-1 ~]# scp -r root@35.221.217.143:/root/.ssh ～/.ssh
```

验证一下连接：

```shell
sh-3.2# ssh root@35.221.217.143
Last login: Thu Feb  6 05:38:10 2020 from 117.89.214.72
[root@instance-1 ~]#
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
[root@instance-1 ~]# wget https://install.direct/go.sh
--2020-02-06 05:57:44--  https://install.direct/go.sh
正在解析主机 install.direct (install.direct)... 104.27.175.71, 104.27.174.71, 2606:4700:3034::681b:ae47, ...
正在连接 install.direct (install.direct)|104.27.175.71|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度：未指定 [text/plain]
正在保存至: “go.sh”

    [ <=>                                   ] 13,666      --.-K/s 用时 0s

2020-02-06 05:57:44 (60.9 MB/s) - “go.sh” 已保存 [13666]
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

v2ray的配置文件/etc/v2ray/config.json:

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

