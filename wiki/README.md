# SSR

### Ubuntu 17.04

#### BBR

```shell
sudo apt-get update

modprobe tcp_bbr
echo "tcp_bbr" >> /etc/modules-load.d/modules.conf
## 查看效果
lsmod | grep bbr

echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
## 查看效果
sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control
```

#### Docker 

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce
```

#### SSR

```shell
docker pull breakwa11/shadowsocksr
docker run -d -p ${PORT}:${PORT}/tcp -p ${PORT}:${PORT}/udp \
        --env SERVER_PORT=${PORT} \
        --env PASSWORD= \
        --env METHOD=aes-256-cfb \
        --env PROTOCOL=auth_sha1_v4 \
        --env OBFS=http_simple \
  breakwa11/shadowsocksr

# 查看
docker ps
```