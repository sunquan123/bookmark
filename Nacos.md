# Nacos

## Docker安装Nacos

```sh
docker pull nacos/nacos-server
docker run -d -p 8848:8848 -e MODE=standalone -e PREFER_HOST_MODE=hostname --name nacos nacos/nacos-server
```





## 持久化

mysql数据库

```properties
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=root
db.password.0=123456
```

运行单机模式

```sh
sh startup.sh -m standalone
```

运行集群模式

```
sh startup.sh

```

