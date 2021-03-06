# 裸机安装bess

1. `git clone https://github.com/NetSys/bess.git`，如果克隆不下来，开Proxychains代理
2. `git checkout dpdk-2011`
3. `cd bess/deps`
4. `proxychains4 wget https://fast.dpdk.org/rel/dpdk-20.11.3.tar.gz`
5. `tar xvf dpdk-20.11.3.tar.gz ./dpdk-20.11.3`
6. `cd .. & build.py`

# 安装新版本meson

1. `pip3 install --user meson`，该命令将meson安装在了~/.local/bin，需要将其复制到/usr/bin
2. `cp ~/.local/bin/meson /usr/bin`
3. `sudo apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev libbz2-dev liblzma-dev sqlite3 libsqlite3-dev tk-dev uuid-dev libgdbm-compat-dev`

# python安装grpc组件

`pip install grpcio grpcio-tools protobuf`

# 卸载老版本python3（可以不用卸载）

`sudo apt remove python3`
`sudo rm /usr/local/*3.6`
`dpkg -l |grep python`，然后一个一个卸载python3

# 安装新版本python3

1. `wget https://www.python.org/ftp/python/3.10.5/Python-3.10.5.tar.xz`
2. `cd Python-3.10.5`
3. `sudo ./configure --enable-optimizations --with-lto --enable-shared`
4. `sudo make -j 30`
5. `sudo make altinstall`
6. `whereis libpython3.10.so.1.0`
7. `sudo cp /usr/local/lib/libpython3.10.so.1.0 /usr/lib/`

# Python安装指定版本包

1. 制定一个高得离谱的包，在报错里会打印所有可安装版本，如 `sudo pip3 install protobuf==100000`

# 提示

1. 目前安装的python版本与原版本共存，使用时需指定python3.10，pip的命令也为pip3.10
2. 或者使用 `sudo make install` 直接覆盖原有的python3二进制文件
3. bess使用的parser module在python3.10版本被删除了，须使用3.7-3.8版本的python
4. 如果出现 `which grpc_python_plugin`找不到的问题，`sudo apt install protobuf-compiler-grpc`

## dpdk编译

dpdk16.07使用make编译，而dpdk20.11使用meson和ninja来编译，注意变换编译工具

### bess(c-legacy)分支替换dpdk20.11.3

1. `wget https://fast.dpdk.org/rel/dpdk-20.11.3.tar.gz`至bess/deps并解压为dpdk-20.11.3
2. 在build.py里将dpdk版本改为20.11.3
3. 在core/Makefile里将dpdk版本改为20.11.3
4. `core/snbuf.h`里第250行改为 `return (phys_addr_t)mbuf->buf_addr + mbuf->data_off;`
5. 在 `core/common.h`里注释掉22-24行的 `container_of`宏定义，因其与标准库中重复定义；
6. 在bess目录里将所有likely和unlikely宏改名，因其与标准库冲突且不可替代，不能直接注释掉；
7. 将core/snbuf.c的35行改为 `immutable->paddr = rte_mempool_virt2iova(snb);`
8. `sudo meson --buildtype=debugoptimized /home/pengyang/bess/deps/dpdk-20.11.3/build /home/pengyang/bess/deps/dpdk-20.11.3`
9. `sudo ninja -C /home/pengyang/bess/deps/dpdk-20.11.3/build install`

## benchmark安装
1. `git clone https://github.com/google/benchmark.git`
2. `cmake -E make_directory "build"`
3. `cmake -E chdir "build" cmake -DBENCHMARK_DOWNLOAD_DEPENDENCIES=on -DCMAKE_BUILD_TYPE=Release ../`
4. `cmake --build "build" --config Release`
5. 应用全局安装`sudo cmake --build "build" --config Release --target install`

## 安装g++/gcc11.1.0
### ubuntu20.04
1. `sudo apt-get install software-properties-common`
2. `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
3. `sudo apt update && sudo apt install gcc-11 g++-11`

### ubuntu16.04
需要源码安装
1. `wget http://ftp.gnu.org/gnu/gcc/gcc-11.1.0/gcc-11.1.0.tar.gz`
2. 安装编译依赖`wget ftp://gcc.gnu.org/pub/gcc/infrastructure/gmp-6.1.0.tar.bz2`, `wget ftp://gcc.gnu.org/pub/gcc/infrastructure/mpfr-3.1.4.tar.bz2`, `wget ftp://gcc.gnu.org/pub/gcc/infrastructure/mpc-1.0.3.tar.gz`
3. 解压`tar jxvf `, `./configure`, `sudo make && sudo make install`
4. 编译gcc`./configure --disable-multilib --enable-languages=c,c++`
5. `sudo make -j 40 && sudo make install `

# bess-dpdk17.11安装
## 前期准备
1. `git clone https://github.com/NetSys/bess`
2. `git checkout -b dpdk-17.11 2516e21348e33be5b65eb9ce451d3bdfce4d4a4b`
3. `sudo apt install ansible`
4. `ansible-playbook -K -i localhost, -c local packages.yml`，其中删掉`- apt_repository: repo='deb http://apt.llvm.org/{{ ansible_distribution_release }}/ llvm-toolchain-{{ ansible_distribution_release }}-4.0 main`
5. 建议分多个步骤逐步进行
6. packages.yml中的grpcio和scapy注释掉，手动安装grpcio-tools==1.12.1和scapy==2.3.3，要求python3版本为为3.7及以上。ubutnu16.04自带版本为3.5.2，需要从源码编译安装python3。
7. 在deps下下载dpdk-17.11，`wget fast.dpdk.org/rel/dpdk-17.11.tar.gz`并解压
8. `sudo python3 build.py`



## grpc组件安装
1. `git clone -b v1.3.2 https://github.com/grpc/grpc`
2. `git submodule update --init`

## OFED版本选择4.2-1.0.0.0
1. 所需的依赖libnl1目前已经无法从软件源安装，`wget http://archive.ubuntu.com/ubuntu/pool/universe/libn/libnl/libnl1_1.1-8ubuntu1_amd64.deb`
2. OFED出现`ModuleNotFoundError: No module named 'lsb_release'`，是更新python版本导致的，打开`/usr/bin/lsb_release`，将`#!/usr/bin/python3 -Es`改为`#!/usr/bin/python3.5m -Es`，其中3.5m为系统自带的python3版本。
3. 升级python后提示没有apt_pkg包，在`/usr/lib/python3/dist-packages`目录下执行`sudo cp apt_pkg.cpython-35m-x86_64-linux-gnu.so apt_pkg.cpython-37m-x86_64-linux-gnu.so`
4. linux-kernel版本需要切换到4.4.0版本，目前官网下载的16.04为4.15.0版本。OFED指定的ubuntu16.04具体内核版本为4.4.0-22，但是该内核版本太老，已经无法在现有硬件上运行，所以切换到4.4.0-142版本。
5. 安装时选择`./mlnxofedinstall --upstream-libs --dpdk`，后面的选项一定要加上，不然编译DPDK会出问题。
6. 以修改过后的bess-build.py替换bess原有的build.py
7. 运行dpdk-devbind的时候需要切换回4.15内核才行。

# ubuntu20.04编译bess-dpdk2011-focal分支
1. 按照/env/dev.yml里安装所需包
2. 在`core/kmog/sn_ethtool.c`的头文件里加上`#include <linux/ethtool.h>`
3. 运行编译脚本
