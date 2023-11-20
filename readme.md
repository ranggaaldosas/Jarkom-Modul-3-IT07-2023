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

---

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
    <img src="https://i.ibb.co/Z6Hf5Rq/82u6f1.jpg" width=350 length=350>

---

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

---

### 14.

> Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer

Pada soal tersebut, Praktikan disuruh untuk melakukan socket connection ke setup Mariadb dengan (Denken) Database server

### Scripting

> Clone github dan install composer dan PHP 8.0 pada tiap tiap pada .bashrc di Database server alias **Denken**

```bash
echo "
nameserver 192.168.122.1
nameserver 10.67.1.3
" > /etc/resolv.conf

apt-get update
apt-get install mariadb-client -y

apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2
curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y

apt-get install nginx -y
apt-get install lynx -y
apt-get install apache2-utils -y

apt-get install wget -y
wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer

apt-get install git -y

cd /var/www && git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git

cd /var/www/laravel-praktikum-jarkom && composer update
cd /var/www/laravel-praktikum-jarkom && cp .env.example .env
echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:z70nhApUrqTL6RdG3HQku6UPwC/JtMOvBN3N0fOvVAc=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.67.2.1
DB_PORT=3306
DB_DATABASE=dbkelompokit07
DB_USERNAME=kelompokit07
DB_PASSWORD=passwordit07

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
' > /var/www/laravel-praktikum-jarkom/.env
cd /var/www/laravel-praktikum-jarkom && php artisan migrate:fresh
cd /var/www/laravel-praktikum-jarkom && php artisan db:seed --class=AiringsTableSeeder
cd /var/www/laravel-praktikum-jarkom && php artisan key:generate
cd /var/www/laravel-praktikum-jarkom && php artisan jwt:secret
cd /var/www/laravel-praktikum-jarkom && php artisan storage:link

echo '
server {
    listen <PORT LARAVEL WORKER>;

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name _;

    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/praktikum-jarkom_error.log;
    access_log /var/log/nginx/praktikum-jarkom_access.log;
}
' > /etc/nginx/sites-available/praktikum-jarkom

ln -s /etc/nginx/sites-available/praktikum-jarkom /etc/nginx/sites-enabled/

chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage

service nginx restart

service php8.0-fpm start
```

#### PORT LARAVEL WORKER

```
10.67.4.1:8001; Untuk Fern
10.67.4.2:8002; Untuk Flamme
10.67.4.3:8003; Untuk Frieren
```

### Testing

```bash
lynx localhost:<PORT LARAVEL WORKER>
```

>

### Result 14

<p align="center">
    <img src="https://i.ibb.co/cTZSg9m/14b.jpg" width=500 length=500>
<p align="center">
    <img src="https://i.ibb.co/s3zz76H/14a.jpg" width=500 length=500>

---

# Nomor 15, 16, 17

Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire.

    - POST /auth/register (15)
    - POST /auth/login (16)
    - GET /me (17)

Pada soal tersebut, Praktikan disuruh untuk melakukan socket connection ke setup Mariadb dengan (Denken) Database server

### Scripting

> Kita akan melakukan testing pada 3 endpoint (15, 16, 17) dengan bantuan **Apache Benchmark**

Pada nomer ini, praktikan diperintahkan untuk melakukan testing pada salah satu Laravel Worker yang nantinya akan dilakukan benchmark oleh Client

Di sini, kelompok kami akan melakukan testing pada

````bash
apt-get update
apt-get install lynx -y
apt-get install htop -y
apt-get install apache2-utils -y
apt-get install jq -y

### Nomor 15
echo '
{
  "username": "kelompokit07",
  "password": "passwordit07"
}' > register.json

### Nomor 16
echo '
{
  "username": "kelompokit07",
  "password": "passwordit07"
}' > login.json

### Nomor 17
curl -X POST -H "Content-Type: application/json" -d @login.json http://10.67.4.1:8001/api/auth/login > login_output.txt

### Nomor 17
token=$(cat login_output.txt | jq -r '.token')```
````

Dan berikut ini adalah hasil dari benchmark nya

### Result 15

<p align="center">
    <img src="https://i.ibb.co/X5KqNGx/15.jpg" width=400 length=400>

### Result 16

<p align="center">
    <img src="https://i.ibb.co/8XMV453/16.jpg" width=400 length=400>

### Result 17

<p align="center">
    <img src="https://i.ibb.co/r63BdWr/17.jpg" width=400 length=400>

### Result 18

<p align="center">
    <img src="https://i.ibb.co/SRnXQxx/18a.jpg" width=400 length=400>
<p align="center">
    <img src="https://i.ibb.co/GJn3Msx/18b.jpg" width=400 length=400>
<p align="center">
    <img src="https://i.ibb.co/0K0q90S/18c.jpg" width=400 length=400>
<p align="center">
    <img src="https://i.ibb.co/dgYFj1K/18d.jpg" width=400 length=400>

### Result 19

<p align="center">
    <img src="https://i.ibb.co/2kk5xtL/19a.jpg" width=400 length=400>
### Result 18

<p align="center">
    <img src="https://i.ibb.co/sCjzbyt/19b.jpg" width=400 length=400>
### Result 18

<p align="center">
    <img src="https://i.ibb.co/vv2mYfn/19c.jpg" width=400 length=400>

### Result 20

<p align="center">
    <img src="https://i.ibb.co/Y7RQmC7/20.jpg" width=400 length=400>
