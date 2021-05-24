



###

```bash
auto enp2s0
# iface enp2s0 inet dhcp
iface enp2s0 inet static
	address 172.20.3.201
	netmask 255.255.252.0


auto enp2s0:0
iface enp2s0:0 inet static
        address 192.168.1.66
        netmask 255.255.255.0
```

