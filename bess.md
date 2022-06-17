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
1. 制定一个高得离谱的包，在报错里会打印所有可安装版本，如`sudo pip3 install protobuf==100000`

# 提示
目前安装的python版本与原版本共存，使用时需指定python3.10，pip的命令也为pip3.10  
或者使用`sudo make install` 直接覆盖原有的python3二进制文件  
bess使用的parser module在python3.10版本被删除了，须使用3.7-3.8版本的python
