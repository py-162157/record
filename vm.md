# 创建KVM虚拟机
`virt-install --connect=qemu:///system \
 --name master \
 --ram 8192 \
 --vcpus=4 \
 --os-type=linux \
 --disk cloud-vm.img,size=30,device=disk,bus=virtio \
 --disk cloud-config.img,device=cdrom \
 --graphics none \
 --import \
 -- w bridge=virbr
`

## 虚拟机克隆
`virt-clone -o worker1 -n worker2 -f ./worker2.img`，其中o是原虚拟机，n是新虚拟机，克隆后没有网卡的话就编辑`/etc/udev/rule.d/70-persistent-net.rules`如下:  
`SUBSYSTEM=="net", ACTION=="add", DRIVERS=="?*", ATTR{address}=="52:54:00:77:61:e2 ", KERNEL=="eth*", NAME="ens2"`

## 通过netplan配置IP
```
network:
  ethernets:
    eno1:
      dhcp4: false
      addresses: [192.168.1.163/24]
      gateway4: 192.168.1.254
      nameservers:
        addresses: [114.114.114.114]
    eno4:
      dhcp4: false
      addresses: [192.168.2.236/24]
      gateway4: 192.168.2.254
      nameservers:
          addresses: [114.114.114.114,192.168.2.254]
  version: 2
```

## 改变某卷大小

1. `growpart /dev/vda 1`
2. 重启
3. `resize2fs /dev/vda1`

或者
1. `vgdisplay`
```
lvextend -L 120G /dev/mapper/ubuntu--vg-ubuntu--lv //增大至120G
lvextend -L +20G /dev/mapper/ubuntu--vg-ubuntu--lv //增加20G
lvreduce -L 50G /dev/mapper/ubuntu--vg-ubuntu--lv //减小至50G
lvreduce -L -8G /dev/mapper/ubuntu--vg-ubuntu--lv //减小8G
lvresize -L 30G /dev/mapper/ubuntu--vg-ubuntu--lv //调整为30G
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv //执行调整
```