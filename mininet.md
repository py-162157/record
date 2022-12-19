## 报错Could not find a default OpenFlow controller

1. `from mininet.node import OVSController`
2. 加上 `net = Mininet(controller = OVSController)`
3. `sudo apt install openvswitch-testcontroller`

## 创建胖树拓扑

1. `sudo mn --custom fat-tree-4.py --topo mytopo --switch ovsbr,stp=1`
2. 胖树拓扑中存在环路，需要等到STP运行完毕才能ping通， `py net.waitConnected()`

## 报错找不到包

主要是因为sudo用户的pythonpath环境跟普通用户不一样，而mininet需要以root用户运行，需要在/root/.bashrc里添加 `export PYTHONPATH=$PYTHONPATH:/home/pengyang/Distrinet/mininet`
