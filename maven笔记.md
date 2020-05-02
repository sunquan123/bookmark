# maven
## 1、mac下配置maven地址

编辑.bash_profile，添加
export M2_HOME=(maven的地址)
export PATH=$PATH:M2_HOME/bin
保存完文件，执行source .bash_profile
验证，执行mvn -v

## 2、mvn install时报错：找不到配置文件config.properties

mvn编译时未将该properties类型文件识别，影响了打包时查找此文件。
解决：在pom中增加项目路径下对.properties文件的识别

```
 <resource>
                <directory>src/main/resource</directory>
                <includes>
                    <include>**/*.properties</include>
                </includes>
                <filtering>false</filtering>
            </resource>
```

## 3、mvn package install deploy

package命令完成了项目编译、单元测试、打包功能，但没有把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库
install命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库，但没有布署到远程maven私服仓库
deploy命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库