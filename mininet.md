## 报错Could not find a default OpenFlow controller

1. `from mininet.node import OVSController`
2. 加上 `net = Mininet(controller = OVSController)`
3. `sudo apt install openvswitch-testcontroller`
