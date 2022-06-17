## python安装grpc组件
`pip install grpcio grpcio-tools protobuf`

## 卸载老版本python3（可以不用卸载）
`sudo apt remove python3`
`sudo rm /usr/local/*3.6`
`dpkg -l |grep python`，然后一个一个卸载python3

## 安装新版本python3
1. `wget https://www.python.org/ftp/python/3.10.5/Python-3.10.5.tar.xz`
2. `cd Python-3.10.5`
3. `sudo ./configure --enable-optimizations --with-lto --enable-shared`
4. `sudo make -j 30`
5. `sudo make altinstall`
6. `whereis libpython3.10.so.1.0`
7. `sudo cp /usr/local/lib/libpython3.10.so.1.0 /usr/lib/`  

## Python安装指定版本包
1. 制定一个高得离谱的包，在报错里会打印所有可安装版本，如`sudo pip3 install protobuf==100000`

## 提示
目前安装的python版本与原版本共存，使用时需指定python3.10，pip的命令也为pip3.10  
或者使用`sudo make install` 直接覆盖原有的python3二进制文件 

## 切换默认python版本
1. `sudo update-alternatives --list python`
2. `sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1`
3. `sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.6 2`
4. `sudo sudo update-alternatives --list python`
5. `sudo update-alternatives --config python`
