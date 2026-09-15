# ANGGOTA KELOMPOK

| Nama | NRP |
| :--- | :--- |
| Barra Ahza Fakhrullah | 5027251023 |
| Nabila Nafisatus Zuhro | 5027251073 |

## NO 1
Router Lain
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 192.241.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.241.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.241.3.1
    netmask 255.255.255.0
```
alice
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.1.2
    netmask 255.255.255.0
    gateway 192.241.1.1
```

mika
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.1.3
    netmask 255.255.255.0
    gateway 192.241.1.1
```

chisa
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.2.2
    netmask 255.255.255.0
    gateway 192.241.2.1
```

knights
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.3.2
    netmask 255.255.255.0
    gateway 192.241.3.1
```

eiri
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.3.3
    netmask 255.255.255.0
    gateway 192.241.3.1
```
