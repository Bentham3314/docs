# install
https://galaxy.ansible.com/Juniper/junos/

### DEPENDENCIES

- Python 2.7  
- Ansible 2.1 or later  
- Junos py-junos-eznc 2.1.7 or later  
- jxmlease 1.0.1 or later  

### CentOS7

```
# yum install -y ansible
# yum install python-pip
# pip install junos-eznc
# pip install junos-netconify
# pip install ncclient

# ansible-galaxy install Juniper.junos
```

### CentOS6
Python2.6にyumが依存しているので2.7を別パスインストール  
別パスでインストールしたPython pipでansibleをインストール  

zlib{,-devel},openssl{,-devel}が存在しない場合、  
pipのインストールがこけるので入れる


```
# yum list installed > /tmp/yum_list
# yum install -y zlib-devel openssl-devel

# cd /usr/local/src ; pwd
# wget https://www.python.org/ftp/python/2.7.14/Python-2.7.14.tgz
# tar xf Python-2.7.14.tgz
# cd Python-2.7.14 ; pwd
# ls -l
# ./configure --prefix=/usr/local/python27 --enable-shared
# make && make install

# ldconfig -a > /tmp/ldconfig-a
# vi /etc/ld.so.conf.d/python27
------------------------------------------
/usr/local/python27/lib/
------------------------------------------

# ldconfig
# ldconfig -a | diff /tmp/ldconfig-a -

# /usr/local/python27/bin/python -V
# curl -kL https://bootstrap.pypa.io/get-pip.py | /usr/local/python27/bin/python

# /usr/local/python27/bin/pip freeze
# /usr/local/python27/bin/pip list
# /usr/local/python27/bin/pip install ansible
# /usr/local/python27/bin/pip freeze
# /usr/local/python27/bin/pip list

# yum list installed | diff /tmp/yum_list -
```
