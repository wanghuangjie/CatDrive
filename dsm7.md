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
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh

/etc/ssh/sshd_config

RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

### 开启led灯 - 休眠时慢闪，唤醒常亮

mount -o bind /dev/null /var/log/scemd.log || true
mkdir -p /tmp/scripts
cat > /tmp/scripts/ledfan.sh <<EOF
#!/bin/sh
if [ ! -d /sys/class/gpio/gpio450 ] ; then
echo 450 > /sys/class/gpio/export
fi
echo out > /sys/class/gpio/gpio450/direction
i2cset -y -f 0 0x45 0x00 0x55
i2cset -y -f 0 0x45 0x01 0x01
i2cset -y -f 0 0x45 0x30 0x07
while true
do
sata="\$(hdparm -C /dev/sda |grep 'drive'|awk '{print \$4}')"
ledss="\$(cat /tmp/scripts/leds.flag)"
if [ \$sata = standby ];then
i2cset -y -f 0 0x45 0x33 0x73 #最大电流 (呼吸)
i2cset -y -f 0 0x45 0x3d 0x55
i2cset -y -f 0 0x45 0x3e 0x55
i2cset -y -f 0 0x45 0x36 200
echo 0 > /sys/class/gpio/gpio450/value      
fi
if [ \$sata = active/idle ];then
sata_temp="\$(smartctl -a /dev/hda -d ata | sed -n '/Temperature_Celsius/p' | awk '{print \$10}')"
i2cset -y -f 0 0x45 0x33 0x03 #最大电流 (呼吸)
i2cset -y -f 0 0x45 0x36 100       #B
echo 1 > /sys/class/gpio/gpio450/value
fi
sleep 60
done
EOF
bash /tmp/scripts/ledfan.sh
