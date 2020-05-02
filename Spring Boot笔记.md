#### 1、Spring Boot是什么？

Spring Boot就是Spring的简化模版设计的产品。

Spring Boot内置了几个Servlet容器（Tomcat、Jetty、Undertow），Spring Boot将应用程序打包成jar包，放到Servlet容器中执行。

Spring Boot没有实现一些企业级Java规范，包括JMS、JPA。Spring Boot内置了JPA的实现（Hibernate）的Bean，以此支持了JPA。如果需要这些企业级规范，需要在Spring Boot配置那些特定的Bean。

Spring Boot的启动器依赖通过Maven、Gradle提供的传递依赖解析，Spring4的条件化配置特性实现的。

#### 2、Spring boot启动依赖--starter-dependency

使用maven或者gradle管理依赖的时候，Spring Boot的启动依赖功能优化了对Spring应用程序的依赖管理：从功能的角度去管理依赖，而不需要深入到每个jar包的层次。每当程序员需要某项功能的时候（持久层、视图层。。），只需要一项启动依赖，背后的maven或者gradle就根据传递依赖的方式尝试下载对应的多个jar包。

#### 3、Spring Boot自动配置

自动配置的实现是通过Spring提供的条件化配置做到的。如何做到条件化配置？使用condition注解在一个类上，当满足某个条件时这个类才会被创建（成Bean）。

| 条件化注解                      | 配置生效条件                                                 |
| :------------------------------ | :----------------------------------------------------------- |
| @ConditionalOnBean              | 配置了特定的 Bean。                                          |
| @ConditionalOnMissingBean       | 没有配置特定的 Bean。                                        |
| @ConditionalOnClass             | Classpath  里有指定的类。                                    |
| @ConditionalOnMissingClass      | Classpath 里没有指定的类。                                   |
| @ConditionalOnExpression        | 给定的 SpringExpressionLanguage(SpEL) 表达式计算结果为 true。 |
| @ConditionalOnJava              | Java 的版本匹配特定值或者一个范围值。                        |
| @ConditionalOnJndi              | JNDI 参数必须存在。                                          |
| @ConditionalOnProperty          | 指定的属性有一个明确的值。                                   |
| @ConditionalOnResource          | Classpath 里存在指定的资源。                                 |
| @ConditionalOnWebApplication    | 是一个 Web 应用程序。                                        |
| @ConditionalOnNotWebApplication | 不是一个 Web 应用程序。                                      |

#### 4、老生常谈的Spring Boot配置文件地址优先级话题

1. 命令行参数
2. 来自java:comp/env的JNDI属性
3. Java系统属性（System.getProperties()）
4. 操作系统环境变量
5. RandomValuePropertySource配置的random.*属性值
6. jar包外部的application.properties或application.yml
7. jar包内部的application.properties或application.yml
8. @Configuration注解类上的@PropertySource
9. 通过SpringApplication.setDefaultProperties指定的默认属性

其中的配置文件能放在四个位置生效：

1. 外置于应用程序同级的config文件夹下
2. 应用程序同级文件夹下
3. 应用程序内的config文件夹下
4. 应用程序内的classpath下

#### 5、使用日志

首先引入目前性能最好的日志框架log4j2：

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

可以象征性的写几个配置：

```properties
logging.level.root=WARN
logging.level.org.springframework.security=DEBUG
```

#### 6、多环境配置

如何应对多环境的配置需求呢？Spring Boot提供了profile类型的匹配机制，使得多环境的配置集成在同一个应用程序里。

1. 配置类加上profile注解

   ```java
   @Profile("prod")
   ```

2. application.properties加上激活哪个profile

   ```properties
   spring.profiles.active=prod
   ```

3. 增加一个application-prod.properties文件

```properties
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/university?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
```

使用yml配置更简单，一个配置文件定义多个环境配置

```yaml
loggig:
  level:
    root: INFO


---

spring:
  profiles: prod

logging:
  level:
    root: DEBUG


---

spring:
  profiles: dev

logging:
  level:
    root: WARN
  file:
    name: BookWorm
    path: /tmp/
```

#### 7、集成测试

--使用springboottest模拟web容器启动一个controller进行端口访问

--使用selenium模拟浏览器进行页面测试

Spring不直接实现这些测试组件，它提供了松耦合的、依赖注入、接口驱动的设计供简化程序猿的测试代码，通过JUnit类运行器加载applicationcontext注入到测试组件中。Spring Boot额外提供了配置加载器，可以将外置配置加载进来。

