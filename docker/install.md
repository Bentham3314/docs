# docker install 2018/03/13
[docs.docker.com](https://docs.docker.com/install/linux/docker-ce/centos/)

### Env:
CentOS7

```
backup status
# yum list installed > ./yum_list
# rpm -qa > ./rpm-qa
# systemctl list-unit-files > ./systemctl_list-unit
# firewall-cmd --list-all

install required packages
# yum install -y yum-utils device-mapper-persistent-data lvm2

stable repository
# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# cat /etc/yum.repos.d/docker-ce.repo

install docker ce
# yum install docker-ce
# yum list docker-ce --showduplicates | sort -r

start Docker
# systemctl start docker
# docker run hello-world
```
