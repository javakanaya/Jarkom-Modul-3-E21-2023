# Jarkom-Modul-3-E21-2023

Laporan Resmi Praktikum Jaringan Komputer Modul 3

## Nama Anggota:

- [Yoel Mountanus Sitorus](https://github.com/zemetia) - 5025211078
- [Java Kanaya Prada](https://github.com/javakanaya) - 5025211112

## Topologi
<img width="952" alt="topologi new" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/562a1dc5-2a49-4ade-916a-e8a154148f9f">



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
address 10.47.4.4
netmask 255.255.255.0
gateway 10.47.4.100
```
- **Frieren (Laravel Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.4.5
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
address 10.47.3.4
netmask 255.255.255.0
gateway 10.47.3.100
```
- **Lawine (PHP Worker)**
```
auto eth0
iface eth0 inet static
address 10.47.3.5
netmask 255.255.255.0
gateway 10.47.3.100
```
- **Revolte, Richter, Sein, dan Stark (Client)**
```
auto eth0
iface eth0 inet dhcp
```


## Soal 13 
> Karena para petualang kehabisan uang, mereka kembali bekerja untuk mengatur riegel.canyon.yyy.com. 
> Semua data yang diperlukan, diatur pada Denken dan harus dapat diakses oleh Frieren, Flamme, dan Fern. (13)

### ***Script***
Jalankan script berikut pada node yang menjadi database server, yaitu node ***Denken***:
```sh
apt-get install mariadb-server -y

echo '
[client-server]

[mysqld]
skip-networking=0
skip-bind-address
' > /etc/mysql/my.cnf

service mysql restart

echo "
CREATE USER IF NOT EXISTS 'kelompoke21'@'%' IDENTIFIED BY 'passworde21';
CREATE USER IF NOT EXISTS 'kelompoke21'@'localhost' IDENTIFIED BY 'passworde21';
CREATE DATABASE IF NOT EXISTS dbkelompoke21;
GRANT ALL PRIVILEGES ON *.* TO 'kelompoke21'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'kelompoke21'@'localhost';
FLUSH PRIVILEGES;
" > ~/script.sql

mysql < ~/script.sql
```
### Hasil
Kemudian untuk melakukan pengujian, kita bisa menjalankan command berikut pada Laravel Worker ***(Frieren, Flamme, dan Fern)***, 
```
apt-get install mariadb-client -y
mariadb --host=10.47.2.1 --port=3306 --user=kelompoke21 --password=passworde21 dbkelompoke21 -e "SHOW DATABASES;"
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/813c798c-3193-4d50-bbbd-dc834c1b2d0e">

## Soal 14
> Frieren, Flamme, dan Fern memiliki Riegel Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer (14)

Pada node Fern, karena IP Fern tujuan dari domain riegel.canyon.e21.com, maka kita perlu mengarahkan request agar menuju ke Load balancer, sebelum mengakses web, sama seperti yang dilakukan pada node ***Lugner***, gunakan script berikut:
```sh
rm /etc/apt/sources.list.d/php.list
apt-get update
apt install nginx php8.0 php8.0-fpm wget zip htop -y
apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2

apt-get install mariadb-client -y

curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

apt-get update

apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl -y

wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer

echo ' 
 server {
 	listen 80;
 	server_name riegel.canyon.e21.com www.riegel.canyon.e21.com;

 	location / {
 	    proxy_pass http://10.47.2.2; #IP Eisen
        proxy_set_header HOST $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 	}
 }' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

wget -O '/var/www/jarkom.zip' 'https://github.com/martuafernando/laravel-praktikum-jarkom/archive/refs/heads/main.zip'
unzip -o /var/www/jarkom.zip -d /var/www/
rm /var/www/jarkom.zip

echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.47.2.1
DB_PORT=3306
DB_DATABASE=dbkelompoke21
DB_USERNAME=kelompoke21
DB_PASSWORD=passworde21

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

JWT_SECRET=o9HWEIfemIeWt5bn8UuiMTrLQyeNsjmCRNDW7X8gRqkb3CJEEIshDjtCs2abcVcA
' > /var/www/laravel-praktikum-jarkom-main/.env

cd /var/www/laravel-praktikum-jarkom-main

composer update
php artisan migrate:fresh
php artisan db:seed --class=AiringsTableSeeder
php artisan key:generate

cd ~

echo '
 server {

 	listen 8000;

 	root /var/www/laravel-praktikum-jarkom-main/public;

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

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/laravel-praktikum-jarkom-main

ln -s /etc/nginx/sites-available/laravel-praktikum-jarkom-main /etc/nginx/sites-enabled/laravel-praktikum-jarkom-main

chown -R www-data.www-data /var/www/laravel-praktikum-jarkom-main/storage

rm /etc/nginx/sites-enabled/default

service nginx restart
service php8.0-fpm stop
service php8.0-fpm start
```

Pada Laravel Worker lainnya, yaitu Node ***Frieren*** dan ***Flamme*** jalankan script berikut:
```sh
rm /etc/apt/sources.list.d/php.list
apt-get update
apt install nginx php8.0 php8.0-fpm wget zip htop -y
apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2

apt-get install mariadb-client -y

curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'

apt-get update

apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl -y

wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer

wget -O '/var/www/jarkom.zip' 'https://github.com/martuafernando/laravel-praktikum-jarkom/archive/refs/heads/main.zip'
unzip -o /var/www/jarkom.zip -d /var/www/
rm /var/www/jarkom.zip

echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.47.2.1
DB_PORT=3306
DB_DATABASE=dbkelompoke21
DB_USERNAME=kelompoke21
DB_PASSWORD=passworde21

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

JWT_SECRET=o9HWEIfemIeWt5bn8UuiMTrLQyeNsjmCRNDW7X8gRqkb3CJEEIshDjtCs2abcVcA
' > /var/www/laravel-praktikum-jarkom-main/.env

cd /var/www/laravel-praktikum-jarkom-main

composer update
php artisan key:generate

cd ~

echo '
 server {

 	listen 8000;

 	root /var/www/laravel-praktikum-jarkom-main/public;

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

 	error_log /var/log/nginx/jarkom_error.log;
 	access_log /var/log/nginx/jarkom_access.log;
 }
' > /etc/nginx/sites-available/laravel-praktikum-jarkom-main

ln -s /etc/nginx/sites-available/laravel-praktikum-jarkom-main /etc/nginx/sites-enabled/laravel-praktikum-jarkom-main

chown -R www-data.www-data /var/www/laravel-praktikum-jarkom-main/storage

rm /etc/nginx/sites-enabled/default

service nginx restart
service php8.0-fpm stop
service php8.0-fpm start
```
### Hasil
Untuk pengujian jalankan command berikut, pada salah satu worker laravel:
```
lynx localhost:8000
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/1fa92bfb-ef6b-4780-868c-5bad55b666c3">


## Soal 15
> Riegel Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada grimoire.
> - POST /auth/register (15)


Jalankan command berikut ke salah satu worker:

Response
```
curl -X POST http://10.47.4.1:8000/api/auth/register -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"username": "test123", "password": "test123"}' | jq
```
<img width="1448" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/659c84a8-6106-4ed8-ad3f-7554189b60c4">

Testing
```
echo {"username": "test123", "password": "test123"} > register.json && ab -n 100 -c 10 -p register.json -T application/json http://10.47.4.4:8000/api/auth/register
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/195e3cb3-5f94-46af-b565-76def8b3ad58">


## Soal 16
> - POST /auth/login (16)
Jalankan command berikut ke salah satu worker:

Response
```
curl -X POST http://10.47.4.1:8000/api/auth/login -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"username": "test123", "password": "test123"}' | jq
```

<img width="1448" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/31544bf2-1a0b-4a33-b0de-f4c829047238">

Testing
```
echo {"username": "test123", "password": "test123"} > login.json && ab -n 100 -c 10 -p login.json -T application/json http://10.47.4.4:8000/api/auth/login
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/91bc651d-c858-46fc-9890-54fb28e03f24">


## Soal 17
> - GET /me (17)
Jalankan command berikut ke salah satu worker:

Response
```
curl -X GET http://10.47.4.1:8000/api/me -H 'Accept: application/json' -H 'Content-Type: application/json' -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwOi8vMTAuNDcuNC4xOjgwMDAvYXBpL2F1dGgvbG9naW4iLCJpYXQiOjE3MDAzODEwMTQsImV4cCI6MTcwMDM4NDYxNCwibmJmIjoxNzAwMzgxMDE0LCJqdGkiOiIyYlp5bUJteFNycFpOZnVuIiwic3ViIjoiMSIsInBydiI6IjIzYmQ1Yzg5NDlmNjAwYWRiMzllNzAxYzQwMDg3MmRiN2E1OTc2ZjcifQ.GQ5Ej6A0uMbUC3sjAKghHnQwVAjIv_Mo1tLvWztgFQc' | jq
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/7a9ea547-03c9-4232-835a-41c50cccd68c">


Testing
```
curl -X POST -H "Content-Type: application/json" -d @login.json http://10.47.4.4:8000/api/auth/login > login_output.txt
token=$(cat login_output.txt | jq -r '.token')
ab -n 100 -c 10 -H "Authorization: Bearer $token" http://10.47.4.4:8000/api/me
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/c3a25c2d-d643-44ac-a82f-6a7c39ca1e93">


## Soal 18
> Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur Riegel Channel maka implementasikan Proxy Bind pada Eisen untuk mengaitkan IP dari Frieren, Flamme, dan Fern. (18)
### ***Script***
Perbarui pengaturan pada Node Load Balancer ***Eisen***, agar bisa mengatur Riegel Channel, gunakan script berikut:
```sh
echo ' # Default menggunakan Round Robin
upstream granz  {
  server 10.47.3.1:8000; #IP Lugner
  server 10.47.3.4:8000; #IP Linie
  server 10.47.3.5:8000; #IP Lawine
}

upstream riegel  {
  server 10.47.4.1:8000; #IP Fern
  server 10.47.4.4:8000; #IP Frieren
  server 10.47.4.5:8000; #IP Flamme
}

server {
  listen 80;
  server_name granz.channel.e21.com www.granz.channel.e21.com;
  
  auth_basic "Authorization";
  auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
  
  location / {
    proxy_pass http://granz;
  }
}

server {
  listen 80;
  server_name riegel.canyon.e21.com www.riegel.canyon.e21.com;
  
  location / {
    proxy_pass http://riegel;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart

```
### Hasil
Pada salah satu client, jalankan command berikut:
```
lynx riegel.canyon.e21.com
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/492f80f0-ca3f-48c4-9dea-a786cff0d51d">

## Soal 19
> Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Frieren, Flamme, dan Fern. Untuk testing kinerja naikkan 
> - pm.max_children
> - pm.start_servers
> - pm.min_spare_servers
> - pm.max_spare_servers

> sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada Grimoire.(19)

Testing menggunakan command berikut pada client:
```
echo {"username": "test123", "password": "test123"} > login.json && ab -n 100 -c 10 -p login.json -T application/json http://reigel.canyon.e21.com/api/auth/login
```

Jalankan script berikut ke semua worker, lalu untuk testing lakukan pada node client, menggunaakan command diatas:
- Percobaan Pertama
```sh
echo '
[www]

user = www-data
group = www-data

listen = /run/php/php8.0-fpm.sock

listen.owner = www-data
listen.group = www-data

pm = dynamic
pm.max_children = 10
pm.start_servers = 5
pm.min_spare_servers = 4
pm.max_spare_servers = 3
' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm stop
service php8.0-fpm start
```
Hasil Percoabaan Pertama
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/2fd01c3d-4412-4df3-80a4-1ce11449da6c">


- Percobaaan Kedua
```sh
echo '
[www]

user = www-data
group = www-data

listen = /run/php/php8.0-fpm.sock

listen.owner = www-data
listen.group = www-data


pm = dynamic
pm.max_children = 40
pm.start_servers = 15
pm.min_spare_servers = 12
pm.max_spare_servers = 9
' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm stop
service php8.0-fpm start
```
Hasil Percoabaan Kedua
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/ed36b824-bb1e-4174-95f2-42534fd01794">


- Percobaan Ketiga
```sh
echo '
[www]

user = www-data
group = www-data

listen = /run/php/php8.0-fpm.sock

listen.owner = www-data
listen.group = www-data


pm = dynamic
pm.max_children = 80
pm.start_servers = 20
pm.min_spare_servers = 16
pm.max_spare_servers = 12
' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm stop
service php8.0-fpm start
```
Hasil Percobaan Ketiga
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/e19266d6-bd32-42d6-ac8f-369b38dcbb79">


## Soal 20
> Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Eisen. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second. (20)
### ***Script***
Untuk mengimplementasikan Least-Conn, Perbarui Load Balancer dengan menambahkan ```least-conn;```. jalankan script berikut pada node ***Eisen***:
```sh
echo ' # Default menggunakan Round Robin
upstream granz  {
  server 10.47.3.1:8000; #IP Lugner
  server 10.47.3.4:8000; #IP Linie
  server 10.47.3.5:8000; #IP Lawine
}

upstream riegel  {
  least_conn;
  server 10.47.4.1:8000; #IP Fern
  server 10.47.4.4:8000; #IP Frieren
  server 10.47.4.5:8000; #IP Flamme
}

server {
  listen 80;
  server_name granz.channel.e21.com www.granz.channel.e21.com;
  
  auth_basic "Authorization";
  auth_basic_user_file /etc/nginx/rahasiakita/.htpasswd;
  
  location / {
    proxy_pass http://granz;
  }
}

server {
  listen 80;
  server_name riegel.canyon.e21.com www.riegel.canyon.e21.com;
  
  location / {
    proxy_pass http://riegel;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
### Hasil
Untuk melakukan testing, pada client jalankan command berikut:
```
ab -n 100 -c 10 http://riegel.canyon.e21.com/
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/507e706f-98dd-4961-8ce9-7c8a7113854e">
