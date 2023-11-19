# Jarkom-Modul-3-E21-2023

Laporan Resmi Praktikum Jaringan Komputer Modul 3

## Nama Anggota:

- [Yoel Mountanus Sitorus](https://github.com/zemetia) - 5025211078
- [Java Kanaya Prada](https://github.com/javakanaya) - 5025211112

## Topologi
<img width="957" alt="topologi modul3" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/5b4ff9af-16c4-415c-8d01-b20230624cc0">


## *Network Configuration*
- **Aura (DHCP Relay)**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
address 10.47.1.10
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 10.47.2.10
netmask 255.255.255.0

auto eth3
iface eth3 inet static
address 10.47.3.100
netmask 255.255.255.0

auto eth4
iface eth4 inet static
address 10.47.4.100
netmask 255.255.255.0
```
- **Himmel (DHCP Server)**
```
auto eth0
iface eth0 inet static
address 10.47.1.1
netmask 255.255.255.0
gateway 10.47.1.10

```
- **Heiter (DNS Server)**
```
auto eth0
iface eth0 inet static
address 10.47.1.2
netmask 255.255.255.0
gateway 10.47.1.10
```
- **Denken (Database Server)**
```
auto eth0
iface eth0 inet static
address 10.47.2.1
netmask 255.255.255.0
gateway 10.47.2.10
```
- **Eisen (Load Balancer)**
```
auto eth0
iface eth0 inet static
address 10.47.2.2
netmask 255.255.255.0
gateway 10.47.2.10
```
- **Fern (Laravel Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.4.1
netmask 255.255.255.0
gateway 10.47.4.100
```

- **Flamme (Laravel Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.4.2
netmask 255.255.255.0
gateway 10.47.4.100
```
- **Frieren (Laravel Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.4.3
netmask 255.255.255.0
gateway 10.47.4.100
```
- **Lugner (PHP Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.3.1
netmask 255.255.255.0
gateway 10.47.3.100

```

- **Linie (PHP Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.3.2
netmask 255.255.255.0
gateway 10.47.3.100
```
- **Lawine (PHP Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.3.3
netmask 255.255.255.0
gateway 10.47.3.100
```
- **Revolte, Richter, Sein, dan Stark (Client)**
```
auto eth0
iface eth0 inet dhcp
```
