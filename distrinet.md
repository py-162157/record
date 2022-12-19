## 安装ansible后，需要确保可以联通至各worker主机
1. `vim /etc/ansible/hosts`，写入
```
[master]
127.0.0.1 ansible_connection=local ansible_python_interpreter=/usr/bin/python3
[workers]
172.28.112.3 ansible_python_interpreter=/usr/bin/python3 ansible_ssh_user="root" ansible_ssh_pass="pengyang"
```
2. 以root账户ssh登录一次worker机，在此之前需要在worker机`sudo vim /etc/ssh/sshd_config`并添加
```
Port 22
PermitRootLogin yes
```
3. 在`/etc/ansible/ansible.cfg`里添加
```
[defaults]
host_key_checking = false
```