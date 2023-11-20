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

### Testing

```bash
ab -n 100 -c 10 -p -s 120 register.json -T application/json http://10.67.4.1:8001/api/auth/register

ab -n 100 -c 10 -p login.json -s 120 -T application/json http://10.67.4.1:8001/api/auth/login

ab -n 100 -c 10 -H "Authorization: Bearer $token" http://10.67.4.1:8001/api/me
```

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

### 18

> Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern.

Sesuai dengan perintah nya, praktikan diminta untuk membagi Load Request dari client kepada ke-3 Laravel Worker. Kita harus melakukan

### Scripting

> Masukkan script ini pada .bashrc di Database server alias **Denken**

```bash
echo 'upstream worker {
    server 10.67.4.1:8001;
    server 10.67.4.2:8002;
    server 10.67.4.3:8003;
}

server {
    listen 80;
    server_name riegel.canyon.it07.com www.riegel.canyon.it07.com;

    location / {
        proxy_pass http://worker;
    }
}
' > /etc/nginx/sites-available/laravel-worker

service nginx restart
```

### Testing request dari client

```bash
ab -n 100 -c 10 -p login.json -s 120 -T application/json http://10.67.4.1:8001/api/auth/login
```

### Result 18

<p align="center">
    <img src="https://i.ibb.co/SRnXQxx/18a.jpg" width=400 length=400>

#### Efek dari Load Balancing

<p align="center">
    Request pada semua Laravel Worker menjadi terdistribusi seimbang
</p>

<p align="center">
    <img src="https://i.ibb.co/GJn3Msx/18b.jpg" width=400 length=400>
<p align="center">
    <img src="https://i.ibb.co/0K0q90S/18c.jpg" width=400 length=400>
<p align="center">
    <img src="https://i.ibb.co/dgYFj1K/18d.jpg" width=400 length=400>

---

### 19

> Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan

- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers

  sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire

Alhasil, kita akan melakukan tuning pada Load Balancer denga script sebagai berikut, Analisa awal kami adalah jika tuning ditingkatkan maka hasil response time juga akan makin cepat

#### Tuning 1

```bash
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 25
pm.start_servers = 5
pm.min_spare_servers = 3
pm.max_spare_servers = 10' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

#### Hasil Tuning 1 = 45.65

<p align="center">
    <img src="https://i.ibb.co/2kk5xtL/19a.jpg" width=400 length=400>

#### Tuning 2

```cpp
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 50
pm.start_servers = 8
pm.min_spare_servers = 5
pm.max_spare_servers = 15' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

#### Hasil Tuning 2 = 45.01

<p align="center">
    <img src="https://i.ibb.co/sCjzbyt/19b.jpg" width=400 length=400>

#### Tuning 3

```bash
echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 75
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

#### Hasil tuning 3 = 38.93

<p align="center">
    <img src="https://i.ibb.co/vv2mYfn/19c.jpg" width=400 length=400>

Alhasil, kami bisa menyimpulkan bahwa semakin tinggi parameter ditingkatkan pada load balancer, response time pun akan meningkat

### 20

> Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second.

Praktikan diminta untuk menambahkan algoritma Least-Conn, dan bandingkan kecepatan nya

```bash
least_conn; # Algoritma Least-Connection
```

```bash
echo 'upstream worker {
    least_conn; # Algoritma Least-Connection
    server 10.67.4.1:8001;
    server 10.67.4.2:8002;
    server 10.67.4.3:8003;
}

server {
    listen 80;
    server_name riegel.canyon.it07.com www.riegel.canyon.it07.com;

    location / {
        proxy_pass http://worker;
    }
}
' > /etc/nginx/sites-available/laravel-worker

service nginx restart
```

### Result 20

<p align="center">
    <img src="https://i.ibb.co/Y7RQmC7/20.jpg" width=400 length=400>

Terlihat hasil **Percentage of the request ...** menurun jika dibandingkan hasil benchmark sebelumnya (1059 -> 288). Hal tersebut menandakan bahwa algoritma tersebut menambah kecepatan (ms) dengan cukup signifikan

---

# IT07 Pamit

## Authors

| Nama                                                | NRP        |
| --------------------------------------------------- | ---------- |
| [Rangga Aldo](https://www.github.com/ranggaaldosas) | 5027211059 |
| [Maulana Ilyasa](https://www.github.com/xxx)        | 5027211065 |

<p align="center">
    <img src="https://i.ibb.co/Z6Hf5Rq/82u6f1.jpg">
