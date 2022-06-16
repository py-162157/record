## 初始化后的操作
1. `mkdir -p $HOME/.kube`
2. `sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`
3. `sudo chown $(id -u):$(id -g) $HOME/.kube/config`
4. `kubectl taint nodes --all node-role.kubernetes.io/master-`
   
## 给node打标签
`kubectl label node master mocknetrole=master`

## pod sandbox已经存在另一个地址
删除宿主机中的cni0网卡
1. `sudo ifconfig cni0 down`
2. `sudo ip link delete cni0`

## etcd cluster降级失败
删除`/var/etcd/mocknet-data`

## k8s的nodeport不通
`sudo ufw disable`  
`sudo setenforce 0`

## docker出现readonly filesyatem
给--privileged

## 容器里启动dpdk-testpmd
`dpdk-testpmd -l0,1 --vdev net_memif0,role=client,id=0,socket=/run/vpp/memif.sock,socket-abstract=no,zero-copy=yes,rsize=9 --single-file-segments --file-prefix config0  -- --total-num-mbufs=2048 -i --nb-cores=1`  
`show config fwd`  
`set fwd txonly`  
`set txpkts 1500`  
`show port summary all`  
`show port stats all`