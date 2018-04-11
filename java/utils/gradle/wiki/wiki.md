# gradle的相关问题
 - AUTHOR: xinzhiniepan
 - DATE: 2018-04-11 10:29

忽略up-to-date检查，强制执行所有task，我们需要添加这个选项 –rerun-tasks
```
$ gradle --rerun-tasks assemble
:compileJava
:processResources
:classes
:jar
:assemble

BUILD SUCCESSFUL

Total time: 1.037 secs
```
