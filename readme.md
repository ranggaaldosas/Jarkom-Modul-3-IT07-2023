# Praktikum Modul 3 Jaringan Komputer - DHCP & Reverse Proxy

Praktikum Modul 3 Jaringan Komputer - **IT07**

## Authors

| Nama                                                | NRP        |
| --------------------------------------------------- | ---------- |
| [Rangga Aldo](https://www.github.com/ranggaaldosas) | 5027211059 |
| [Maulana Ilyasa](https://www.github.com/xxx)        | 5027211065 |

## Daftar Isi

- Topologi
- Config
- Resources
- Soal 1-20

## Topologi

<p align="center">
    <img src="https://i.ibb.co/N2642BH/Screenshot-80.png">

## Config

### Aura (Router)

```bash
auto eth0
iface eth0 inet dhcp
up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.67.0.0/16

auto eth1
iface eth1 inet static
address 10.67.1.0
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 10.67.2.0
netmask 255.255.255.0

auto eth3
iface eth3 inet static
address 10.67.3.0
netmask 255.255.255.0

auto eth4
iface eth4 inet static
address 10.67.4.0
netmask 255.255.255.0
```

### Himmel (DHCP Server)

```bash
auto eth0
iface eth0 inet static
address 10.67.1.1
netmask 255.255.255.0
gateway 10.67.1.0
```

### Helter (DNS Server)

```bash
auto eth0
iface eth0 inet static
address 10.67.1.2
netmask 255.255.255.0
gateway 10.67.1.0
```

### Denken (Database Server)

```bash
auto eth0
iface eth0 inet static
address 10.67.2.1
netmask 255.255.255.0
gateway 10.67.2.0
```

### Eiken (Load Balancer)

```bash
auto eth0
iface eth0 inet static
address 10.67.2.2
netmask 255.255.255.0
gateway 10.67.2.0
```

### Lugner (PHP Worker)

```bash
auto eth0
iface eth0 inet static
address 10.67.3.1
netmask 255.255.255.0
gateway 10.67.3.0
```

### Linie (PHP Worker)

```bash
auto eth0
iface eth0 inet static
address 10.67.3.2
netmask 255.255.255.0
gateway 10.67.3.0
```

### Lawine (PHP Worker)

```bash
auto eth0
iface eth0 inet static
address 10.67.3.3
netmask 255.255.255.0
gateway 10.67.3.0
```

### Fern (Laravel Worker)

```bash
auto eth0
iface eth0 inet static
address 10.67.4.1
netmask 255.255.255.0
gateway 10.67.4.0
```

### Flamme (Laravel Worker)

```bash
auto eth0
iface eth0 inet static
address 10.67.4.2
netmask 255.255.255.0
gateway 10.67.4.0
```

### Frieren (Laravel Worker)

```bash
auto eth0
iface eth0 inet static
address 10.67.4.3
netmask 255.255.255.0
gateway 10.67.4.0
```

## Resources

- 6 (https://drive.google.com/file/d/1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1/view?usp=sharing)
- 14 (https://github.com/martuafernando/laravel-praktikum-jarkom)

## Template per-soal

### (Nomor).

> [Desikripsi Soal]

### Scripting

```bash
awk grep fafifu
```

```bash
cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.it07.com

echo 'zone "arjuna.it07.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.it07.com";
        allow-transfer { 10.67.2.2; }; // IP Werkudara
};

zone "abimanyu.it07.com" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; }; // IP Werkudara
        allow-transfer { 10.67.2.2; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.it07.com";
};' > /etc/bind/named.conf.local
```

### Result

<p align="center">
    <img src="https://i.ibb.co/Z6Hf5Rq/82u6f1.jpg">

### 13.

> Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern.

### Scripting

> Masukkan script ini pada .bashrc di Database server alias **Denken**

```bash
echo 'nameserver 10.67.1.2' > /etc/resolv.conf
apt-get update
apt-get install mariadb-server -y
service mysql start

echo '# This group is read both by the client and the server
# use it for options that affect everything
[client-server]

# Import all .cnf files from configuration directory
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/

# Options affecting the MySQL server (mysqld)
[mysqld]
skip-networking=0
skip-bind-address
' > /etc/mysql/my.cnf

service mysql restart
```

```bash
mysql -u root -p
```

> Setelah mysql berhasil dijalankan pada denken, masukan script SQL berikut

```bash
CREATE USER 'kelompokit07'@'%' IDENTIFIED BY 'passwordit07';
CREATE USER 'kelompokit07'@'localhost' IDENTIFIED BY 'passwordit07';
CREATE DATABASE dbkelompokit07;
GRANT ALL PRIVILEGES ON *.* TO 'kelompokit07'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'kelompokit07'@'localhost';
FLUSH PRIVILEGES;
```

Selanjutnya, lakukan pengecekan pada Laravel worker, apakah sudah bisa melihat data yang diperlukan

```bash
mariadb --host=10.67.2.1 --port=3306 --user=kelompokit07 --password=passwordit07 dbkelompokit07 -e "SHOW DATABASES;"
```

### Result 13

<p align="center">
    <img src="https://i.ibb.co/Pmd7czQ/13.jpg">

### Result 14

<p align="center">
    <img src="https://i.ibb.co/cTZSg9m/14b.jpg">
<p align="center">
    <img src="https://i.ibb.co/s3zz76H/14a.jpg">

### Result 15

<p align="center">
    <img src="https://i.ibb.co/X5KqNGx/15.jpg">

### Result 16

<p align="center">
    <img src="https://i.ibb.co/8XMV453/16.jpg">

### Result 17

<p align="center">
    <img src="https://i.ibb.co/r63BdWr/17.jpg">
