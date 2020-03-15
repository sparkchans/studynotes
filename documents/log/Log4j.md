##### 配置

对于 Maven 项目，配置文件需要放在 Resource 目录下。日志级别从低到高为 DEBUG，INFO，WARN，ERROR

```properties
# 设置哪个级别及以上的日志才输出，以及输出的位置
# 这里指定了输出目的地为 stdout, debug, error
log4j.rootLogger = debug, stdout, D, E

# 这里定义 stdout 目的地
log4j.appender.stdout = org.apache.log4j.ConsoleApppender
log4j.appender.stdout.target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss} method:%l%n%m%n

# 输出 Debug 级别以上的日志到 E:/logs/log.log
log4j.appender.debug = org.apache.log4j.DailyRollingFileAppender 
log4j.appender.debug.File = E:/logs/log.log
log4j.appender.debug.Append = true
log4j.appender.debug.Threshold = DEBUG
log4j.appender.debug.layout = org.apache.log4j.PatternLayout
log4j.appender.debug.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ]

# 输出 Error 级别以上的日志到 E:/logs/error.log
log4j.appender.error = org.apache.log4j.DailyRollingFileAppender 
log4j.appender.error.File = E:/logs/error.log
log4j.appender.error.Append = true
log4j.appender.error.Threshold = DEBUG
log4j.appender.error.layout = org.apache.log4j.PatternLayout
log4j.appender.error.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ]
########## Appender 类型 ##########
# ConsoleAppender 控制台
# FileAppender 文件
# DailyRollingFileAppender 每天产生一个日志文件
# RollingFileAppender 文件达到指定尺寸产生新的文件
# WriterAppender 以流格式发送到任意位置
##########  Layout 类型  ##########
# HTMLLayout HTML 表格形式布局
# PatternLayou 灵活指定布局
# SimpleLayout 包含日志信息的级别和信息字符串
# TTCCLayout 包含日志产生的时间，线程，类别等信息
##########     格式化    ##########
# %p 输出优先级
# %r 输出自应用启动到输出该 log 信息耗费的毫秒数
# %c 输出所属的类名
# %t 输出该日志时间的线程名
# %d 输出日志时间点的日期或时间
# %l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(TestLog4.java:10)
```



##### 参考

[1] : [No appenders could be found for logger(log4j)?](https://stackoverflow.com/questions/12532339/no-appenders-could-be-found-for-loggerlog4j)

[2] : [log4j:WARN No appenders could be found for logger 解决办法](https://blog.csdn.net/chw0629/article/details/80567936)

[3] : [Log4j详细使用教程](https://blog.csdn.net/Evankaka/article/details/45815047)

[4] : [精通Log4j配置](http://blog.zollty.com/b/archive/proficient-in-log4j-config.html)