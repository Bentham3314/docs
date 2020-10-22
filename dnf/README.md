# dnf
[コマンドリファレンス](https://dnf.readthedocs.io/en/latest/command_ref.html)

## install
yumと一緒

## group

```
# dnf group list
# dnf group info Standard
# dnf group install --with-optional Standard
```

#### Note.
CentOS8では `Standard` をインストールするとそれなりに色々入る  
`mini` + `Development Tools` + `Standard` でだいたいそれっぽいのは入る  
今の所 `postfix` は入らない  

```
Group: Standard
 Description: The standard installation of CentOS Linux.
 Mandatory Packages:
   acl
   at
   attr
   bc
   centos-release
   centos-release-eula
   cpio
   crontabs
   cyrus-sasl-plain
   dbus
   ed
   file
   iptstate
   irqbalance
   kpatch
   logrotate
   lsof
   mcelog
   microcode_ctl
   net-tools
   pciutils
   psacct
   quota
   rsyslog-gnutls
   rsyslog-gssapi
   rsyslog-relp
   sudo
   symlinks
   systemd-udev
   tar
   tree
   util-linux-user
 Default Packages:
   bash-completion
   blktrace
   bpftool
   bzip2
   chrony
   cockpit
   cryptsetup
   dnf-plugin-spacewalk
   dos2unix
   dosfstools
   ethtool
   fprintd-pam
   gnupg2
   insights-client
   kmod-kvdo
   ledmon
   libstoragemgmt
   lvm2
   mailcap
   man-pages
   mdadm
   mlocate
   mtr
   nano
   nmap-ncat
   pinfo
   plymouth
   realmd
   rng-tools
   rsync
   smartmontools
   sos
   sssd
   strace
   tcpdump
   teamd
   time
   unzip
   usbutils
   vdo
   vim-enhanced
   virt-what
   wget
   which
   words
   xfsdump
   zip
 Optional Packages:
   cifs-utils
   cockpit-doc
   fwupd
   fwupdate
   ima-evm-utils
   nfs-utils
   nvmetcli
   traceroute
   zsh
```

## repo
リポジトリの設置先: `/etc/yum.repos.d/`  

```
# dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
# dnf config-manager --add-repo=http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo
```

このあたりはyumと同様.  
yum.repos.d以下に設置して `--enablerepo` で読める.  

