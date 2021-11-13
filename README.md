# jarkom-modul-3-a01-2021

# Anggota kelompok A1 :
- 05111940000083 Fajar Satria
- 05111940000124 Gerry Sihaj
- 05111940000130 Adrian

### 1 Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server
Langkah - langkah :
- Setting topologi seperti berikut :  
![topo](https://user-images.githubusercontent.com/65168221/141472305-fc2c9772-89a4-43fa-a7f8-cd742dc6b000.png)


Pada Foosha (Router) :
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.169.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.169.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.169.3.1
	netmask 255.255.255.0
```
Kemudian, jalankan :
- ```iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.181.0.0/16```
- ```echo nameserver 192.168.122.1 > /etc/resolv.conf``` pada masing - masing node

Pada Loguetown (Client) :
```
auto eth0
iface eth0 inet static
	address 192.169.1.2
	netmask 255.255.255.0
	gateway 192.169.1.1
```

Pada Alabasta (CLient) :
```
auto eth0
iface eth0 inet static
	address 192.169.1.3
	netmask 255.255.255.0
	gateway 192.169.1.1
```

Skypie (Client) :
```
auto eth0
iface eth0 inet static
	address 192.169.3.2
	netmask 255.255.255.0
	gateway 192.169.3.1

```

Tottoland (CLient) :
```
auto eth0
iface eth0 inet static
	address 192.169.3.3
	netmask 255.255.255.0
	gateway 192.169.3.1

```

Pada EniesLobby (DNS Server) :
```
auto eth0
iface eth0 inet static
	address 192.169.2.2
	netmask 255.255.255.0
	gateway 192.169.2.1
```
Kemudian, jalankan :
- ```apt-get update```
- ```apt-get install bind9 -y```
- Ubah ```/etc/bind/kaizoku/franky.A01.com``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141472360-d58f3354-9449-4950-a3a2-85b957592274.png)


Pada Water 7 (Proxy server) :
```
auto eth0
iface eth0 inet static
	address 192.169.2.3
	netmask 255.255.255.0
	gateway 192.169.2.1
```
Kemudian, jalankan :
- ```apt-get install squid```
- ```mv /etc/squid/squid.conf /etc/squid/squid.conf.bak```
- Ubah ```/etc/squid/squid.conf``` seperti berikut :  
![2](https://user-images.githubusercontent.com/65168221/141472614-954e6918-2914-4e08-827f-c7727d24fad0.png)
- service squid restart

Pada Jipangu (DHCP Server) :
```
auto eth0
iface eth0 inet static
	address 192.169.2.4
	netmask 255.255.255.0
	gateway 192.169.2.1

```
Kemudian, jalankan :
- ```apt-get update```
- ```apt-get install isc-dhcp-server```
- Ubah ```/etc/default/isc-dhcp-server``` seperti berikut :  
![3](https://user-images.githubusercontent.com/65168221/141472496-325cf331-6ffe-4d2d-aabc-5fab7eedc1e7.png)


### 2 Foosha sebagai DHCP Relay
Langkah - langkah :
Pada Foosha, jalankan :
- ```apt-get install -y```
- Untuk IP servers isikan dengan ip Jipangu ```192.169.2.4```
- Isikan interface dengan ```eth1 eth2 eth3```
- Pengaturan pada ```/etc/default/isc-dhcp-relay``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141473358-8e463328-5d82-4617-9bbf-f60e8df3c4bb.png)


### 3 Ada beberapa kriteria yang ingin dibuat oleh Luffy dan Zoro, yaitu:Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
Langkah - langkah :
- Pada semua node client (Loguetown, Alabasta, Tottoland, Skypie) ubah network config menjadi :
```
auto eth0
iface eth0 inet dhcp
```
- Pada Jipangu, ubah ```/etc/dhcp/dhcpd.conf``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141474403-b656027b-d75c-4de4-9455-2385697ae043.png)
- ```service isc-dhcp-server restart```
- Pada Foosha, ```service isc-dhcp-relay restart```
- Coba pada client :  
![2](https://user-images.githubusercontent.com/65168221/141474613-0dafc74a-5b00-4a5f-8b3a-665ae8c1d14d.png)


### 4 Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50
Langkah - langkah :
- Pada Jipangu, ubah ```/etc/dhcp/dhcpd.conf``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141474955-dcc56472-f7e1-486d-b1f0-d941b44a707a.png)

- Pada foosha, ```service isc-dhcp-relay restart```
- Coba pada client :  
![2](https://user-images.githubusercontent.com/65168221/141475026-3b9c5b22-54e7-4a62-824f-001db6e4158b.png)


### 5 Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut 
Langkah - langkah :
- Pada Enieslobby, ubah ``` /etc/bind/named.conf.options``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141475357-b881cb68-11ee-4cc1-963d-e896defb3894.png)

- Coba pada client :  
![2](https://user-images.githubusercontent.com/65168221/141475433-07f068cd-e262-4767-867a-2487a2bfec9e.png)


### 6 Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.
Langkah - langkah :
- Pada langkah soal 3 dan 4, lama waktu DHCP meminjamkan IP juga telah di atur pada file ```/etc/dhcp/dhcpd.conf``` pada bagian ```default-lease-time``` dan ```max-lease-time``` (satuan dalam detik) 


### 7 Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69 
Langkah - langkah :
- Pada Jipangu, ubah ```/etc/dhcp/dhcpd.conf``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141476055-3d4205dd-eef3-47ad-8a37-50b2641ce22d.png)
- ```service isc-dhcp-server restart```
- Agar addressnya tidak berubah - ubah, tambahkan juga pada Skypie di ```/etc/network/interfaces```:  
![2](https://user-images.githubusercontent.com/65168221/141476309-92c470eb-189c-4c15-b730-097dca2642d8.png)
- Restart node skypie
- Tes pada Skypie :  
![3](https://user-images.githubusercontent.com/65168221/141476389-40ffeb94-c62e-4767-8080-c79552b26427.png)


### 8 Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi.Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000 
Langkah - langkah :
- Buat domain baru pada EniesLobby, edit ```/etc/bind/named.conf.local``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141476889-ef693f55-15b2-4efb-b707-df664690e2d4.png)
- Buat folder ```mkdir /etc/bind/jarkom ```
- Copy file, ```cp /etc/bind/db.local /etc/bind/jarkom/jualbelikapal.A01.com```
- Edit file ```/etc/bind/jarkom/jualbelikapal.A01.com``` seperti berikut :  
![2](https://user-images.githubusercontent.com/65168221/141477125-931b3f0a-f83e-4981-90b3-a404056033cb.png)

- ```service bind9 restart```
- Coba ping pada loguetown :  
 ![3](https://user-images.githubusercontent.com/65168221/141477231-9fc13d81-ab50-4888-a523-e91541eb4e2b.png)

- Pada water7, ubah ```/etc/squid/squid.conf``` seperti berikut :  
![4](https://user-images.githubusercontent.com/65168221/141477334-b9686a36-7e6a-4dfb-913c-ac6953438dd1.png)
- ```service squid restart```

Pada loguetown, jalankan : 
- ```apt-get update```
- ```apt-get install lynx -y```
- Jalankan proxy nya, ```export http_proxy="http://jualbelikapal.a01.com:5000"```
- Coba ping website its.ac.id :  
![5](https://user-images.githubusercontent.com/65168221/141477611-b3efccaa-15c4-4032-a36d-e6363b9f4d78.png)

### 9 Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy 
Langkah - langkah :
- Pada Water7, install ```apt-get install apache2-utils```
- Masukkan perintah berikut untuk menambahkan 2 user sesuai perintah soal :  
```htpasswd -c /etc/squid/passwd luffybelikapala01``` dengan password ```luffy_a01```  
```htpasswd  /etc/squid/passwd zorobelikapala01``` dengan password ```zoro_a01```
- Ubah ```/etc/squid/squid.conf``` seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141644790-dceb3e83-ff17-4f3c-b53c-1e1d00fc68d6.png)

- ```service squid restart```
- Coba pada loguetown misalnya dengan membuka ```http://its.ac.id```  
![2](https://user-images.githubusercontent.com/65168221/141644793-894d09f9-25df-4ab2-a914-b1cd4c06dbae.png)  
![3](https://user-images.githubusercontent.com/65168221/141644795-3686a33c-a6f3-49bb-ae45-42a22d26a76b.png)


### 10 Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00) 
Langkah - langkah :
- Pada water 7 buat file baru ```nano /etc/squid/acl.conf``` dan ubah seperti berikut :  
![1](https://user-images.githubusercontent.com/65168221/141644883-c8bb4ca5-32bd-4403-a8ac-f12269df638d.png)

- Ubah ```/etc/squid/squid.conf``` seperti berikut :
![2](https://user-images.githubusercontent.com/65168221/141644989-a7ca4a54-c7c3-44e3-818d-deb6dd753b7f.png)

- ```service squid restart```
- Coba jalankan pada loguetown, jika diluar waktu yang ditentukan:   
![3](https://user-images.githubusercontent.com/65168221/141644993-62f4342a-82b3-4642-b068-9ccc577b854a.png)  
![4](https://user-images.githubusercontent.com/65168221/141645005-68634710-2b43-4752-89df-8cfe93d10b71.png)

- Jika sesuai waktu yang ditentukan:  
![5](https://user-images.githubusercontent.com/65168221/141645012-5e4b58ff-dc1a-4516-a03e-95d7bd9b43ca.png)  
![6](https://user-images.githubusercontent.com/65168221/141645015-263af4d0-2170-489b-99d3-74c02ef3647d.png)


### 11 Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie 
Langkah - langkah :
**Pada EniesLobby**
- Edit file `/etc/bind/named.conf.local` seperti pada gambar berikut:
![Screenshot (57)](https://user-images.githubusercontent.com/72863287/141643036-6f84ed3b-2a0a-401c-8877-47b9ad212260.png)

- Buat file baru dengan filepath `/etc/bind/jarkom/super.franky.a01.com` dan isi seperti pada gambar berikut:
![Screenshot (56)](https://user-images.githubusercontent.com/72863287/141643043-00993aeb-3e8a-4d03-9dcf-d242811526c2.png)

- Restart bind9.
  ```
  service bind9 restart
  ```
**Pada Skypie**
- Install aplikasi apache, PHP, dan libapache2-mod-php7.0.
  ```
  apt-get install apache2 -y
  apt-get install php -y
  apt-get install libapache2-mod-php7.0 -y
  ```
- Buat file baru dengan filepath `/etc/apache2/sites-available/super.franky.a01.com.conf` dan isi seperti pada gambar berikut:
![Screenshot (58)](https://user-images.githubusercontent.com/72863287/141643106-8832ce86-efe8-474e-aa14-2169fa511ccb.png)

- Aktifkan konfigurasi super.franky.a01.com.

  ```
  a2ensite super.franky.a01.com
  ```
- Restart apache.
  ```
  service apache2 restart
  ```
- Download file zip menggunakan `wget` dan simpan pada `/var/www`.

  ```
  wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/super.franky.zip -P /var/www/
  ```

- Unzip file yang sudah didownload sebelumnya dan simpan pada `/var/www`.

  ```
  unzip /var/www/super.franky.zip -d /var/www
  ```

- Ganti nama folder `super.franky` menjadi `super.franky.a01.com` pada `/var/www/`.

  ```
  mv /var/www/super.franky /var/www/super.franky.a01.com
  ```
- Pastikan isi folder `/var/www/super.franky.a01.com` sudah seperti ini
![Screenshot (59)](https://user-images.githubusercontent.com/72863287/141643157-98579dbe-db3a-43d6-b46d-72d21c9534dc.png)

**Pada Water7**
- Edit file `/etc/squid/squid.conf` lalu tambahkan potongan config berikut:
```
acl BLACKLIST dstdomain google.com
deny_info http://super.franky.a01.com/ BLACKLIST
http_reply_access deny BLACKLIST
```
- Restart squid.
  ```
  service squid restart
  ```
**Pada Loguetown**
- Buka `google.com` menggunakan lynx sehingga akan menampilkan laman seperti berikut.
![Screenshot (60)](https://user-images.githubusercontent.com/72863287/141643215-d4f18a35-ee48-4419-9c7b-d35edf3f2500.png)

### 12 Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps 
Langkah - langkah :
**Pada Water7**
- Buat file bernama `acl-bandwidth.conf` di folder squid.
- Edit file `/etc/squid/acl-bandwidth.conf` seperti pada gambar berikut:

![Screenshot (65)](https://user-images.githubusercontent.com/72863287/141643622-6d8635e1-d91c-426b-995a-64328385d90b.png)

- Tambahkan potongan config `include /etc/squid/acl-bandwidth.conf` pada file `/etc/squid/squid.conf` seperti pada gambar berikut:

![Screenshot (63)](https://user-images.githubusercontent.com/72863287/141643295-89b29fae-10b2-4cc4-8e1c-10275a7f51a7.png)

- Restart squid.
  ```
  service squid restart
  ```
**Pada Loguetown**
- Buka `super.franky.a01.com` menggunakan lynx dengan akun Luffy dan coba download file `background-frank.jpg`. Dapat dilihat ada delay bandwidth.

![Screenshot (55)](https://user-images.githubusercontent.com/72863287/141643304-6de7bccf-6fdb-405c-a2f2-a36a1a740d80.png)

- Buka `super.franky.a01.com` menggunakan lynx dengan akun Luffy dan coba download file `bukanfrankytapirandom.99689`. Dapat dilihat ada delay bandwidth.

![2021-11-12 22-12-02-1(1)-1](https://user-images.githubusercontent.com/72863287/141644121-bf1aa5c4-a8c7-4e82-8827-b1062009fdb1.gif)

### 13 Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya 
Langkah - langkah :
**Pada Loguetown**
- Buka `super.franky.a01.com` menggunakan lynx dengan akun Zoro dan coba download file `background-frank.jpg`. Dapat dilihat tidak ada delay bandwidth.

![2021-11-13 19-48-28](https://user-images.githubusercontent.com/72863287/141644464-80540539-eea5-4984-a260-bb65b2dab460.gif)

- Buka `super.franky.a01.com` menggunakan lynx dengan akun Zoro dan coba download file `roboto-frankyasdjklkljqwennmn.listingbro`. Dapat dilihat tidak ada delay bandwidth.

![2021-11-13 19-48-56](https://user-images.githubusercontent.com/72863287/141644466-82935ea8-a95b-46c1-b7b3-f959e7e962db.gif)


Kendala : 
- Kesusahan dengan dhcp relay karena tidak ada di modul
- Kesusahan unutk no 12 dan 13 ketika membatasi bandwith 
- disaat menggunakn dhcp lupa menghilangkan perintah echo nameserver xxxxx > resolv.conf sehingga dhcp tidak bisa bekerja dengan baik
- kebingungan mengkonversikan satuan bandwith

Pembagian Tugas :
- 05111940000124 Gerry Sihaj (No 1 - 4)
- 05111940000130 Adrian (No 5 - 8)
- 05111940000083 Fajar Satria (No 9 - 13)


