#  网络时钟
 - AUTHOR: xinzhiniepan
 - DATE: 2018-04-22 08:12

## 安装gentoo时设置体timezone
```
root # ls /usr/share/zoneinfo
root # echo "Asia/Shanghai" > /etc/timezone
root # emerge --config sys-libs/timezone-data
```

## 时间同步
```
root # rc-update add ntp-client default
```
