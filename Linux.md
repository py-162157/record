## 查看当前目录内所有文件的大小
`du -h --max-depth=1`

## 设置巨页
`echo 1024 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages`或者在安装dpdk后运行`dpdk-hugepages.py -r 4G`
## 改变某卷的文件系统格式

`mkfs.ext4 /dev/sdb2`

## shell脚本问题
1. `if`的`[]`前后都要加空格，如`[ ! -d “my” ]`，否则报错

## 改变某卷大小
1. `growpart /dev/vda 1`
2. 重启
3. `resize2fs /dev/vda1`

## 添加删除IP地址
`ip addr add(del) 192.168.122.21/24 dev vpp1host`

## 查看进程
`ps -eo pid,args,psr`

## 查看某个软件在apt里的所有可用版本
`apt-cache madison`

## 查看系统日志
`dmesg |tail`

## 查看巨页配置
`grep Huge /proc/meminfo`

## 启动linux系统的iptable
`echo "1" >/proc/sys/net/bridge/bridge-nf-call-iptables`

## 查看某块网卡的速度
`cat /sys/class/net/enp10s0/speed`

## 关闭超线程
`echo off > /sys/devices/system/cpu/smt/control`