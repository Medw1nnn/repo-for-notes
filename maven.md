## maven简介

[Maven](https://baike.baidu.com/item/Maven)项目对象模型(POM)，是可以通过一小段描述信息来管理项目的构建，报告和文档的软件[项目管理工具](https://baike.baidu.com/item/%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7) 

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

分为：**本地仓库、中心仓库、私有仓库**（nexus）

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
<dependencyManament>
	dependencies
</dependencyManament>
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

- propertise参数

- 其他maven内置属性

## 搭建私服

用于企业内部

Sonatype nexus

## 生命周期

LifecyclePhase：

1. **clean** ：包含三个步骤
   1. preclean
   2. clean
2. **compile** ：包含hin多个步骤
3. site ：成为站点 

## 插件 plugin

用于执行compile、clean等各步骤

如compile插件有compile、test compile两个目标

#### 自定义插件

要把某插件绑定到生命周期中，在parent的pom.xml中加入

如source-plugin插件可将源文件一起打包进jar：

```
<bulid>
	<plugins>
		<plugin>
			...
			<execution>
				<phase>
					<!--要绑定的周期 如compile则在compile之后执行-->
				</phase>
				<goals>
					<goal><!--目标从官网查阅--></goal>
				</goals>
			</execution>
			<configruation>
			</...>
		...
	...
</build>
```

- 各种插件见maven.apache.org/plugins
- 使用插件依然可以指定依赖
- 主要插件：surefire...

## 测试、Junit

**junit注解：**

1.@Test: 测试方法

　　　　 a)(expected=XXException.class)如果程序的异常和XXException.class一样，则测试通过　　　　

​		b)(timeout=100)如果程序的执行能在100毫秒之内完成，则测试通过

　　2.@Ignore: 被忽略的测试方法：加上之后，暂时不运行此段代码

　　3.@Before: 每一个测试方法之前运行

　　4.@After: 每一个测试方法之后运行

　　5.@BeforeClass: 方法必须必须要是静态方法（static 声明），所有测试开始之前运行，注意区分before，是所有测试方法

　　6.@AfterClass: 方法必须要是静态方法（static 声明），所有测试结束之后运行，注意区分 @After

**编写测试类的原则：**　

　　  ①测试方法上必须使用@Test进行修饰

​        ②测试方法必须使用public void 进行修饰，不能带任何的参数

​        ③新建一个源代码目录来存放我们的测试代码，即将测试代码和项目业务代码分开

​        ④测试类所在的包名应该和被测试类所在的包名保持一致

​        ⑤测试单元中的每个方法必须可以独立测试，测试方法间不能有任何的依赖

​        ⑥测试类使用Test作为类名的后缀（不是必须）

​        ⑦测试方法使用test作为方法名的前缀（不是必须）
