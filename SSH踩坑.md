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

bash <(curl -s -L https://git.io/v2ray-setup.sh)


..由于你的 VPS 内核支持开启 BBR ...已经为你启用 BBR 优化....


---------- V2Ray 配置信息 -------------

 地址 (Address) = 23.106.158.135

 端口 (Port) = 37777

 用户ID (User ID / UUID) = 4b1ce1c3-81aa-4bfa-9027-2332512f97a4

 额外ID (Alter Id) = 0

 传输协议 (Network) = tcp

 伪装类型 (header type) = none

---------- END -------------

V2Ray 客户端使用教程: https://git.io/v2ray-client

提示: 输入 v2ray url 可生成 vmess URL 链接 / 输入 v2ray qr 可生成二维码链接

免被墙..推荐使用JMS: https://www.itblogcn.com/article/1012.html


 该脚本已自动关闭防火墙...


---------- V2Ray vmess URL / V2RayNG v0.4.1+ / V2RayN v2.1+ / 仅适合部分客户端 -------------

vmess://ewoidiI6ICIyIiwKInBzIjogImlwXzIzLjEwNi4xNTguMTM1IiwKImFkZCI6ICIyMy4xMDYuMTU4LjEzNSIsCiJwb3J0IjogIjM3Nzc3IiwKImlkIjogIjRiMWNlMWMzLTgxYWEtNGJmYS05MDI3LTIzMzI1MTJmOTdhNCIsCiJhaWQiOiAiMCIsCiJuZXQiOiAidGNwIiwKInR5cGUiOiAibm9uZSIsCiJob3N0IjogIiIsCiJwYXRoIjogIiIsCiJ0bHMiOiAiIgp9Cg==

免被墙..推荐使用JMS: https://www.itblogcn.com/article/1012.html

```

v2ray的配置文件usr/local/etc/v2ray/config.json:

在线生成器：https://intmainreturn0.com/v2ray-config-gen/#

控制v2ray的命令：

```shell
v2ray info 查看 V2Ray 配置信息
v2ray config 修改 V2Ray 配置
v2ray link 生成 V2Ray 配置文件链接
v2ray infolink 生成 V2Ray 配置信息链接
v2ray qr 生成 V2Ray 配置二维码链接
v2ray ss 修改 Shadowsocks 配置
v2ray ssinfo 查看 Shadowsocks 配置信息
v2ray ssqr 生成 Shadowsocks 配置二维码链接
v2ray status 查看 V2Ray 运行状态
v2ray start 启动 V2Ray
v2ray stop 停止 V2Ray
v2ray restart 重启 V2Ray
v2ray log 查看 V2Ray 运行日志
v2ray update 更新 V2Ray
v2ray update.sh 更新 V2Ray 管理脚本
v2ray uninstall 卸载 V2Ray
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





