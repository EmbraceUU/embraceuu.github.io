# 记录Log4j的配置范本

记录Log4j在项目中的配置范本.
<!--more-->

下面是properties的配置

```yml
log4j.rootLogger=info,console, D, E
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.Threshold=DEBUG
log4j.appender.console.Target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%-5p][%d][%t][%l][logid(%X{request.logid})][uri(%X{request.uri})]: %m %x %n
log4j.appender.console.Encoding=UTF-8

log4j.appender.D=org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.layout=org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern=[%-5p][%d][%t][%l][logid(%X{request.logid})][uri(%X{request.uri})]: %m %x %n
log4j.appender.D.DatePattern='.'yyyyMMdd
log4j.appender.D.Threshold = DEBUG
log4j.appender.D.File=./logs/info/tc.log
log4j.appender.D.Encoding=UTF-8

log4j.appender.E=org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.layout=org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern=[%-5p][%d][%t][%l][logid(%X{request.logid})][uri(%X{request.uri})]: %m %x %n
log4j.appender.E.DatePattern='.'yyyyMMdd
log4j.appender.E.Threshold = DEBUG
log4j.appender.E.File=./logs/error/tc.log
log4j.appender.E.Encoding=UTF-8 
```

JAVA中给某个线程配置Log

```java
RuntimeMXBean rt = ManagementFactory.getRuntimeMXBean(); 
String pid = rt.getName(); 

MDC.put(LOG_REQUEST_LOGID, Thread.currentThread().getId());
MDC.put(LOG_REQUEST_PID, pid);
```
