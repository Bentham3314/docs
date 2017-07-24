# kvm install
https://wiki.centos.org/HowTos/KVM

# Environment:
- CentOS6

# Host Setup
yumしてpacket forward許可してrebootしたら使える。

```
# yum -y install @virt* libguestfs-tools policycoreutils-python bridge-utils
# sed -i 's/^\(net.ipv4.ip_forward =\).*/\1 1/' /etc/sysctl.conf
# sysctl -p   ### CentOS 6 or less

# sysctl --system ## CentOS 7

# reboot
```
