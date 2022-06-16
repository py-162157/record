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