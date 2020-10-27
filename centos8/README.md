# CentOS8

## repo
- yum(python2)からdnf(python3)になった.  
- [dnfメモ](https://github.com/Bentham3314/docs/tree/master/dnf)  


```
# dnf -y install epel-release
// develのpkgがいろいろある. neovimをbuildするのにninja-buildがいるけどここにある
# sed -i -e s/enabled=0/enabled=1/g /etc/yum.repos.d/CentOS-PowerTools.repo
```

- default  

```
[root@t3 ~]# dnf repolist all
Failed to set locale, defaulting to C.UTF-8
Last metadata expiration check: 0:10:53 ago on Tue Oct 27 13:22:45 2020.
repo id                                              repo name                                                                           status
AppStream                                            CentOS-8 - AppStream                                                                enabled: 4935
AppStream-source                                     CentOS-8 - AppStream Sources                                                        disabled
BaseOS                                               CentOS-8 - Base                                                                     enabled: 1673
BaseOS-source                                        CentOS-8 - BaseOS Sources                                                           disabled
Devel                                                CentOS-8 - Devel WARNING! FOR BUILDROOT USE ONLY!                                   disabled
HighAvailability                                     CentOS-8 - HA                                                                       disabled
PowerTools                                           CentOS-8 - PowerTools                                                               disabled
base-debuginfo                                       CentOS-8 - Debuginfo                                                                disabled
c8-media-AppStream                                   CentOS-AppStream-8 - Media                                                          disabled
c8-media-BaseOS                                      CentOS-BaseOS-8 - Media                                                             disabled
centosplus                                           CentOS-8 - Plus                                                                     disabled
centosplus-source                                    CentOS-8 - Plus Sources                                                             disabled
cr                                                   CentOS-8 - cr                                                                       disabled
extras                                               CentOS-8 - Extras                                                                   enabled:   27
extras-source                                        CentOS-8 - Extras Sources                                                           disabled
fasttrack                                            CentOS-8 - fasttrack                                                                disabled
[root@t3 ~]#
```

## Python
[redhatエンジニアブログ](https://rheb.hatenablog.com/entry/rhel8-python)  

- platform-python: system周りで使うpythonがこれ. ユーザの利用は非推奨.  
- python3: ユーザが使うPython. `dnf install python3` .  
- python2: 2020でEoL. さよなら.  

## iptables
[nftables](https://wiki.nftables.org/wiki-nftables/index.php/Main_Page)に変わった.  
使い方はExampleを見ればよき.  

- [nftablesメモ](https://github.com/Bentham3314/docs/tree/master/nftables)  


以下でiptablesからnftables変換できる:  

```
# iptables-restore-translate -f /tmp/iptables-save
```

