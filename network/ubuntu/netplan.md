
# netplan

- `renderer: networkd`  
  Specifid `Network Manager` or `networkd` 

### Bridge

- configuration (16.04 or less)  
	`/etc/network/interface`

```
auto eth0
iface eth0 inet manual

auto br0
iface br0 inet static
	bridge_ports eth0
	bridge_stp off
	address 192.0.2.10
	network 255.255.255.0
	gateway 192.0.2.1
```

- configuration (18.04 or more)  
  `/etc/netplan/01-netcfg.yml`

```
network:
	version: 2
	renderer: networkd
	ethernets:
		eth0:
			dhcp4: false
			dhcp6: false
			accept-ra: false
	bridges:
		br0:
			interfaces:
			  - eth0
			address:
			dhcp4: false
			dhcp6: false
			accept-ra: false
				- 192.0.2.10/24
				- 2001:db8::192:0:2:10/64
			gateway4: 192.0.2.1
			gateway6: 2001:db8::192:0:2:1
			nameservers:
				addresses:
					- 8.8.8.8
					- 1.1.1.1
					- 2001:2860:4860::8888
					- 2606:4700:4700::1111
				search:
				  - example.com
			parameters:
				stp: false
```

