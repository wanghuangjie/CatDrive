# dsm7 猫盘修复

### 防止固态温度高关机

```
sed -i -- 's/61/68/g' /usr/syno/etc.defaults/scemd.xml
sed -i -- 's/58/68/g' /usr/syno/etc.defaults/scemd.xml
```

### 修复软件中心空白

```
sed  -i  's/supportsystempwarning="yes"/supportsystempwarning="no"/g' /etc.defaults/synoinfo.conf 
```

### 修复cpu占用高，添加开机计划
```
systemctl stop pkg-scsit-monitor.service
```

### 安装docker
```
https://spk7.imnks.com/
sudo sed -i "s/\"username\":[^:]*/\"username\": \"root\",/g" "/var/packages/Docker/conf/privilege"
```

### 添加docker路径
```
~/.bashrc
PATH=$PATH:/var/packages/Docker/target/bin
export PATH
```
### 给予当前用户docker权限

```sudo synogroup --add docker
sudo synogroup --member docker $USER
sudo chown root:Docker /var/run/docker.sock
```

### 休眠

```
mount -o bind /dev/null /var/log/scemd.log || true
```


### ssh免密码登录
```
chmod 755 ~
chmod 600 ~/.ssh/authorized keys
chmod 700 ~/.ssh

/etc/ssh/sshd_config
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized keys```
