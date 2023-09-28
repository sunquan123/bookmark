# 体系结构

1、innodb使用next-key-locking避免幻读产生 支持事务 聚集型存储 行锁
2、nyisam分开存储索引文件与数据文件
3、memory存储在内存中 适合临时数据临时表  mysql查询的中间结果集使用此引擎 hash索引
4、mysql使用 tcp/ip套接字、命名管道、UNIX域套接字 通信

踩坑：

1、设置mysql用户拥有所有特权：

```
UPDATE mysql.user SET Grant_priv='Y', Super_priv='Y' WHERE User='oc_admin';
flush privileges;
```

安装mysql 8.0([Docker安装mysql8-超详细、每步都有截图_docker 安装mysql8_小小小小真的博客-CSDN博客](https://blog.csdn.net/a1150499208/article/details/131437199))

```
docker pull mysql:8.0.20
docker run -p 3307:3306 --name mysql8 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.20
docker ps

docker cp  mysql8:/etc/mysql /docker/mysql8.0.20/
```
