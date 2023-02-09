## 软件源安装vpp
1. `deb [trusted=yes] https://packagecloud.io/fdio/release/ubuntu focal main`写入`/etc/apt/sources.list.d/99fd.io.list`
2. `curl -L https://packagecloud.io/fdio/master/gpgkey | sudo apt-key add -`并`sudo apt update`
3. `apt install libvppinfra=21.06-release`
4. `4. apt install vpp=21.06-release`
5. `5. apt install vpp-plugin-core=21.06-release`
6. `6. apt install vpp-plugin-dpdk=21.06-release`

## vpp命令
1. 添加删除IP地址`set int ip address (del) GigabitEthernet0/6/0 10.3.0.1/24`

## vpp-2110与dpdk联合编译
1. `git clone -b stable/2110 https://github.com/FDio/vpp.git`
2. `sudo make install-deps`与`sudo make install-ext-deps`
3. 在`vpp/build-data/platforms/vpp.mk`里加上`vpp_uses_dpdk_mlx5_pmd=y`
4. 在`vpp/build/external/packages/dpdk.mk`里将`mlx5-pmd`修改为y
5. `sudo make build -j 40`
6. `sudo make build-release`
7. `make pkg-deb`
8. 进入到build-root目录里`dpkg -i *.deb`