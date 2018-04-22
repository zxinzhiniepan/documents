#  wiki.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-04-22 20:01

## 打开某些文件卡顿
原因：开启了set cusorline 和set cusorcolumn 
解决方法：
注释.vimrc中的的相应的设置项
和直接在当前窗口输入命令
```
:set nocusorline
```

```
:set nocusorcolumn
```
