## 配置文件

Log4J配置文件的基本格式如下： 

```
#配置根Logger
log4j.rootLogger  =   [ level ]   ,  appenderName1 ,  appenderName2 ,  …
#配置日志信息输出目的地Appender
log4j.appender.appenderName  =  fully.qualified.name.of.appender.class 
log4j.appender.appenderName.option1  =  value1 
...
log4j.appender.appenderName.optionN  =  valueN 
#配置日志信息的格式（布局）
log4j.appender.appenderName.layout  =  fully.qualified.name.of.layout.class 
log4j.appender.appenderName.layout.option1  =  value1 
… 
log4j.appender.appenderName.layout.optionN  =  valueN 
```

- 其中 **[level]** 是日志输出级别，

  ERROR 为严重错误 主要是程序的错误 

  WARN 为一般警告，比如session丢失 

  INFO 为一般要显示的信息，比如登录登出 

  DEBUG 为程序的调试信息 

- Appender 为日志输出目的地，Log4j提供的appender有以下几种： 

  org.apache.log4j.ConsoleAppender（控制台）， ![log4j配置详解 - stone - stonexmx 的博客](http://www.blogjava.net/Images/OutliningIndicators/None.gif)org.apache.log4j.FileAppender（文件）， ![log4j配置详解 - stone - stonexmx 的博客](http://www.blogjava.net/Images/OutliningIndicators/None.gif)org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）， ![log4j配置详解 - stone - stonexmx 的博客](http://www.blogjava.net/Images/OutliningIndicators/None.gif)org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）， ![log4j配置详解 - stone - stonexmx 的博客](http://www.blogjava.net/Images/OutliningIndicators/None.gif)org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方） 

- **Layout**：日志输出格式 
- **打印参数:** Log4J采用类似C语言中的printf函数的打印格式格式化日志信息 

**. 在代码中初始化Logger:** 

 1）在程序中调用**BasicConfigurator.configure()**方法：给根记录器增加一个ConsoleAppender，输出格式通过PatternLayout设为**"%-4r [%t] %-5p %c %x - %m%n"**，还有根记录器的默认级别是**Level.DEBUG**.  2）配置放在文件里，通过命令行参数传递文件名字，通过**PropertyConfigurator.configure(args[x])**解析并配置； 3）配置放在文件里，通过环境变量传递文件名等信息，利用log4j默认的初始化过程解析并配置； 4）配置放在文件里，通过应用服务器配置传递文件名等信息，利用一个特殊的servlet来完成配置。 

**为不同的 Appender 设置日志输出级别：** 

当调试系统时，我们往往注意的只是异常级别的日志输出，但是通常所有级别的输出都是放在一个文件里的，如果日志输出的级别是BUG！？那就慢慢去找吧。 这时我们也许会想要是能把异常信息单独输出到一个文件里该多好啊。当然可以，Log4j已经提供了这样的功能，我们只需要在配置中修改**Appender**的**Threshold** 就能实现 

## log4j.properties 使用 

一.**参数意义说明 输出级别的种类**

 ERROR、WARN、INFO、DEBUG ERROR 为严重错误 主要是程序的错误 

WARN 为一般警告，比如session丢失 

INFO 为一般要显示的信息，比如登录登出 

DEBUG 为程序的调试信息 配置日志信息输出目的地 

log4j.appender.appenderName = fully.qualified.name.of.appender.class 1.org.apache.log4j.ConsoleAppender（控制台） 

2.org.apache.log4j.FileAppender（文件） 

3.org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件） 4.org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件） 5.org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方） 

**配置日志信息的格式** 

log4j.appender.appenderName.layout = fully.qualified.name.of.layout.class 1.org.apache.log4j.HTMLLayout（以HTML表格形式布局）， 

2.org.apache.log4j.PatternLayout（可以灵活地指定布局模式），

 3.org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）， 4.org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息） 

**控制台选项** 

Threshold=DEBUG:指定日志消息的输出最低层次。 

ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 

Target=System.err：默认情况下是：System.out,指定输出控制台 

FileAppender 选项 Threshold=DEBUF:指定日志消息的输出最低层次。 

ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 

File=mylog.txt:指定消息输出到mylog.txt文件。 

Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。 

RollingFileAppender 选项 Threshold=DEBUG:指定日志消息的输出最低层次。

 ImmediateFlush=true:默认值是true,意谓着所有的消息都会被立即输出。 

File=mylog.txt:指定消息输出到mylog.txt文件。 

Append=false:默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。 MaxFileSize=100KB: 后缀可以是KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动，即将原来的内容移到mylog.log.1文件。 

MaxBackupIndex=2:指定可以产生的滚动文件的最大数。 log4j.appender.A1.layout.ConversionPattern=%-4r %-5p %d{yyyy-MM-dd HH:mm:ssS} %c %m%n 

**日志信息格式中几个符号所代表的含义：**  

-X号: X信息输出时左对齐；  

%p: 输出日志信息优先级，即DEBUG，INFO，WARN，ERROR，FATAL,  

%d: 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss,SSS}，输出类似：2002年10月18日 22：10：28，921  

%r: 输出自应用启动到输出该log信息耗费的毫秒数  

%c: 输出日志信息所属的类目，通常就是所在类的全名  

%t: 输出产生该日志事件的线程名  

%l: 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main (TestLog4.java:10)  

%x: 输出和当前线程相关联的NDC(嵌套诊断环境),尤其用到像java servlets这样的多客户多线程的应用中。

 %%: 输出一个"%"字符  

%F: 输出日志消息产生时所在的文件名称  

%L: 输出代码中的行号  

%m: 输出代码中指定的消息,产生的日志具体信息  

%n: 输出一个回车换行符，Windows平台为"\r\n"，Unix平台为"\n"输出日志信息换行  可以在%与模式字符之间加上修饰符来控制其最小宽度、最大宽度、和文本的对齐方式。如：  1)

%20c：指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，默认的情况下右对齐。  

2)%-20c:指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，"-"号指定左对齐。  

3)%.30c:指定输出category的名称，最大的宽度是30，如果category的名称大于30的话，就会将左边多出的字符截掉，但小于30的话也不会有空格。  

4)%20.30c:如果category的名称小于20就补空格，并且右对齐，如果其名称长于30字符，就从左边较远输出的字符截掉。 

## example：

```
# 根日志记录器，如果没有找到合适的appender的时候，默认使用根日志记录器
# 下面一行表示默认级别为error，输出到console和file两个appender
log4j.rootLogger = error,console,file

# 下面的com.fkjava属于我们自己程序里面的日志记录器的前缀
# 一般我们自己的日志记录器，都是通过Class创建的，包含了类的全名。
# log4j 会基于包名精度优先模糊匹配。下面表示输出debug级别的信息。
# 级别一共有6个：OFF、ERROR、WARN、INFO、DEBUG、ALL
log4j.logger.org.fkjava=debug
log4j.logger.org.fkjava.log4j1=error

## APPENDERS ##
# 输出目标，下面这个输出目标名为console，目标的实现程序为ConsoleAppender，其实就是System.out
log4j.appender.console = org.apache.log4j.ConsoleAppender
# 下面两行表示日志输出的内容格式
log4j.appender.console.layout = org.apache.log4j.PatternLayout
# %p 级别
# %m 消息的内容
# %n 换行
# %d 时间
# %l 日志输出的类名，包括方法名、行号、文件名！
# %t 线程名称
# %n 换行
log4j.appender.console.layout.ConversionPattern = %p: %m%n      %d %l %t%n

# 下面使用DailyRollingFileAppender作为输出目标，名为file。
# 每天产生一个日志记录文件，记录到C:/log/oa.log文件里面。
# 第二天的时候，就会把oa.log重命名为oa_2017-6-30.log这种格式！
log4j.appender.file = org.apache.log4j.DailyRollingFileAppender
log4j.appender.file.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.file.File = C:/log/oa.log
log4j.appender.file.layout = org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern = %p: %m%n      %d %l %t%n
```

