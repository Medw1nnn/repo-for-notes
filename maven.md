## 常用命令

mvn compile 编译 --执行后maven通过dependency找到需要用到的jar包（网络或jar仓库）

- mvn test 测试


- mvn clean 清空


- mvn package 打包 --将项目打包为jar


- mvn install安装 。即把编译好的项目安装到本地库中。服务端的文件改动之后如果发现没有效果的时候要记得问题可能是没有编译，这时候可以使用maven的install命令编译一下 

- mvn archetype 骨架生成


- maven项目一定要按照骨架创建

将项目分成多个模块分别开发，之后用dependency引入 

## maven仓库

分为：**本地仓库、中心仓库、私有仓库**

用于存放jar

默认路径：我的文档/.m2/repository。可在conf/settings里设置

中央工程地址（search.maven.org）也可更改为阿里巴巴仓库

在mvnrepository.com查找dependency

## 其他

easymock？dbunit？log4j？

maven隐藏参数：如${project.groupId}等

新建项目只能选**maven-archetype-webapp** 

## 依赖

### 依赖范围

<scope>:

compile--在编译时加入依赖（默认）

provided--编译和测试时使用该包，打包为war之后不加入（如servlet-api，在tomcat里有。否则会产生冲突）

runtime--只运行时依赖（如mysql-connector）

test（重要）--在测试范围有效，编译和打包都不使用此依赖（如junit）能提高运行效率

### 依赖传递

即A-->B-->C,则A-->C

只会传递compile的，不会传递test

依赖于不同module时，若scope=test不会传递依赖

### 依赖冲突

解决方法：

1.**依赖级别相同**：project a依赖于1.0包，project b依赖于2.0包，project C依赖于a、b，此时在C中先声明哪一个包就依赖哪一个  

2.**依赖级别不同**：自动选择依赖级别短（层级少的）的那个的版本

3.**用依赖排除**

### 依赖排除

在不想使用的dependency里使用<exclusion>.

如a依赖b，而不想使用b项目依赖的commons-logging版本，则在b的dependency中加入：

```
<exclusions>    
    <exclusion>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
    </exclusion>
</exclusions>
```

## 聚合和继承

**聚合aggregation：**

在几个模块的根目录中创建pom.xml，集中编译

即新建在根目录新建一个module

**继承**：（统一版本管理）

module继承于某个根类，避免dependency重复

建立**parent模块**加上  

```
<modules>
	child modules
</modules>
<parent>
	<groupId><..>
	<artifactId><..>
	<version><..>
	<relativePath>(../user-parent/)pom.xml</relativePath>
</parent>
```

此时在child模块中不用再写version和scope，会自动找到parent对应起来

parent可以是聚合模块

- parent的pom.xml可以放在工程的根目录下面,若这样做需将上面括号里的去掉
- **继承的绝对路径是pom的文件，聚合是模块的位置**

## pom.xml

- groupId--项目全称 如cn.medwin.test

- artifactId--模块名称

- version--版本 1.0.0-SNAPSHOT (快照版本，alpha 项目组内部测试版本，beta 公测版，Release(RC)释放版本，GA 最终版本)

   xxx0.0.1-SNAPSHOT-->xxx0.0.1-Release-->xxx1.0.1-SNAPSHOT		

  ​				-->xxx0.1.1-SS分支-->xxx0.1.1-Release-->两个分支合并为xxx1.0.1-Release

 