# junos

### commands

- RSI

```
check storage space
> show system storage

check file list
> file list detail /var/tmp/

> request support information | save /var/tmp/_hostname.rsi
> file list detail /var/tmp/_hostname.rsi
> file copy /var/tmp/_hostname.rsi ftp://_user@_dest/tmp
> file copy /var/tmp/_hostname.rsi scp://_user@_dest/tmp
```

- config

```
> show configuration | no-more
> show configuration | display set | no-more
```

- 受光レベル確認

```
> show interfaces diagnostics optics [int]

// vc-port optics power
> request virtual-chassis vc-port diagnostics optics
> show virtual-chassis vc-port diagnostics optics
```

- config upload(ftp)

```
# save ftp://[user]@[destIPaddr]/[hostname].cfg
```

### interface

#### tag vlan interface
vlan interfaceでOSPFが不安定な時とかに

```
# set interfaces ge-0/0/0 vlan-tagging
# set interfaces ge-0/0/0 unit [vlan-id] vlan-id [vlan-id]
# set interfaces ge-0/0/0 unit [vlan-id] family inet address [IP/Prefix]
# set interfaces ge-0/0/0 unit [vlan-id] vlan-id [vlan-id]
# set interfaces ge-0/0/0 unit [vlan-id] family inet address [IP/Prefix]

ex)
# set interfaces ge-0/0/0 vlan-tagging
# set interfaces ge-0/0/0 unit 100 vlan-id 100
# set interfaces ge-0/0/0 unit 100 family inet address 192.168.100.1/24
# set interfaces ge-0/0/0 unit 200 vlan-id 200
# set interfaces ge-0/0/0 unit 200 family inet address 192.168.200.1/24
```


### process

```
show cpu usage
> show system processes extensive
> show system processes extensive | match rpd

process restart lldp-service for EX Series
> restart lldpd-service

process restart l2cpd-service for QFX
> restart l2cpd-service
```
