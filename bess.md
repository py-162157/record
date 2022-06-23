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

# bess-dpdk17.11安装
## 前期准备
1. `git clone https://github.com/NetSys/bess`
2. `git checkout -b dpdk-17.11 5994415559efd7580354b08ecfc043a8e0aff7a6`
3. `sudo apt install ansible`
4. `ansible-playbook -K -i localhost, -c local env/dev.yml`
5. `sudo python3 build.py`

## 源码安装g++/gcc11.1.0
该版本需要`__CPLUSPLUS__`定义为201703L及以上，需要新g++和gcc版本。查询得需要11.1.0版本及以上，而bess的env中的版本并未更新（坑爹）。
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
5. `sudo make && sudo make install `

## grpc组件安装
1. `git clone -b v1.3.2 https://github.com/grpc/grpc`
2. 在grpc/thirdparty里安装各分组件：
3. `proxychains git clone -b v1.4.1 https://github.com/google/benchmark`
4. `proxychains git clone https://github.com/google/boringssl`并`git checkout -b "cloest-version" 2a19a17ca754a178c74ce5736adaf30f3ca6465b`
5. `git clone https://github.com/zjwzcnjsy/boringssl-with-bazel`
6. `git clone -b 2.2.1 https://github.com/gflags/gflags`
7. `git clone -b release-1.8.0 https://github.com/google/googletest`
8. `proxychains git clone -b v1.1.0 https://github.com/golang/protobuf`
9. `git clone -b 0.11.0 https://github.com/apache/thrift`
10. 