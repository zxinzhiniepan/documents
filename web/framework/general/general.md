#  general.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-04-22 17:03

## 基本环境搭建(gradle构建工具)
### 日志管理
#### jar包管理
 ```jar包
 compile group: 'org.apache.logging.log4j', name: 'log4j-api', version: '2.11.0'  // 日志记录核心jar
 compile group: 'org.apache.logging.log4j', name: 'log4j-core', version: '2.11.0' // log4j2核心jar包
 compile group: 'org.apache.logging.log4j', name: 'log4j-jul', version: '2.11.0'  // 记录单元测试的jar包
 ```
#### 日志配置文件(log4j2.xml)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
<configuration status="WARN" monitorInterval="30">
    <!--先定义所有的appender-->
    <appenders>
    <!--这个输出控制台的配置-->
        <console name="Console" target="SYSTEM_OUT">
        <!--输出日志的格式-->
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
        </console>
    <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，这个也挺有用的，适合临时测试用-->
    <File name="log" fileName="./log/test.log" append="true">
       <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"/>
    </File>
    <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
    <!--    <RollingFile name="RollingFileInfo" fileName="${sys:user.home}/logs/info.log"
                     filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log">  -->
        <RollingFile name="RollingFileDebug" fileName="./logs/debug.log"
            filePattern="./logs/$${date:yyyy-MM}/debug-%d{yyyy-MM-dd}-%i.log">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->        
            <ThresholdFilter level="debug" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>

        <RollingFile name="RollingFileInfo" fileName="./logs/info.log"
            filePattern="./logs/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->        
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>
        <!--    <RollingFile name="RollingFileWarn" fileName="${sys:user.home}/logs/warn.log"
        filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log"> -->
        <RollingFile name="RollingFileWarn" fileName="./logs/warn.log" 
            filePattern="./logs/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log">
            <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件，这里设置了20 -->
            <DefaultRolloverStrategy max="20"/>
        </RollingFile>
        <!-- <RollingFile name="RollingFileError" fileName="${sys:user.home}/logs/error.log"
              filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log"> -->
        <RollingFile name="RollingFileError" fileName="./logs/error.log"
            filePattern="./logs/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log">
            <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>
    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <loggers>
        <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
        <logger name="org.springframework" level="INFO"></logger>
        <logger name="org.mybatis" level="INFO"></logger>

        <!-- hibernate -->
        <!-- Log everything in hibernate -->
        <Logger name="org.hibernate" level="info" additivity="false">
            <AppenderRef ref="Console" />
        </Logger>

        <!-- Log SQL statements -->
        <Logger name="org.hibernate.SQL" level="debug" additivity="false">
            <AppenderRef ref="Console" />
            <AppenderRef ref="File" />
        </Logger>

        <!-- Log JDBC bind parameters -->
        <Logger name="org.hibernate.type.descriptor.sql" level="trace" additivity="false">
            <AppenderRef ref="Console" />
            <AppenderRef ref="File" />
        </Logger>
        <!-- Struts2 -->
        <Logger name="com.opensymphony.xwork2" level="debug"/>
                <Logger name="org.apache.struts2" level="debug"/>
        <root level="all">
            <appender-ref ref="Console"/>
            <appender-ref ref="RollingFileDebug"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
        </root>
    </loggers>
</configuration>
```

### 单元测试
#### gradle中的配置
jar包管理(build.gradle)
```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'org.junit.platform:junit-platform-gradle-plugin:1.2.0-M1'
    }
}
	......
      testCompile("org.junit.jupiter:junit-jupiter-api:5.2.0-M1")
      testRuntime("org.junit.jupiter:junit-jupiter-engine:5.2.0-M1")
	......
}
```

[junit-platform-gradle-plugin插件配置(在build.gradle中的配置)](http://sjyuan.cc/junit5/5.0.2/user-guide-cn/#42-%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7%E6%94%AF%E6%8C%81 "jpgp")
```
junitPlatform {
	// platformVersion '1.2.0-M1'
	filters {
		engines {
			// include 'junit-jupiter', 'junit-vintage'
			// exclude 'custom-engine'
		}
		tags {
			// include 'fast'
			exclude 'slow'
		}
		// includeClassNamePattern '.*Test'
	}
	// enableStandardTestTask true
	// reportsDir file('build/test-results/junit-platform') // this is the default
	logManager 'org.apache.logging.log4j.jul.LogManager'
}
```

### web服务器配置
#### jar包配置

#### [插件配置和管理(gretty)](http://akhikhl.github.io/gretty-doc/Getting-started.html "gretty")

