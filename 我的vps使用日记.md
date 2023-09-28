#### 安装宝塔面板

1、脚本安装

```bash
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

2、使用http://172.93.45.163:8888/620662ab 访问

#### SSH使用手册

1、免密登录

```shell
ssh-keygen -t dsa
cat ~/.ssh/id_rsa.pub | ssh root@172.93.45.189 -p 36666 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
#vim /etc/ssh/sshd_config  
RSAAuthentication yes       #RSA身份验证:yes开启no关闭
PubkeyAuthentication yes    #公钥身份验证:yes开启no关闭
PermitRootLogin yes          #允许root登录:yes开启no关闭
PasswordAuthentication no   #密码登录方式:yes开启no关闭
systemctl restart sshd
ssh root@172.93.45.163 -p 29857
cat ~/.ssh/authorized_keys
```

2、自定义banner登陆信息

```shell
#vim /etc/ssh/sshd_config
Banner /usr/local/etc/warning.txt
```

3、ssh端口转发

```shell
ssh -D 2121 tunnel-host -N #在本地执行，将本地的2121端口使用socks5协议转发到目标主机上
ssh -L 1080:目标主机ip:目标主机port 中转主机ip #在本地执行ssh，将本地1080端口与中转主机的22端口建立连接，访问目标主机的ip:port
ssh -R 2999:目标主机ip:目标主机port local  #在中转主机上执行ssh，将local（中转主机）的2999端口，访问目标主机的IP:port
-N #只建立隧道，不建立shell连接
-f #后台运行
```

4、安装ping

```shell
apt-get install iputils-ping
yum install iputils
```
