title: maven的安装及使用
date: 2015-09-15 17:32:44
categories:
- java
- maven
tags: maven
---

## maven的安装及使用     

### 目标    

- 安装
- 使用maven搞定HelloWorld
- 一些构建命令




### 安装    

下载地址: http://maven.apache.org/download.cgi    

<!-- more -->

![](/images/java/maven/lesson/1.png)

而选其一进行下载.解压.放置到一个你喜欢的文件夹,切记不要删除.配置环境    
确保在系统环境变量中配置了`JAVA_HOME`的键值,然后再添加一个`MAVEN_HOME`    
配置系统全局的,`sudo vi /etc/profile`,在后面加入内容即可,不要输入错了,否则臣妾真的救不了你噢

![](/images/java/maven/lesson/2.png)

```
    JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_60.jdk/Contents/Home   ## MAC下,jdk就在这个目录,linux安装配置请百度谷歌

    export PATH=$JAVA_HOME/bin:$PATH

    MAVEN_HOME=/usr/local/maven/maven-3.3.3     ## 这里是我解压存放的路径

    export PATH=$MAVEN_HOME/bin:$PATH

    export JAVA_HOME        ## 该键不仅需要加入PATH(用于终端使用java命令),maven也会使用,不加入,你会一直呵呵的

    export MAVEN_HOME       
```

使用`mvn -v`来测试是否安装成功.    

### 使用maven搞定HelloWorld

先来说说我们的文件夹格式    

```
maven01
├─ src
|   ├─ main
|   |   └─ java
|   |       └─ 包名(java的包名就是无数个文件夹,不写了com.xxx.maven01.model,四个文件夹,自己去建)
|   |           └─ 类文件.java
|   └─ test
|       └─ java
|           └─ 包名(和上面的包名是一样的,写测试用例的而已com.xxx.maven01.model)
|               └─ 类文件.java
└─ pom.xml
```

下面写代码,类和测试类都写,是为了找一个依赖junit,maven有一个中央仓库在远程服务器中,一般情况下,我们依赖的jar包都在上面;但是一会儿我们会说,maven其实先在本地仓库中查找jar包,如果没有才会在中央服务器中下载jar包,所以第一次使用maven需要下载很多东西    

main->java->...>HelloWorld.java:    

``` code
    package com.xxx.maven01.model;

    public class HelloWorld {
        public String sayHello() {
            return "Hello World!";
        }
    }
```

test->java->...>HelloWorldTest.java:    

``` code
    package com.xxx.maven01.model;

    import org.junit.*;             //能找到个依赖不容易,简而高效嘛
    import org.junit.Assert.*;

    public class HelloWorldTest {
        
        @Test
        public void testHello() {
            Assert.assertEquals("Hello World!", new HelloWorld().sayHello());
        }   
    }
```

好,现在我们来写`pom.xml`,maven就靠解析这玩意儿知道我们需要依赖什么,然后才去下载的    

``` code
    <?xml version="1.0" encoding="UTF-8"?>

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

      <modelVersion>4.0.0</modelVersion> <!-- maven版本,固定的 -->

      <groupId>com.xxx.maven01</groupId> <!-- 组织名,公司名反写+项目名 -->
      <artifactId>maven01-model</artifactId> <!-- 项目名-模块名 -->
      <version>0.0.1SNAPSHOT</version> <!-- 指定版本号,这个你随意,遵循版本规则来 -->

      <dependencies>
        <dependency>
          <groupId>junit</groupId> <!-- 在中央处理器中的jar包,唯一,简写 -->
          <artifactId>junit</artifactId> 
          <version>4.10</version>   
        </dependency>
      </dependencies>
    </project>
```

最后一步,在`maven01`目录下执行`mvn compile`编译生成字节码,`mvn test`执行测试,`mvn package`打包jar,`mvn clean`清理生成的字节码等等,`mvn install`将生成的jar安装到本地仓库,生成的字节码和打包的jar,在target目录下找       

### 一些构建命令

上面我们讲述了`mvn install`能将生成的jar安装到本地仓库,现在我们要来使用本地仓库中的jar    
目录结构一致,这温暖一如从前       

main->java->...>Hello.java:    

``` code
    package com.xxx.maven02.util;

    import com.xxx.maven01.model.HelloWorld;    //引入jar包种的类,本地仓库,我们打包的,记不得自宫吧

    public class Hello {
        public String sayHi() {
            return new HelloWorld().sayHello(); //调用我们自己写的类实例方法
        }
    }
```

test->java->...->HelloTest.java:    

``` code
package com.xxx.maven02.util;

import org.junit.*;
import org.junit.Assert.*;

public class HelloTest {
    
    @Test
    public void testHi() {
        Assert.assertEquals("Hello World!", new Hello().sayHi());
    }   
}
```

搞定,我们应该注意的是pom的写法:    

``` code
    <?xml version="1.0" encoding="UTF-8"?>

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

          <modelVersion>4.0.0</modelVersion>

          <groupId>com.xinpu.maven02</groupId>
          <artifactId>maven02-util</artifactId>
          <version>0.0.1</version>

          <dependencies>
            <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.10</version>
            </dependency>
            <dependency>
              <groupId>com.xinpu.maven01</groupId>  
              <artifactId>maven01-model</artifactId>
              <version>0.0.1SNAPSHOT</version>
            </dependency>
          </dependencies>
        </project>
```

执行`mvn compile`,`BUILD SUCCESS`,请先安装`maven01-model`的jar到本地仓库    

通常在我们开始一个新的项目的时候,我们会手动创建很多目录来管理资源,就比如我们之前创建目录结构,是有多累,我们来讲述使用mvn插件手动帮我们生成目录结构,呵呵,你肯定觉得我在逗你.     

使用`mvn archetype:generate`插件来生成目录结构    

``` text
  //回车跳过
  Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): 644: 
  
  //选择版本,6,回车
  Choose a number: 6: 6

  //默认groupId,似曾相似, com.xxx.maven03
  Define value for property 'groupId': : com.xxx.maven03

  //默认artifactId, maven03-service
  Define value for property 'artifactId': : maven03-service

  //版本 1.0.0
  Define value for property 'version':  1.0-SNAPSHOT: : 1.0.0

  //包名 com.xxx.maven03.service
  Define value for property 'package':  com.xxx.maven03: : com.xxx.maven03.service
  
  // y
   Y: : 
```

搞定,生成成功了,自己看看,有点晕吧,包名就是无数个文件夹.      
还有一种方式,就是直接在命令中指定这一堆参数,到时候可以少输入一些,而已    

```
  mvn archetype:generate -DgroupId=com.xxx.maven04 -DartifactId=maven04-demo -Dversion=1.0.0SNAPSHOT -Dpackage=com.xxx.maven04.demo
```

- **仓库**   

当使用mvn构建项目时,先在本地仓库中查找jar包,没有就会前往远程中央服务器查找,没有就报错    

在maven目录的lib下`maven-model-builder-3.3.3.jar`解压,找到`pom-4.0.0.xml`,可以看到

``` code
  <repositories>
    <repository>
      <id>central</id>
      <name>Central Repository</name>
      <url>https://repo.maven.apache.org/maven2</url>
      <layout>default</layout>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>
```

记录了maven所使用的中央仓库,访问https://repo1.maven.org/maven2/,列出了仓库中所有包含的jar    
加速国内访问可以设置国内镜像源,maven目录下conf文件夹`settings.xml`的146行mirrors    

示例,配置了就只会使用镜像源了,所以一般不使用镜像源:    

``` code
    <mirror>
      <id>maven.net.cn</id>
      <mirrorOf>central</mirrorOf>
      <name>central mirror in China</name>
      <url>http://maven.oschina.net/content/groups/public/</url>
    </mirror>
```

- 修改本地仓库位置

默认本地仓库为用户主目录的.m2文件夹下    

```
root
  L .m2
      L repository
                L ...
```

配置刚才的`settings.xml`,53行,下面是示例   
将settings.xml复制到仓库的根目录下,以后升级maven可以不用再配置settings    

```
  <localRepository>/usr/loca/repo</localRepository>
```

- 生命周期

完整的项目构建过程包括:    
`清理>编译>测试>打包>集成测试>验证>部署`

maven生命周期:   
* clean   清理项目
* default 构建项目
* site    生成项目站点

clean、compile、test、package、install这五个过程,后面的命令都会执行前面的命令    

clean的三个阶段:    

* pre-clean  执行清理前的工作
* clean      清理上一次构建生成的所有文件
* post-clean 执行清理后的文件

default阶段`compile test package install`都属于默认阶段    

site的三个阶段:   

* pre-site    在生成项目站点前要完成的工作
* site        生成项目的站点文档
* post-site   在生成项目站点后要完成的工作
* site-deploy 发布生成的站点到服务器上


- maven插件

例举一个`source`插件,在maven01的pom中dependencies后配置   
指定在运行package命令的时候打包源代码

``` code
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
        <version>2.4</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>jar-no-fork</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

`mvn package`会帮我们打包源码,生成jar源码文件在target目录下

- 修改`settings.xml`使用eclipse创建项目使用对应的jre版本

找到182行,添加    

``` code
    <profile>
      <id>jdk-1.8</id>

      <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
      </activation>

      <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
      </properties>
    </profile>
```

- `pom.xml`简述

```
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <!-- 当前pom版本 -->
  <modelVersion>4.0.0</modelVersion>

  <groupId>反写的公司网址+项目名</groupId>
  <artifactId>项目名+模块名</artifactId>
  <!--
    snapshot 快照
    alpha 内部测试
    beta 公测
    release 稳定
    GA 正式发布
  -->
  <version>当前项目的版本号,1.0.0,大版本.分支.小版本</version>
  <packaging>默认打包为jar,可以不写, 或者war、zip、pom</packaging>
  <name>项目描述名</name>
  <url>项目网址</url>
  <description>项目描述</description>
  <developers>开发人员名单</developers>
  <licenses>开源协议</licenses>
  <organization>组织信息</organization>

  <dependencies>
    <dependency>
      <groupId></groupId>
      <artifactId></artifactId>
      <version></version>
      <type></type>
      <scope></scope><!-- 依赖范围 -->
      <optional></optional><!-- 设置依赖是否可选,false子项目默认自动引入,true子项目必须自行引入 -->
      <!-- 解除依赖传递 -->
      <exclusions>
        <exclusion>
        </exclusion>
      </exclusions>
    </dependency>
  </dependencies>
  <!-- 依赖管理 -->
  <dependencyManagement>
    <dependencies>
      <dependency></dependency>
    </dependencies>
  </dependencyManagement>
  <build>
    <!-- 插件 -->
    <plugins>
      <plugin>
        <groupId></groupId>
        <artifactId></artifactId>
        <version></version>
      </plugin>
    </plugins>
  </build>
  <parent></parent>
  <modules>
    <module></module>
  </modules>
</project>
```







