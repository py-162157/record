## 软件源安装vpp
1. `deb [trusted=yes] https://packagecloud.io/fdio/release/ubuntu focal main`写入`/etc/apt/sources.list.d/99fd.io.list`
2. `curl -L https://packagecloud.io/fdio/master/gpgkey | sudo apt-key add -`并`sudo apt update`
3. `apt install libvppinfra=21.06-release`
4. `4. apt install vpp=21.06-release`
5. `5. apt install vpp-plugin-core=21.06-release`
6. `6. apt install vpp-plugin-dpdk=21.06-release`

## vpp命令
1. 添加删除IP地址`set int ip address (del) GigabitEthernet0/6/0 10.3.0.1/24`