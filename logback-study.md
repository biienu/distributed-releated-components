# spring-boot 中的logback日志学习使用

@[TOC](文档目录)



## 1. 日志级别

日志记录器(Logger)的行为分等级的。如下表所示:

| OFF  | FATAL | ERROR | WARN | DEBUG | ALL  |
| ---- | ----- | ----- | ---- | ----- | ---- |

默认情况下，spring boot 从控制台打印出来的日志级别只有INFO及以上级别，可以配置日志级别

```properties
#设置日志级别
loging.level.root=WARN
```

这种方式只能将日志打印在控制台上

***

## 2. Logback日志



spring boot内部使用Logback作为日志实现的框架。

Logback和log4j非常相似，如果你对log4j很熟悉，那对logback很快就会得心应手。

logback相对于log4j的一些优点：<https://blog.csdn.net/caisini_vc/article/details/48551287>

***

## 3. 配置Logback日志



删除appplication.properties中的所有日志配置

安装idea彩色日志插件:	grep-console

resources 中创建logback-spring.xml 文件，该文件内容如下:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration  scan="true" scanPeriod="10 seconds">
    <!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
    <!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
    <!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
    <!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->
    <contextName>logback</contextName>
    <!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
    
    <property name="log.path" value="E:/ProcessDocuments/项目测试/online-education/log" />
    <!-- 彩色日志 -->
    <!-- 配置格式变量：CONSOLE_LOG_PATTERN 彩色日志格式 -->
    <!-- magenta:洋红 -->
    <!-- boldMagenta:粗红-->
    <!-- cyan:青色 -->
    <!-- white:白色 -->
    <!-- magenta:洋红 -->
    <property name="CONSOLE_LOG_PATTERN" value="%yellow(%date{yyyy-MM-dd HH:mm:ss}) |%highlight(%-5level) |%blue(%thread) |%blue(%file:%line) |%green(%logger) |%cyan(%msg%n)"/>
    
    <!--输出到控制台-->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">

        <!--此日志appender是为开发使用，只配置最底级别，控制台输出的日志级别是大于或等于此级别的日志信息-->

        <!-- 例如：如果此处配置了INFO级别，则后面其他位置即使配置了DEBUG级别的日志，也不会被输出 -->

        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">

            <level>INFO</level>

        </filter>

        <encoder>

            <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>

            <!-- 设置字符集 -->

            <charset>UTF-8</charset>

        </encoder>

    </appender>

    <!--输出到文件-->

    <!-- 时间滚动输出 level为 INFO 日志 -->

    <appender name="INFO_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

        <!-- 正在记录的日志文件的路径及文件名 -->

        <file>${log.path}/log_info.log</file>

        <!--日志文件输出格式-->

        <encoder>

            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>

            <charset>UTF-8</charset>

        </encoder>

        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <!-- 每天日志归档路径以及格式 -->

            <fileNamePattern>${log.path}/info/log-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>

            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">

                <maxFileSize>100MB</maxFileSize>

            </timeBasedFileNamingAndTriggeringPolicy>

            <!--日志文件保留天数-->

            <maxHistory>15</maxHistory>

        </rollingPolicy>

        <!-- 此日志文件只记录info级别的 -->

        <filter class="ch.qos.logback.classic.filter.LevelFilter">

            <level>INFO</level>

            <onMatch>ACCEPT</onMatch>

            <onMismatch>DENY</onMismatch>

        </filter>

    </appender>

    <!-- 时间滚动输出 level为 WARN 日志 -->

    <appender name="WARN_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

        <!-- 正在记录的日志文件的路径及文件名 -->

        <file>${log.path}/log_warn.log</file>

        <!--日志文件输出格式-->

        <encoder>

            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>

            <charset>UTF-8</charset> <!-- 此处设置字符集 -->

        </encoder>

        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <fileNamePattern>${log.path}/warn/log-warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">

                <maxFileSize>100MB</maxFileSize>

            </timeBasedFileNamingAndTriggeringPolicy>

            <!--日志文件保留天数-->

            <maxHistory>15</maxHistory>

        </rollingPolicy>

        <!-- 此日志文件只记录warn级别的 -->

        <filter class="ch.qos.logback.classic.filter.LevelFilter">

            <level>warn</level>

            <onMatch>ACCEPT</onMatch>

            <onMismatch>DENY</onMismatch>

        </filter>

    </appender>

    <!-- 时间滚动输出 level为 ERROR 日志 -->
     <appender name="ERROR_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

        <!-- 正在记录的日志文件的路径及文件名 -->

        <file>${log.path}/log_error.log</file>

        <!--日志文件输出格式-->

        <encoder>

            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>

            <charset>UTF-8</charset> <!-- 此处设置字符集 -->

        </encoder>

        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <fileNamePattern>${log.path}/error/log-error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>

            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">

                <maxFileSize>100MB</maxFileSize>

            </timeBasedFileNamingAndTriggeringPolicy>

            <!--日志文件保留天数-->

            <maxHistory>15</maxHistory>

        </rollingPolicy>

        <!-- 此日志文件只记录ERROR级别的 -->

        <filter class="ch.qos.logback.classic.filter.LevelFilter">

            <level>ERROR</level>

            <onMatch>ACCEPT</onMatch>

            <onMismatch>DENY</onMismatch>

        </filter>

    </appender>

    <!--
121
        <logger>用来设置某一个包或者具体的某一个类的日志打印级别、以及指定<appender>。
122
        <logger>仅有一个name属性，
123
        一个可选的level和一个可选的addtivity属性。
124
        name:用来指定受此logger约束的某一个包或者具体的某一个类。
125
        level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，
126
              如果未设置此属性，那么当前logger将会继承上级的级别。
127
    -->

    <!--
129
        使用mybatis的时候，sql语句是debug下才会打印，而这里我们只配置了info，所以想要查看sql语句的话，有以下两种操作：
130
        第一种把<root level="INFO">改成<root level="DEBUG">这样就会打印sql，不过这样日志那边会出现很多其他消息
131
        第二种就是单独给mapper下目录配置DEBUG模式，代码如下，这样配置sql语句会打印，其他还是正常DEBUG级别：
132
     -->

    <!--开发环境:打印控制台-->

    <springProfile name="dev">

        <!--可以输出项目中的debug日志，包括mybatis的sql日志-->

        <logger name="com.guli" level="INFO" />

        <!--
139
            root节点是必选节点，用来指定最基础的日志输出级别，只有一个level属性
140
            level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，默认是DEBUG
141
            可以包含零个或多个appender元素。
142
        -->
        <root level="INFO">
            <appender-ref ref="CONSOLE" />
            <appender-ref ref="INFO_FILE" />
            <appender-ref ref="WARN_FILE" />
            <appender-ref ref="ERROR_FILE" />
        </root>
    </springProfile>
    <!--生产环境:输出到文件-->

    <springProfile name="pro">

        <root level="INFO">
            <appender-ref ref="CONSOLE" />
            <appender-ref ref="DEBUG_FILE" />
            <appender-ref ref="INFO_FILE" />
            <appender-ref ref="ERROR_FILE" />
            <appender-ref ref="WARN_FILE" />
        </root>
    </springProfile>
</configuration>
```

***

## 4. 错误日志输入到日志文件中

1. 在异常处理类或其他类上添加`@Slf4j` [^1]注解
2. 在该类中的方法上加上 ` log.error(e.getMessage());` [^1] 语句



```java
//@ControllerAdvice是一个为控制器增强的注解,通常用于统一异常的处理
@ControllerAdvice
@ResponseBody
@Slf4j		//  <------  添加@Slf4j 注解
public class GlobalExceptionHandler {
    @ExceptionHandler(MyException.class)
    public CommonResult errorMyExceptionHandler(MyException myException){
        myException.printStackTrace();
        //将错误信息写入日志中, 添加 " log.error(e.getMessage()); " 语句
        log.error(ExceptionUtil.getMessage(myException));
        return CommonResult.error().code(myException.getCode()).message(myException.getMsg());
    }
}
```







[^1]: 属于`lombok` 依赖
