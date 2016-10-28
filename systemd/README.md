# systemd

## runlevel
|runlevel|/etc/systemd/system/以下|
|:-:|:-:|
|0|poweroff|
|1|rescue|
|2|multi-user|
|3|multi-user|
|4|multi-user|
|5|graphical|
|6|reboot|

---

## commands
- start
- stop
- status/show
- list-units
- list-unit-files
- enable
- disable
- reenable
- list-dependencies

### Unitの起動(start)
```
    # systemctl start httpd
```

### Unitの停止(stop)
```
    # systemctl stop httpd
```

### 状態確認
```
    # systemctl status httpd
    # systemctl show httpd
```

- status
目視しやすいだけ

```
[root@centos7 ~]# systemctl status httpd
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled)
   Active: active (running) since Sat 2016-10-01 02:56:31 UTC; 25s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 16577 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           tq16577 /usr/sbin/httpd -DFOREGROUND
           tq16578 /usr/sbin/httpd -DFOREGROUND
           tq16579 /usr/sbin/httpd -DFOREGROUND
           tq16580 /usr/sbin/httpd -DFOREGROUND
           tq16581 /usr/sbin/httpd -DFOREGROUND
           mq16582 /usr/sbin/httpd -DFOREGROUND

Oct 01 02:56:31 centos7 httpd[16577]: AH00557: httpd: apr_sockaddr_info_get() failed for centos7
Oct 01 02:56:31 centos7 httpd[16577]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0...s message
Oct 01 02:56:31 centos7 systemd[1]: Started The Apache HTTP Server.
Hint: Some lines were ellipsized, use -l to show in full.
[root@centos7 ~]#
```

- show
スクリプトで何かする場合はこちらの方が使い勝手いいらしい

```
[root@centos7 ~]# systemctl show httpd --no-pager | head
Id=httpd.service
Names=httpd.service
Requires=basic.target
Wants=system.slice
WantedBy=multi-user.target
Conflicts=shutdown.target
Before=shutdown.target multi-user.target
After=network.target remote-fs.target nss-lookup.target systemd-journald.socket basic.target system.slice
Documentation=man:httpd(8) man:apachectl(8)
Description=The Apache HTTP Server
[root@centos7 ~]#
```


### 有効化されているUnitの一覧表示
```
[centos@centos7 ~]$
[centos@centos7 ~]$ systemctl list-units --no-pager | head
UNIT                                                              LOAD   ACTIVE SUB       DESCRIPTION
proc-sys-fs-binfmt_misc.automount                                 loaded active waiting   Arbitrary Executable File Formats File System Automount Point
sys-devices-pci0000:00-0000:00:03.0-virtio0-net-eth0.device       loaded active plugged   Virtio network device
sys-devices-pci0000:00-0000:00:04.0-virtio1-block-vda-vda1.device loaded active plugged   /sys/devices/pci0000:00/0000:00:04.0/virtio1/block/vda/vda1
sys-devices-pci0000:00-0000:00:04.0-virtio1-block-vda.device      loaded active plugged   /sys/devices/pci0000:00/0000:00:04.0/virtio1/block/vda
sys-devices-platform-serial8250-tty-ttyS2.device                  loaded active plugged   /sys/devices/platform/serial8250/tty/ttyS2
sys-devices-platform-serial8250-tty-ttyS3.device                  loaded active plugged   /sys/devices/platform/serial8250/tty/ttyS3
sys-devices-pnp0-00:04-tty-ttyS0.device                           loaded active plugged   /sys/devices/pnp0/00:04/tty/ttyS0
sys-devices-pnp0-00:05-tty-ttyS1.device                           loaded active plugged   /sys/devices/pnp0/00:05/tty/ttyS1
sys-module-configfs.device                                        loaded active plugged   /sys/module/configfs
[centos@centos7 ~]$
```


### インストールされているUnitファイルの一覧表示(list-unit-files)
```
[centos@centos7 ~]$ systemctl list-unit-files --no-pager | head
UNIT FILE                              STATE
proc-sys-fs-binfmt_misc.automount      static
dev-hugepages.mount                    static
dev-mqueue.mount                       static
proc-fs-nfsd.mount                     static
proc-sys-fs-binfmt_misc.mount          static
sys-fs-fuse-connections.mount          static
sys-kernel-config.mount                static
sys-kernel-debug.mount                 static
tmp.mount                              masked
[centos@centos7 ~]$
```

### Unitの有効化
```
    # systemctl enable httpd
```

`/usr/lib/systemd/system/httpd.service` が
`/etc/systemd/system/multi-user.target.wants/httpd.service` へ
symlinkが貼られる

```
    # systemctl list-unit-files --no-pager | grep httpd
```

disabled -> enable へ

### Unitの無効化
```
    # systemctl disable httpd
```

`/etc/systemd/system/multi-user.target.wants/httpd.service` が削除される

```
    # systemctl list-unit-files --no-pager | grep httpd
```

enabled -> disabled へ


### Unitの再有効化(reenable)
一度disabledした上でenable化(symlinkだからどこで使うのか分からん)

```
    # systemctl reenable httpd
```

`/etc/systemd/system/multi-user.target.wants/httpd.service`
一度削除され再度リンクが貼られる


### Unitの依存関係の確認(list-dependencies)

```
[root@centos7 ~]# systemctl list-dependencies httpd --no-pager | head
httpd.service
tqsystem.slice
mqbasic.target
  tqmicrocode.service
  tqrhel-autorelabel-mark.service
  tqrhel-autorelabel.service
  tqrhel-configure.service
  tqrhel-dmesg.service
  tqrhel-loadmodules.service
  tqpaths.target
[root@centos7 ~]#
```
