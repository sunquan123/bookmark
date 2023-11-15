## Docker 容器修改内部时间

一、创建镜像时
 如果是ubuntu 系统 在dockerfile加入

```bash
RUN echo "Asia/shanghai" > /etc/timezone;
```

如果是centos 系统在dockerfile加入

```undefined
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

二、容器已经启动时

如果是ubuntu 系统

```bash
docker exec [容器id]  echo "Asia/shanghai" > /etc/timezone;
```

如果是centos 系统

```ruby
docker cp /usr/share/zoneinfo/Asia/Shanghai  [容器id]:/etc/localtime
```

还可以在启动容器的时候加上下面参数

```jsx
-v /etc/localtime:/etc/localtime:ro  
```

三、安装oracle

![image-20200211173407459](/Users/sunquan/Library/Application Support/typora-user-images/image-20200211173407459.png)

注意：连接的时候sid设置为xe。有的人用的orcl，有的人用的ora11g。

oracle如果存储文字乱码，需要执行

```
export NLS_LANG='SIMPLIFIED CHINESE_CHINA.AL32UTF8'
```

重启docker时，自动启动相关容器

```shell
docker run -d --restart always tomcat
```

修改容器启动参数

```shell
docker container update --restart=always d8237e7c3399
```

设置docker镜像源地址

```shell
vim /etc/docker/daemon.json
{
    "registry-mirrors": [https://registry.docker-cn.com","https://docker.mirrors.ustc.edu.cn"]
}
service docker restart
docker info
```

## docker容器添加对外映射端口

### 首次启动容器指定映射端口

在容器首次启动是，可通过-p参数（-P参数则会[随机选择](https://so.csdn.net/so/search?q=%E9%9A%8F%E6%9C%BA%E9%80%89%E6%8B%A9&spm=1001.2101.3001.7020)宿主机的一个端口进行映射）来指定宿主机和容器端口的映射，例如：

```
docker run -it -d --name [container-name] -p 8088:80 [image-name]
```

上述命令将容器内的[80端口](https://so.csdn.net/so/search?q=80%E7%AB%AF%E5%8F%A3&spm=1001.2101.3001.7020)映射到宿主机的8088端口。

参数说明

- -d 表示后台运行容器
- -t 为docker分配一个伪终端并绑定到容器的标准输入上
- -i 是让容器的标准输入保持打开状态
- -p 指定映射端口

### 新增映射端口

当容器已经运行之后，且容器中已经有配置改动，不方便重新创建逐一修改配置时，则可通过修改配置文件进行端口改动。

#### 修改配置文件

修改配置文件，此种方式稍微有些麻烦，而且在操作过程中需要把的docker服务停掉，使用时需注意。

查看容器信息：

```
docker ps -a
```

查看容器的端口映射情况，在容器外执行：

```
docker port d5104ec757b6
# 或
docker port nginx
```

查找要修改容器的容器Id：

```
docker inspect d5104ec757b6 |grep Id
```

执行命令结果：

```
"Id": "d5104ec757b667d5647c61c9d6066fc9b7b3a675eaba6af4dc990989f78e99b7",
```

不同的容器Id不同，然后在/var/lib/docker/containers目录下找到与上述Id相同的目录，修改该目录下的 hostconfig.json 和 config.v2.json文件。

在修改之前，还需要停到容器和docker服务。

停掉容器：

```
docker stop d5104ec757b6
```

停掉docker服务：

```
systemctl stop docker
```

修改hostconfig.json，添加端口绑定"443/tcp": [{“HostIp”: “”,“HostPort”: “443”}]，表示绑定端口443：

```
"PortBindings":{
    "443/tcp":[{"HostIp":"","HostPort":"443"}],
     "80/tcp":[{"HostIp":"","HostPort":"80"}]
 }
```

找到key为PortBindings的配置，在其value部分中新增了443相关的配置。

修改config.v2.json文件，在ExposedPorts中加上要暴露的端口，即443：

```
"ExposedPorts":{"443/tcp":{},"80/tcp":{}}
```

找到key为ExposedPorts的配置，在其value部分新增443的配置。

保持配置文件，重启docker服务和容器：

```
systemctl start docker
docker start d5104ec757b6
```

此时，再用前面提到的docker port命令即可看到新增的端口已经绑定成功。
