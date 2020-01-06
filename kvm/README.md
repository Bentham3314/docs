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

- misc  

```
# virsh info <domain>
# virsh dumpxml <domain>
# virsh net-list --all
# virsh net-info <net-domain>
```

### create vm

- create disk  

```
# qemu-img create -f raw /var/lib/libvirt/images/centos6.img 30G
```

- local kickstart file

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

- kickstart

```
# virt-install --connect qemu:///system --hvm \
--os-type linux --accelerate \
--name pxe_vm \
--vcpus 2 \
--memory 2048 \
--disk /var/lib/libvirt/images/vm.img,size=30,device=disk,bus=virtio,format=raw \
--network bridge=br255,model=virtio,mac=52:54:00:49:3f:f7 \
--nographics \
--os-variant centos7.0 \
--location=http://...... \
--extra-args "console=tty0 console=ttyS0,115200n8 ksdevice=eth0 ks=http://...../ks/centos7_kvm_vda.ks"
```

### virt-clone  
オリジナルのゲストはシャットダウンすること

```
# virt-clone --original <original dom> --name <new dom> --file /var/lib/libvirt/images/<img>
```


### vyosインストール


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
