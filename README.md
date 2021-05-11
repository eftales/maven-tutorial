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


## dependencies
1. 添加存储在 mvn 中央库中的依赖


2. 添加本地依赖

## advanced
1. 用mvn创建 spring boot 项目
