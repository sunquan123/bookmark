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
