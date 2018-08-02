# The Logging API

在java.util.logging包中主要提供了包括Logger、LogRecord、Handler、Level、Formatter等在内的类以及Filter接口来提供日志服务。
Logger类实例主要用来记录程序组件运行时的日志信息，LogRecord类实例主要用来传递日志请求在日志框架和具体的LogHandlers之间，Handler及其子类实例用来将Logger得到的日志信息输出到指定的位置如Console、File和Socket等，Level主要用来定义日志输出时的级别，Formatter为日志输出格式规范提供支持，Filter主要用来提供比Level更好的细粒度控制服务。
在此基础之上，apache开源了Log4j1.0、Log4j2.0和Logback日志组件，下面是Logback配置示例：
```xml
<!-- 负责写日志,控制台日志 -->
<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">

    <!-- 一是把日志信息转换成字节数组,二是把字节数组写入到输出流 -->
    <encoder>
        <Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
        <charset>UTF-8</charset>
    </encoder>
</appender>

<!-- 文件日志 -->
<appender name="DEBUG" class="ch.qos.logback.core.FileAppender">
    <file>debug.log</file>
    <!-- append: true,日志被追加到文件结尾; false,清空现存文件;默认是true -->
    <append>true</append>
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
        <!-- LevelFilter: 级别过滤器，根据日志级别进行过滤 -->
        <level>DEBUG</level>
        <onMatch>ACCEPT</onMatch>
        <onMismatch>DENY</onMismatch>
    </filter>
    <encoder>
        <Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
        <charset>UTF-8</charset>
    </encoder>
</appender>

<!-- 滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件 -->
<appender name="INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <File>info.log</File>

    <!-- ThresholdFilter:临界值过滤器，过滤掉 TRACE 和 DEBUG 级别的日志 -->
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
        <level>INFO</level>
    </filter>

    <encoder>
        <Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
        <charset>UTF-8</charset>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <!-- 每天生成一个日志文件，保存30天的日志文件
        - 如果隔一段时间没有输出日志，前面过期的日志不会被删除，只有再重新打印日志的时候，会触发删除过期日志的操作。
        -->
        <fileNamePattern>info.%d{yyyy-MM-dd}.log</fileNamePattern>
        <maxHistory>30</maxHistory>
        <TimeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
            <maxFileSize>100MB</maxFileSize>
        </TimeBasedFileNamingAndTriggeringPolicy>
    </rollingPolicy>
</appender >

<!--<!– 异常日志输出 –>-->
<!--<appender name="EXCEPTION" class="ch.qos.logback.core.rolling.RollingFileAppender">-->
    <!--<file>exception.log</file>-->
    <!--<!– 求值过滤器，评估、鉴别日志是否符合指定条件. 需要额外的两个JAR包，commons-compiler.jar和janino.jar –>-->
    <!--<filter class="ch.qos.logback.core.filter.EvaluatorFilter">-->
        <!--<!– 默认为 ch.qos.logback.classic.boolex.JaninoEventEvaluator –>-->
        <!--<evaluator>-->
            <!--<!– 过滤掉所有日志消息中不包含"Exception"字符串的日志 –>-->
            <!--<expression>return message.contains("Exception");</expression>-->
        <!--</evaluator>-->
        <!--<OnMatch>ACCEPT</OnMatch>-->
        <!--<OnMismatch>DENY</OnMismatch>-->
    <!--</filter>-->

    <!--<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">-->
        <!--<!– 触发节点，按固定文件大小生成，超过5M，生成新的日志文件 –>-->
        <!--<maxFileSize>5MB</maxFileSize>-->
    <!--</triggeringPolicy>-->
<!--</appender>-->

<appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>error.log</file>

    <encoder>
        <Pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%5level] [%thread] %logger{0} %msg%n</Pattern>
        <charset>UTF-8</charset>
    </encoder>

    <!-- 按照固定窗口模式生成日志文件，当文件大于20MB时，生成新的日志文件。
    -    窗口大小是1到3，当保存了3个归档文件后，将覆盖最早的日志。
    -    可以指定文件压缩选项
    -->
    <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
        <fileNamePattern>error.%d{yyyy-MM}(%i).log.zip</fileNamePattern>
        <minIndex>1</minIndex>
        <maxIndex>3</maxIndex>
        <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
            <maxFileSize>100MB</maxFileSize>
        </timeBasedFileNamingAndTriggeringPolicy>
        <maxHistory>30</maxHistory>
    </rollingPolicy>
</appender>

<!-- 异步输出 -->
<appender name ="ASYNC" class= "ch.qos.logback.classic.AsyncAppender">
    <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
    <discardingThreshold >0</discardingThreshold>
    <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
    <queueSize>512</queueSize>
    <!-- 添加附加的appender,最多只能添加一个 -->
    <appender-ref ref ="ERROR"/>
</appender>

<!--
- 1.name：包名或类名，用来指定受此logger约束的某一个包或者具体的某一个类
- 2.未设置打印级别，所以继承他的上级<root>的日志级别“DEBUG”
- 3.未设置addtivity，默认为true，将此logger的打印信息向上级传递；
- 4.未设置appender，此logger本身不打印任何信息，级别为“DEBUG”及大于“DEBUG”的日志信息传递给root，
-  root接到下级传递的信息，交给已经配置好的名为“STDOUT”的appender处理，“STDOUT”appender将信息打印到控制台；
-->
<logger name="ch.qos.logback" />

<!--
- 1.将级别为“INFO”及大于“INFO”的日志信息交给此logger指定的名为“STDOUT”的appender处理，在控制台中打出日志，
-   不再向次logger的上级 <logger name="logback"/> 传递打印信息
- 2.level：设置打印级别（TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF），还有一个特殊值INHERITED或者同义词NULL，代表强制执行上级的级别。
-        如果未设置此属性，那么当前logger将会继承上级的级别。
- 3.additivity：为false，表示此logger的打印信息不再向上级传递,如果设置为true，会打印两次
- 4.appender-ref：指定了名字为"STDOUT"的appender。
-->
<logger name="com.weizhi.common.LogMain" level="INFO" additivity="false">
    <appender-ref ref="STDOUT"/>
    <!--<appender-ref ref="DEBUG"/>-->
    <!--<appender-ref ref="EXCEPTION"/>-->
    <!--<appender-ref ref="INFO"/>-->
    <!--<appender-ref ref="ERROR"/>-->
    <appender-ref ref="ASYNC"/>
</logger>


<!--
- 根logger
- level:设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为INHERITED或者同义词NULL。
-       默认是DEBUG。
-appender-ref:可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger
-->
<root level="DEBUG">
    <appender-ref ref="STDOUT"/>
    <!--<appender-ref ref="DEBUG"/>-->
    <!--<appender-ref ref="EXCEPTION"/>-->
    <!--<appender-ref ref="INFO"/>-->
    <appender-ref ref="ASYNC"/>
</root>
```

