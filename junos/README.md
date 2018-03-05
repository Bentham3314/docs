# junos

### commands

- config

```
> show configuration | no-more
> show configuration | display set | no-more
```

- 受光レベル確認

```
> show interfaces diagnostics optics [int]
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
