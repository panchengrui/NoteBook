```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    　　　
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>　　　
            <pattern>
                %red(%d{yyyy-MM-dd HH:mm:ss}) %green([%thread]) %highlight(%-5level) %boldMagenta(%logger) - %cyan(%msg%n)
            </pattern>
        </encoder>
    </appender>

    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <file>testFile.log</file>
        <append>true</append>
        <encoder>
            <pattern>
                %4relative [%thread] %-5level %logger{35} - %msg%n
            </pattern>
        </encoder>
    </appender>

    <appender name="ROLLFILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>mylog-%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
            <maxFileSize>100MB</maxFileSize>
            <maxHistory>60</maxHistory>
            <totalSizeCap>20GB</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>
                %4relative [%thread] %-5level %logger{35} - %msg%n
            </pattern>
        </encoder>
    </appender>

    <logger name="java.sql.PreparedStatement" level="DEBUG"/>

    <root level="INFO">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="FILE"/>
        <appender-ref ref="ROLLFILE"/>
    </root>
</configuration>
```