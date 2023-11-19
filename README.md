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

## Soal 0
> Setelah mengalahkan Demon King, perjalanan berlanjut. Kali ini, kalian diminta untuk melakukan register domain berupa riegel.canyon.yyy.com untuk worker Laravel dan granz.channel.yyy.com untuk worker PHP (0) mengarah pada worker yang memiliki IP [prefix IP].x.1.

Melakukan register domain dilakukan pada node yang menjadi DNS Server, yaitu pada node ***Heater***, berikut adalah konfigurasi DNS nya
### *Script*
```sh
apt-get install bind9 -y

echo 'zone "riegel.canyon.e21.com" {
type master;
file "/etc/bind/jarkom/riegel.canyon.e21.com";
};

zone "granz.channel.e21.com" {
type master;
file "/etc/bind/jarkom/granz.channel.e21.com";
};' > /etc/bind/named.conf.local

mkdir -p /etc/bind/jarkom
cp /etc/bind/db.local /etc/bind/jarkom/riegel.canyon.e21.com
cp /etc/bind/db.local /etc/bind/jarkom/granz.channel.e21.com

echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA        root.riegel.canyon.e21.com. (
					2023111401      ; Serial
						604800      ; Refresh
						86400       ; Retry
						2419200     ; Expire
						604800 )    ; Negative Cache TTL
;
@       IN      NS      riegel.canyon.e21.com.
@       IN      A       10.47.4.1     ; IP Fern
www     IN      CNAME   riegel.canyon.e21.com.' > /etc/bind/jarkom/riegel.canyon.e21.com

echo '
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     granz.channel.e21.com. root.granz.channel.e21.com. (
					2023111401      ; Serial
						604800      ; Refresh
						86400       ; Retry
						2419200     ; Expire
						604800 )    ; Negative Cache TTL
;
@       IN      NS      granz.channel.e21.com.
@       IN      A       10.47.3.1     ; IP Lugner
www     IN      CNAME   granz.channel.e21.com.' > /etc/bind/jarkom/granz.channel.e21.com

echo 'options {
	directory "/var/cache/bind";

	forwarders {
			192.168.122.1;
	};

	// dnssec-validation auto;
	allow-query{any;};
	auth-nxdomain no;    # conform to RFC1035
	listen-on-v6 { any; };
}; ' >/etc/bind/named.conf.options

service bind9 start
```
Konfigurasi DNS tersebut mengarahkan domain **riegel.canyon.e21.com** ke IP node ***Fern*** ```10.47.4.1 ``` (Laravel Worker) dan domain **granz.channel.e21.com** ke IP node ***Lugner*** ```10.47.3.1``` (PHP Worker). 

Untuk mencoba apakah domain tersebut benar dapat diakses, kita perlu menambahkan terlebih dahulu IP DNS Server ***(Heater)***, ke dalam file ```/etc/resolv.conf```, jalakan script berikut, pada node **selain node client** (pada node client akan ditambahkan melalui DHCP):
```sh
echo 'nameserver 10.47.1.2
nameserver 192.168.122.1
' > /etc/resolv.conf

apt update -y
apt-get install dnsutils htop jq apache2-utils lynx -y
```
### Hasil
Untuk mencobanya gunakan command berikut
```
ping granz.channel.e21.com -c 5
ping riegel.canyon.e21.com -c 5
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/4a81b499-5e22-4a5c-8459-792ddf87f649">


## Soal 1
> Lakukan konfigurasi sesuai dengan peta yang sudah diberikan. Kemudian, karena masih banyak spell yang harus dikumpulkan, bantulah para petualang untuk memenuhi kriteria berikut: 
> - Semua CLIENT harus menggunakan konfigurasi dari DHCP Server.

Pada peta yang diberikan menggunakan image docker ```danielcristh0/debian-buster:1.1```, untuk itu perlu membuat template docker baru, dengan image mengarah pada image docker yang telah diberikan. 
Kemudian menyusun setiap node, sesuai dengan peta, dan memberikan ***network configuration*** seperti yang telah ditampilkan pada bagian [*Network Configuration*](##*network-configuration*)

Selanjutnya untuk konfigurasi DHCP server yang diletakan pada node ***Himmel*** adalah sebagai berikut:
```sh
apt-get install isc-dhcp-server -y

echo "
INTERFACESv4=\"eth0\"
#INTERFACESv6=\"\"
" > /etc/default/isc-dhcp-server

yes | echo "
default-lease-time 600;
max-lease-time 7200;

subnet 10.47.1.0 netmask 255.255.255.0 {}
subnet 10.47.2.0 netmask 255.255.255.0 {}

subnet 10.47.3.0 netmask 255.255.255.0 {
	range 10.47.3.16 10.47.3.32;
	range 10.47.3.64 10.47.3.80;
	option routers 10.47.3.100;
	option broadcast-address 10.47.3.255;
	option domain-name-servers 10.47.1.2;
	default-lease-time 180;
	max-lease-time 5760;
}

subnet 10.47.4.0 netmask 255.255.255.0 {
	range 10.47.4.12 10.47.4.20;
	range 10.47.4.160 10.47.4.168;
	option routers 10.47.4.100;
	option broadcast-address 10.47.4.255;
	option domain-name-servers 10.47.1.2;
	default-lease-time 720;
	max-lease-time 5760;
}

" > /etc/dhcp/dhcpd.conf

rm /var/run/dhcpd.pid

service isc-dhcp-server restart
```



Berikutnya untuk konfigurasi pada DHCP relay, yaitu di node ***Aura***, yang menjadi penghubung antara DHCP server dan client, menggunakan script berikut:
```sh
apt update -y
apt-get install isc-dhcp-relay -y

echo '
SERVERS="10.47.1.1"
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""
' > /etc/default/isc-dhcp-relay

echo '
net.ipv4.ip_forward=1
' > /etc/sysctl.conf

service isc-dhcp-relay restart
```

Setelah itu kita perlu melakukan restart pada node client, dan ketika kita masuk ke console pada node client, akan terlihat bahwa node client sekarang sudah memiliki alamat IP.

## Soal 2
> - Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.16 - [prefix IP].3.32 dan [prefix IP].3.64 - [prefix IP].3.80 (2)
Pengaturan *range* IP untuk client yang melalui Switch3 ada pada bagian berikut, di script sebelumnya.
```
subnet 10.47.3.0 netmask 255.255.255.0 {
...
range 10.47.3.16 10.47.3.32;
range 10.47.3.64 10.47.3.80;
...
}
```
Jika dilihat maka IP dari client yang melalui switch3 juga berada pada •range• yang telah ditentukan.


## Soal 3
> - Client yang melalui Switch4 mendapatkan range IP dari [prefix IP].4.12 - [prefix IP].4.20 dan [prefix IP].4.160 - [prefix IP].4.168 (3)
Sama seperti sebelumnya, Pengaturan *range* IP untuk client yang melalui Switch4 ada pada bagian berikut, di script sebelumnya.
```
subnet 10.47.4.0 netmask 255.255.255.0 {
...
range 10.47.4.12 10.47.4.20;
range 10.47.4.160 10.47.4.168;
...
}
```
Jika dilihat maka IP dari client yang melalui switch4 juga berada pada •range• yang telah ditentukan.



## Soal 4
> - Client mendapatkan DNS dari Heiter dan dapat terhubung dengan internet melalui DNS tersebut (4)
Agar Client menerimaa DNS dari DNS Server yaitu ***Heiter***. Diberikan konfigurasi berikut:
```
subnet 10.47.3.0 netmask 255.255.255.0 {
...
option domain-name-servers 10.47.1.2;
...
}

subnet 10.47.4.0 netmask 255.255.255.0 {
...
option domain-name-servers 10.47.1.2;
...
}
```

kemudian agar Client dapat terhubung ke internet melalui node DNS Server ***Heiter*** digunakan DNS forwarder, yang sebelumnya juga telah dilakukan pada bagian berikut:
```sh
echo 'options {
	directory "/var/cache/bind";

	forwarders {
			192.168.122.1;
	};

	// dnssec-validation auto;
	allow-query{any;};
	auth-nxdomain no;    # conform to RFC1035
	listen-on-v6 { any; };
}; ' >/etc/bind/named.conf.options
```

## Soal 5
> - Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch3 selama 3 menit sedangkan pada client yang melalui Switch4 selama 12 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 96 menit (5)

Untuk pengaturan lama waktu peminjaman ada pada bagian berikut, waktu tersebut diubah dulu ke dalam bentuk detik, sehingga waktu peminjaman untuk client yang melalui Switch3 menjadi 180 detik, cleint yang melalui switch 4 menjadi 720 detik, dan waktu maksimalnya menjadi 5760 detik.
```
subnet 10.47.3.0 netmask 255.255.255.0 {
...
default-lease-time 180;
max-lease-time 5760;
...
}

subnet 10.47.4.0 netmask 255.255.255.0 {
...
default-lease-time 720;
max-lease-time 5760;
...
}
```

Setelah proses pada node ***Himmel*** selesai, selanjutnya lakukan restart pada Node Aura yang menjadi DHCP Relay, dan pada Client, agar Client bisa meneripa IP dari DHCP.
<img width="1512" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/eb3745e8-dd73-4229-927f-b2d82be81641">

Client juga telah terhubung ke internet, melalui DNS Forwarders.
<img width="1512" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/e16432ec-2e1a-4531-bb61-32a0212400d7">


## Soal 6
> Pada masing-masing worker PHP, lakukan konfigurasi virtual host untuk website [berikut](https://drive.google.com/file/d/1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1/view?usp=sharing) dengan menggunakan php 7.3. (6)

### *Script*
Jalankan script berikut pada PHP Worker, untuk melakukan konfigurasi virtual host pada website [tersebut](https://drive.google.com/file/d/1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1/view?usp=sharing). dengan menggunakan php 7.3.
```sh
apt install nginx php7.3 php7.3-fpm wget zip htop -y

wget -O '/var/www/jarkom.zip' 'https://drive.google.com/uc?id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1&export=download'
unzip -o /var/www/jarkom.zip -d /var/www/
rm /var/www/jarkom.zip

echo '
server {

	listen 8000;

	root /var/www/modul-3;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

	# pass PHP scripts to FastCGI server
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/jarkom_error.log;
	access_log /var/log/nginx/jarkom_access.log;
}
' > /etc/nginx/sites-available/modul-3

ln -s /etc/nginx/sites-available/modul-3 /etc/nginx/sites-enabled/modul-3

rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.3-fpm stop
service php7.3-fpm start
```
### Hasil
Kemudian untuk melakukan pengujian jalankan command berikut pada php worker:
```
lynx localhost:8000 
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/9877e921-1af9-4f20-9cae-809c01acc50d">


## Soal 7
> Kepala suku dari Bredt Region memberikan resource server sebagai berikut: Lawine, 4GB, 2vCPU, dan 80 GB SSD; Linie, 2GB, 2vCPU, dan 50 GB SSD; Lugner 1GB, 1vCPU, dan 25 GB SSD; aturlah agar Eisen dapat bekerja dengan maksimal, lalu lakukan testing dengan 1000 request dan 100 request/second. (7)

### *Script*
Untuk mengatur Load balancer ***Eisen*** gunakan script berikut, pada node ***Eisen***:
```sh
apt install nginx -y

echo ' # Default menggunakan Round Robin
upstream granz  {
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
	server 10.47.3.5:8000; #IP Lawine
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
Kemudian kita juga perlu menjalankan script berikut pada node ***Lugner***, yang menjadi lokasi dari domain granz.channel.e21.com yang sebelumnya telah diatur pada DNS Server. Hal ini untuk mengarahkan request ke Load Balancer. gunakan script berikut untuk melakukannya pada node ***Lugner***:
```sh
apt install nginx php7.3 php7.3-fpm wget zip htop -y

echo '
server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_set_header Host $host;
		proxy_pass http://10.47.2.2; #IP Eisen
}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

wget -O '/var/www/jarkom.zip' 'https://drive.google.com/uc?id=1ViSkRq7SmwZgdK64eRbr5Fm1EGCTPrU1&export=download'
unzip -o /var/www/jarkom.zip -d /var/www/
rm /var/www/jarkom.zip

echo '
server {

	listen 8000;

	root /var/www/modul-3;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

	# pass PHP scripts to FastCGI server
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/jarkom_error.log;
	access_log /var/log/nginx/jarkom_access.log;
}
' > /etc/nginx/sites-available/modul-3

ln -s /etc/nginx/sites-available/modul-3 /etc/nginx/sites-enabled/modul-3

rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.3-fpm stop
service php7.3-fpm start
```
### Hasil
Kemudian untuk melakukan pengujian dengan 1000 request dan 100 request/second, gunakan command berikut pada node client:
```
ab -n 1000 -c 100 http://www.granz.channel.e21.com/ 
```
<img width="853" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/1bdd8778-fae4-4d8c-b3a4-6bd3d4c6d608">
<img width="853" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/0a1aac5a-a1c6-4279-b6fd-0147d5eb59c1">
<img width="853" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/296cf3ac-5570-428f-8c1c-f9b1e74986e6">
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/16f2f703-cf68-4c5b-88fc-f620714918ac">


## Soal 8
> Karena diminta untuk menuliskan grimoire, buatlah analisis hasil testing dengan 200 request dan 10 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
> - Nama Algoritma Load Balancer
> - Report hasil testing pada Apache Benchmark
> - Grafik request per second untuk masing masing algoritma. 
> - Analisis (8)

Gunakan commmand berikut untuk menlakukan testing
```
ab -n 200 -c 10 http://www.granz.channel.e21.com/
```

Perbarui algoritma load balancer pada node ***Eisen***, menggunakan script berikut
- Round Robin
```
echo ' # Default menggunakan Round Robin
upstream granz  {
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
	server 10.47.3.5:8000; #IP Lawine
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
<img width="1512" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/8ed2fad4-b57d-4422-8c5b-4cad09de38d5">
<img width="879" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/0e587f73-78f1-43a5-901e-8837c2ff3349">


- Least Connection
```
echo '
upstream granz  {
 	least_conn;
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
	server 10.47.3.5:8000; #IP Lawine
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
<img width="1512" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/a7bf1652-de76-4cd9-8733-0619ca7853be">
<img width="858" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/079c1513-81e9-47de-b9cf-958131cbfc67">


- IP Hash:
```
echo '
upstream granz  {
 	ip_hash;
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
	server 10.47.3.5:8000; #IP Lawine
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
<img width="1512" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/0e1f428f-65d5-4a4f-beab-67f0b6769ce5">
<img width="858" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/859b3d2b-b82f-49f1-b078-821b0f9d42d2">


- Generic Hash
```
echo '
upstream granz  {
 	hash $request_uri consistent;
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
	server 10.47.3.5:8000; #IP Lawine
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
<img width="1512" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/91916c86-186c-447a-8355-9ab2ba5d3795">
<img width="858" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/8f144a39-2c36-4ccd-b62f-88960d758a9e">



## Soal 9
> Dengan menggunakan algoritma Round Robin, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 100 request dengan 10 request/second, kemudian tambahkan grafiknya pada grimoire. (9)

Gunakan commmand berikut untuk menlakukan testing
```
ab -n 100 -c 10 http://www.granz.channel.e21.com/
```

- 3 Worker
```
echo ' # Default menggunakan Round Robin
upstream granz  {
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
	server 10.47.3.5:8000; #IP Lawine
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
<img width="879" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/09e5738f-eb19-47d2-bd58-a9af8a7aaec0">


- 2 Worker
```
echo ' # Default menggunakan Round Robin
upstream granz  {
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
<img width="879" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/d90d7a15-7a06-430d-a620-647adf0c0453">

- 1 Worker
```
echo ' # Default menggunakan Round Robin
upstream granz  {
	server 10.47.3.1:8000; #IP Lugner
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location / {
		proxy_pass http://granz;
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```
<img width="879" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/92d1b4d6-7177-4037-b6e6-06e98291206e">


## Soal 10
> Selanjutnya coba tambahkan konfigurasi autentikasi di LB dengan dengan kombinasi username: “netics” dan password: “ajkyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/rahasisakita/ (10)

### ***Script***
Untuk menambahkan autentikasi pada Load Balancer, jalankan command berikut:
```sh
mkdir /etc/nginx/rahasiakita
echo 'ajke21' | htpasswd -ic /etc/nginx/rahasiakita/.htpasswd netics
```
Kemudian untuk memperbarui konfigurasi webserver, jalankan script berikut:
```sh
echo ' # Default menggunakan Round Robin
upstream granz  {
	server 10.47.3.1:8000; #IP Lugner
	server 10.47.3.4:8000; #IP Linie
	server 10.47.3.5:8000; #IP Lawine
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
'> /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
```

### Hasil
Untuk mencobanya, coba akses ```granz.channel.e21.com``` menggunakan ```lynx```:
```
lynx granz.channel.e21.com
```
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/18d73842-5500-4afc-9bb7-b5a58877fab4">
<img width="1402" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/4c80fdca-da1a-4470-b196-c3451aba12ff">
<img width="863" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/352f42a9-31df-496d-92f4-2f1bd6491871">




## Soal 11
> Lalu buat untuk setiap request yang mengandung /its akan di proxy passing menuju halaman https://www.its.ac.id. (11) hint: (proxy_pass)

### ***Script***
Gunakan script berikut pada node ***Lugner*** untuk memperbarui konfigurasi webserver agar mengatur request yang mengandung /its:
```sh
apt install nginx php7.3 php7.3-fpm wget zip htop -y

echo '
server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location ~ /its {
		proxy_pass https://www.its.ac.id;
		proxy_set_header Host www.its.ac.id;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $scheme;
	}

	location / {
		proxy_set_header Host $host;
		proxy_pass http://10.47.2.2; #IP Eisen
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.3-fpm stop
service php7.3-fpm start
```

### Hasil
Jalankan command berikut untuk mencobanya:
```
lynx granz.channel.e21.com/its
```
<img width="863" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/39d8b54c-8d9d-4d16-b5ea-aec51904546c">
<img width="863" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/49f02986-347e-4fa3-a345-4be68a5a0ef5">


## Soal 12
> Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].3.69, [Prefix IP].3.70, [Prefix IP].4.167, dan [Prefix IP].4.168. (12) hint: (fixed in dulu clinetnya)

Gunakan script berikut pada node ***Lugner*** untuk memperbarui konfigurasi webserver agar mengarahkan request ke Load Balancer oleh client dengan IP yang telah ditentukan.
```sh
echo '
geo $allowed {
	default     0;
	10.47.3.69  1;
	10.47.3.70  1;
	10.47.4.167 1;
	10.47.4.168 1;
}

server {
	listen 80;
	server_name granz.channel.e21.com www.granz.channel.e21.com;

	location ~ /its {
		proxy_pass https://www.its.ac.id;
		proxy_set_header Host www.its.ac.id;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $scheme;
	}

	location / {
		proxy_set_header Host $host;
		if ($allowed) {
			proxy_pass http://10.47.2.2; #IP Eisen
		}
		deny all; 
		return 403; 
	}
}' > /etc/nginx/sites-available/lb-proxy

ln -s /etc/nginx/sites-available/lb-proxy /etc/nginx/sites-enabled/lb-proxy

rm /etc/nginx/sites-enabled/default

service nginx restart
service php7.3-fpm stop
service php7.3-fpm start
```

### Hasil

Disini, kami mencoba mengakses melaui client ***Revolte***, yang pada saat ini dijalankan DHCP server memberikan IP, ```10.47.3.18```. Maka karena hanya range tertentu yang bisa mengakses, otomatis pada percobaan ini tidak akan berhasil untuk mengakses ```granz.channel.e21.com```
<img width="863" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/1c7975b4-8058-4c01-ac81-c39a6dad9a94">
<img width="863" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/5664330e-59a9-4ebc-80d9-3d721a95dcc3">
<img width="863" alt="image" src="https://github.com/javakanaya/Jarkom-Modul-3-E21-2023/assets/87474722/c5cae3b6-4c0d-4b22-901e-89c3da1a34e3">



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
