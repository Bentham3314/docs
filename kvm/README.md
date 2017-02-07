# kvm

### commands

- 削除  

```
# virsh undefine <domain>
```

- 開始  

```
# virsh start <domain>
```

- console  

```
# virsh console <domain>
```

- shutdown  

```
# virsh shutdown <domain>  
# virsh destroy <domain>
```

### ゲスト作成

- ディスク  

```
# qemu-img create -f raw /var/lib/libvirt/images/centos6.img 30G
```

- 作成  
ファイル

```
# virt-install \
--connect qemu:///system \
--name=centos6 \
--ram=2048 \
--file=/var/lib/libvirt/images/centos6.img \
--file-size=30 \
--vcpus=2 \
--os-type=linux \
--nographics \
--accelerate \
--hvm \
--location='' \
--initrd-inject /root/work/kvm/centos6.ks \
--extra-args='console=tty0 console=ttyS0,115200n8 keymap=us ks=file:/centos6.ks'
```

URL

```
# virt-install \
--connect qemu:///system \
--name=c5 \
--ram=512 \
--file=/var/lib/libvirt/images/centos6.img \
--file-size=30 \
--vcpus=2 \
--os-type=linux \
--nographics \
--accelerate \
--hvm \
--location='' \
--extra-args='console=tty0 console=ttyS0,115200n8 keymap=us ks=http://hoge/centos6.ks'
```


- 複製  
オリジナルのゲストはシャットダウンすること

```
# virt-clone --original <original dom> --name <new dom> --file /var/lib/libvirt/images/<img>
```


### 余談

vyosインストール


```
# qemu-img create -f raw /var/lib/libvirt/images/vyos.img 2G
# virt-install \
--connect qemu:///system \
--name=router3 \
--disk path=/var/lib/libvirt/images/vyos.img \
--vcpus=1 \
--ram=512 \
--cdrom=/root/iso/vyos-1.1.3-amd64.iso \
--os-type=linux \
--accelerate \
--hvm \
--os-variant=debiansqueeze
```
