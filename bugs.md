## mybatis连接mysql的jdbc驱动引发的问题

info：

Caused by: java.sql.SQLException: The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support. 

解决：

在jdbcUrl=jdbc:mysql://localhost:3306/spring 后加上

?serverTimezone=UTC

## Mapped Statements collection does not contain value for解决

1.mybatis的映射文件的命令空间与接口的全限定名不一致;

2有可能mybatis的映射文件名字与接口的类名字不一致;

3.还有一种情况就是接口声明的方法在映射文件里面没有；

4.mapper包中的mapper.xml没有编译到targger中， maven的配置文件可能有问题，即没有配置build的resources 。

## 新建SqlSessionFactory产生空指针

解决：maven中，conf.xml等资源都应放到resources文件夹中，并在pom.xml中配置，格式如：

```
<directory>src/main/resource</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
          <include>**/*.tld</include>
        </includes>
        <filtering>false</filtering>
      </resource>
```

## Mybatis无法获取单个属性进行判空，需获取整个对象

如 要通过获取是否有id检查商家是否存在，产生空指针