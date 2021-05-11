# maven 完全手册

## dependency 
ubuntu 16.04

java == 8

mvn == 3.3.9

- notices

    java11 在 ubuntu16.04 上和 mvn 配合起来有 bug

## catalogue


## install java and mvn
```
sudo apt update
sudo apt install openjdk-8-jdk
sudo apt install maven
```

## create a mvn project from command line
```
mvn archetype:generate -DgroupId=com.test -DartifactId=folderName -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

-DgroupeId 组id（也是你的文件夹嵌套结构）

-DartifactId 存放mvn项目的文件夹的名称

-DarchetypeArtifactId 从模板创建工程

-DinteractiveMode 非交互模式，没有输入的参数直接用默认值

## clean compile
```
cd folderName
mvn clean
mvn compile 
```

执行上述命令，会在 `folderName/target/classes/com/test/` 下生成编译后的文件 App.class

执行一下！

```
cd target/classes
java com.test.App
# Hello World!
```

注意这里并没有到 .class 文件所在的路径，而是在 `包` 的路径下，用全名执行

## test
这里的test专指 junit 的测试

输入 `mvn test` 之后，会去执行位于 `test/java` 文件夹下的 `com.test.AppTest.testApp()` 函数
 
从 `junit` 的名字也可以看出来，这个是“单元测试”，因此 `testApp()` 函数里一般不执行 `App.main()`  函数，而是去测试一些子函数


## package
我们直接执行 `mvn package`，就会在 `target` 这个文件夹下生成对应的 `.jar` 文件。但是这个 `jar` 文件并不能通过 `java- jar folderName.jar` 来执行，会报错:

```
no main manifest attribute, in folderName-1.0-SNAPSHOT.jar
```

这是因为我们没有指定这个 .jar 的入口类。

jar 文件的本质上是一个压缩了编译后的 .class 文件和资源文件的 rar 格式的压缩包。

直接把 jar 文件作为参数交给 java 来执行，java 就有点不清楚该从哪个文件执行起...我们只需要增加一个 manifest 文件即可解决

这里并不推荐手写 manifest 文件，而是用 mvn 的插件`maven-jar-plugin`自动生成

在 .pom 文件的 <url> 之后 <dependencies> 之前加入:
```
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <configuration>
                <archive>
                    <manifest>
                        <mainClass>com.test.App</mainClass>
                        <addClasspath>true</addClasspath>
                    </manifest>
                </archive>
            </configuration>
        </plugin>
    </plugins>
</build>

```

再试试？
```
mvn clean package
java -jar target/folderName-1.0-SNAPSHOT.jar
```

## dependencies
1. 添加存储在 mvn 中央库中的依赖

    这节我们将以 log4j 为例，演示如何添加官方依赖。我们先在[这个网站](https://search.maven.org/)搜索log4j的唯一ID。

    ```
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    ```

    把这些粘贴到 .pom 的 <dependencies> 标签下;增加 lo4j配置文件;修改一下 App.java 文件...

    试试？
    ```
    mvn clean package
    java -jar target/folderName-1.0-SNAPSHOT.jar 
    ```

    很不幸，报错了，他说，找不到 log4j这个类。为什么编译的时候没有问题，运行的时候出问题了呢？

    ```
    Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/log4j/Logger
    ```

    原来是运行的时候找不到第三方库；添加 assembly 依赖

    ```
    <plugin>  
        <groupId>org.apache.maven.plugins</groupId>  
        <artifactId>maven-assembly-plugin</artifactId>  
        <version>2.5.5</version>  
        <configuration>  
            <archive>  
                <manifest>  
                    <mainClass>com.test.App</mainClass>  
                </manifest>  
            </archive>  
            <descriptorRefs>  
                <descriptorRef>jar-with-dependencies</descriptorRef>  
            </descriptorRefs>  
        </configuration>  
    </plugin> 
    ```

    再试试？
    ```
    mvn clean  package assembly:single
    java -jar target/folderName-1.0-SNAPSHOT.jar 
    ```


2. 添加本地依赖

    先把本地依赖通过 mvn install 安装到本地的 mvn 仓库之后，再用 1 的方法引入

    以 javastruct库为例

    将 javastruct-0.1.jar 拷贝到 lib 路径下，然后 install

    ```
    cd lib
    mvn install:install-file -DgroupId=com.javastruct -DartifactId=javastruct -Dversion=0.1 -Dpackaging=jar -Dfile=javastruct-0.1.jar
    ```

    安装后就可以使用标准 mvn 格式将 javastruct 导入了
    ```
    <dependency>
        <groupId>com.javastruct</groupId>
        <artifactId>javastruct</artifactId>
        <version>0.1</version>
    </dependency>
    ```

    试试？
    ```
    mvn clean  package assembly:single
    java -jar target/folderName-1.0-SNAPSHOT.jar 
    ```

## advanced
1. 用mvn创建 spring boot 项目

## Core Concepts
maven只定义了项目的生命周期，但是并没有规定生命周期的实现。生命周期都是由插件实现的。

为了简化配置，特别常用的插件（clean compile package）就直接默认集成在了项目中，但是我们也可以根据需要自行更换或引入插件。
