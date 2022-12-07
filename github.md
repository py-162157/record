## 切换到某个commit之前的版本
1. 复制该commit的sha码
2. 克隆该库
3. `git checkout =b "AnyName" sha`

## 显示Connection was reset, errno 10054
git config --global http.sslVerify "false"