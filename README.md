根据https://j-g-web.com/2021/04/12/Deploying-OpenStack-Victoria-on-Raspberry-Pis/这篇教程，我依葫芦画瓢，按步骤配置了4节点的树莓派4B。
1.系统采用的是 Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-1044-raspi aarch64)
2.网络方面，每个树莓派各添加了一根绿联百兆的USB转RJ45的转接线。每个树莓派的eth0网口配置的网段为10.62.247.0/24，每个树莓派的USB网口配置的网段为172.25.250.0/24，分别接在了两个思科交换机上，其中的10.62.247.0/24网段可以连接互联网。
3.ubuntu 22.04.3 LTS默认安装了cloud-init工具，所以每次开机重启都会重置hostname和/etc/hosts，这个只需要修改/boot/firmware/user-data中hostname:和manage_etc_hosts:字段即可。
4.ubuntu 22.04.3 LTS设置网络使用netplan工具：
sudo vim /etc/netplan/50-cloud-init.yaml
  network:
    ethernets:
        eth0:
            dhcp4: true
            optional: true
        enx000XXXXXXXXX:
            dhcp4: false
            optional: false
            addresses:
              - 172.25.250.11/24
            nameservers:
              addresses: [172.25.250.1]
            routes:
              - to: 172.25.250.0/24
                via: 172.25.250.1
    version: 2
5.Openstack安装及环境配置教程网址为：https://docs.openstack.org/install-guide/environment.html，我使用的是ubuntu 22.04.3 LTS中默认源自带的版本，即Victoria release
6.树莓派4b-8GB四个，使用64GB TF卡启动,镜像烧录采用Raspberry Pi Imager v1.8.1
7.让我们开始吧，首先我配置了hostname，分别是controller、compute01、compute02、computehci,
8.然后配置网络：controller-172.25.250.11-10.62.247.11、compute01-172.25.250.12-10.62.247.12、compute02-172.25.250.13-10.62.247.13、computehci-172.25.250.14-10.62.247.14
10.62.247.0/24网段我在路由器上设置了静态地址分配，172.25.250.0/24网段我手动配置IP地址和网关。
9.先更新下ubuntu os：
sudo apt update -y && sudo apt upgrade -y
喝杯咖啡稍等片刻
10.
