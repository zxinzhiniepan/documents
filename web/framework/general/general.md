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
#### [日志配置文件(log4j2.xml)](https://github.com/zxinzhiniepan/myapplication/blob/master/local/dev_util/log4j/etc/log4j2.xml "log4j2.xml")

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
经试验只有tomcat能用，jetty不能用

