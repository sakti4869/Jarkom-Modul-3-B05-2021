# Jarkom-Modul-3-B05-2021

## 1. Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server (1)
- Langkah 1: buat topologi nya
![image](https://user-images.githubusercontent.com/71221969/141052412-7a553923-e37f-4276-a44c-0fb293a580de.png)
- Langkah 2: install library yang diperlukan <br>

  jalankan pada EniesLobby supaya menjadi DNS Server:
  ```
  apt-get install bind9 -y
  ```
  jalankan pada Jipangu supaya menjadi DHCP Server:
  ```
  apt-get install isc-dhcp-server -y
  ```
  jalankan pada Water7 supaya menjadi Proxy Server:
  ```
  apt-get install squid -y
  ```
- Langkah 3: set interfaces pada file `/etc/default/isc-dhcp-server` menjadi `eth0` supaya dia dapat terhubung ke switch yang menghubungkannya dengan DHCP relay
![image](https://user-images.githubusercontent.com/71221969/141052960-5e865626-a521-4bda-9fba-5d28b924d400.png)

## 2. dan Foosha sebagai DHCP Relay (2)
- Langkah 1: install library untuk DHCP relay pada `Foosha` dengan menjalankan
```
apt-get install isc-dhcp-relay -y
```
- Langkah 2: set SERVERS nya dengan IP dari DHCP server yaitu `192.179.2.4` supaya terhubung dengan DHCP server dan interfaces nya menjadi `eth1 eth2 eth3` supaya terhubung dengan switch1, switch2 dan switch3
![image](https://user-images.githubusercontent.com/71221969/141056838-487762cf-b4cb-49b5-befd-47c793d9ccde.png)

# Ada beberapa kriteria yang ingin dibuat oleh Luffy dan Zoro, yaitu:
(nomor 3 - 6)
### 1. Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
- Langkah 1: set file `interfaces` pada directory `/etc/network` dengan kode berikut supaya IP nya menggunakan konfigurasi dari DHCP server:
```
auto eth0
iface eth0 inet dhcp
```
- Pada `Loguetown`
![image](https://user-images.githubusercontent.com/71221969/141057409-7cff0855-d38f-4f64-9825-415a80df928d.png)

- Pada `Alabasta`
![image](https://user-images.githubusercontent.com/71221969/141057899-cd710bb9-0eae-450b-b941-6bf8b69a0d43.png)

- Pada `Skypie`
![image](https://user-images.githubusercontent.com/71221969/141057994-2a1ff717-11ce-433b-8932-675c35757497.png)

- Pada `TottoLand`
![image](https://user-images.githubusercontent.com/71221969/141058284-a7caebf5-8f4f-4b79-a264-2cd6c36ae7eb.png)

### (Nomor 3) 2. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
- Langkah 1: edit konfigurasi pada file `dhcpd.conf` pada directory `/etc/dhcp/` dengan menambahkan:
```
subnet 192.179.2.0 netmask 255.255.255.0 {
     option routers 192.179.2.1;
}
```
supaya DHCP relay yaitu `Foosha` dapat terhubung dengan subnet `192.179.2.0`.<br>
- Langkah 2: tambahkan juga pada file yang sama:
```
subnet 192.179.1.0 netmask 255.255.255.0 {
    range 192.179.1.20 192.179.1.99;
    range 192.179.1.150 192.179.1.169;
    option routers 192.179.1.1;
    option broadcast-address 192.179.1.255;
    option domain-name-servers 192.179.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}
```
untuk mengatur range IP pada `Switch1`
![image](https://user-images.githubusercontent.com/71221969/141066354-f9f88507-10a9-47d5-a31e-6480ee1d18a7.png)
- Langkah 3: lakukan pengecekan ip pada client yang terhubung dengan `Switch1` menggunakan command `ip a` lalu cek pada bagian `eth0`
  - Pada `Loguetown` terlihat bahwa ip nya adalah `192.179.1.35/24` yang masih termasuk dalam range 
  ![image](https://user-images.githubusercontent.com/71221969/141068827-2546c193-e508-473b-8b62-1cd3130e4ab4.png)
  - Pada `Alabasta` terlihat bahwa ip nya adalah `192.179.1.34/24` yang masih termasuk dalam range
  ![image](https://user-images.githubusercontent.com/71221969/141069228-a7b0831e-4821-4bb3-96e2-3d72abdd5991.png)

### (Nomor 4) 3. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50
- Langkah 1: tambahkan juga konfigurasi pada file `dhcpd.conf` pada directory `/etc/dhcp/` pada DHCP server dengan:
```
subnet 192.179.3.0 netmask 255.255.255.0 {
    range 192.179.3.30 192.179.3.50;
    option routers 192.179.3.1;
    option broadcast-address 192.179.3.255;
    option domain-name-servers 192.179.2.2;
    default-lease-time 720;
    max-lease-time 7200;
}
```
![image](https://user-images.githubusercontent.com/71221969/141066976-1bd7c7d2-edf6-4d33-a44f-d6969ce027d8.png)
- Langkah 2: lakukan pengecekan ip pada client yang terhubung dengan `Switch3` menggunakan command `ip a` lalu cek pada bagian `eth0`
  - Pada `TottoLand` terlihat bahwa ip nya adalah `192.179.3.35/24` yang masih termasuk dalam range
  ![image](https://user-images.githubusercontent.com/71221969/141069896-4eeea5c7-050b-4523-b8a6-55d0fede64ff.png)
 
  - Pada `Skypie` terlihat bahwa ip nya adalah `192.179.3.69/24` yang telah keluar dari range karena ada konfigurasi pada nomor berikutnya yang mengharuskan untuk IP dari `Skypie` adalah `192.179.3.69/24`
  ![image](https://user-images.githubusercontent.com/71221969/141069615-04d05f6a-a62c-4fa8-8350-cf14eeaaf55b.png)

### (Nomor 5) 4. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut.
Untuk mangerjakan ini digunakan setu DNS forwarder yaitu dengan cara:
- Langkah 1: Edit file `/etc/bind/named.conf.options` pada server EniesLobby dengan menambahkan:
```
forwarders {
  192.168.122.1;
};
```
- Langkah 2: comment pada bagian:
```
dnssec-validation auto;
```
- Langkah 3: lalu tambahkan:
```
allow-query{any;};
```
![image](https://user-images.githubusercontent.com/71221969/141067740-7522fe68-c082-483b-96c0-c5e53f8ca9cf.png)
- Langkah 4: restart DNS server yaitu `EniesLobby` dengan cara:
```
service bind9 restart
```
- Langkah 5: cek pada client untuk `ping google.com`
  - pada `Loguetown`
  ![image](https://user-images.githubusercontent.com/71221969/141068145-7ee87e67-92fa-47f1-9072-264e248e97c0.png)
  - pada `Alabasta`
  ![image](https://user-images.githubusercontent.com/71221969/141068274-ba9ec28d-a4f1-416a-a6f6-734231b648da.png)
  - pada `Skypie`
  ![image](https://user-images.githubusercontent.com/71221969/141068342-e5ec382c-00c0-4ee7-b819-351f67109821.png)
  - pada `TottoLand`
  ![image](https://user-images.githubusercontent.com/71221969/141068421-8932f57d-76ea-42e5-b014-fe12416c479f.png)

### (Nomor 6) 5. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.
Nomor ini telah kami kerjakan sekaligus pada nomor 3 sebelumnya yaitu pada code bagian: (pada subnet `192.179.1.0`)
```
default-lease-time 360;
max-lease-time 7200;
```
serta code: (pada subnet `192.179.3.0`)
```
default-lease-time 720;
max-lease-time 7200;
```
## 7. Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69
Pada soal ini digunakan konfigurasi fixed address dengan cara:
- Langkah 1: edit konfigurasi pada file `dhcpd.conf` pada directory `/etc/dhcp/` pada DHCP server yaitu `Jipangu` dengan menambahkan:
```
host Skypie{
    hardware ethernet 1e:04:79:09:f8:1f;
    fixed-address 192.179.3.69;
}
```
![image](https://user-images.githubusercontent.com/71221969/141072086-b4a214c0-f6a7-46f2-8ca9-13177cb2e9d8.png)
`hardware ethernet` dari `Skypie` didapatkan dari command `ip a` dari Skypie:
![image](https://user-images.githubusercontent.com/71221969/141072186-4d9b4727-4335-446c-b2d7-d117ab5404a5.png)
- Langkah 2: restart DHCP server dengan command 
```
isc-dhcp-server
```
- Langkah 3: tambahkan pada file `/etc/network/interfaces` pada `Skypie` sehingga menjadi:
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 1e:04:79:09:f8:1f
```
dengan `hwadress ether` dari langkah 1 sebelumnya, dengan begini IP dari `Skypie` menjadi fixed `192.179.3.69/24`:
![image](https://user-images.githubusercontent.com/71221969/141069615-04d05f6a-a62c-4fa8-8350-cf14eeaaf55b.png)

## 8. Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000
- Langkah 1: Buat konfigurasi Squid baru Pada file `/etc/squid/squid.conf` pada Proxy server yaitu `Water7`:
```
http_port 5000
visible_hostname jualbelikapal.b05.com
```
![image](https://user-images.githubusercontent.com/71221969/141075335-3e14c31d-a655-481e-91f8-906923f1183b.png)
- Langkah 2: export proxy yang telah kita buat dengan cara 
```
export http_proxy=http://192.179.2.3:5000
```
dimana `192.179.2.3` adalah IP dari `Water7` sebagai Proxy server, kemudian proxy bisa di cek dengan command:
```
env | grep -i proxy
```
![image](https://user-images.githubusercontent.com/71221969/141076913-f5f3a579-bcba-4ce1-b0a5-846519d1d75e.png)
- Langkah 3: lakukan pengecekan dengan `lynx its.ac.id` pada `Loguetown`, terlihat bahwa proxy sudah terdeteksi pada kotak hijau
![2021-11-09](https://user-images.githubusercontent.com/71221969/141077685-60d6ff26-963b-4467-b973-9e18227c9cd9.png)
- Langkah 4: karena secara default data dari luar di deny semua oleh proxy server maka bisa ditambahkan:
```
http_access allow all
```
untuk mengizinkan data dari internet masuk ke local,berikut tampilan jika berhasil membuka `its.ac.id`:
![image](https://user-images.githubusercontent.com/71221969/141079042-1173d6fc-fb43-41e0-b09d-1d51ab025f39.png)

## 9. Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy
- Langkah 1: Install `apache2-utils` pada node `Water7`
```
apt-get install apache2-utils -y
```
- Langkah 2: Buat user dan password baru pada file `/etc/squid/passwd`:
  - untuk user: `luffybelikapalb05`
    ```
    touch /etc/squid/passwd
    htpasswd -nbm /etc/squid/passwd luffybelikapalb05
    ```
    lalu ketikkan password: `luffy_b05`
  - untuk user: `zorobelikapalb05`
    ```
    htpasswd --nbm /etc/squid/passwd zorobelikapalb05
    ```
    lalu ketikkan password: `zoro_b05`
  Sehingga isi dari file `/etc/squid/passwd` adalah:
  ![image](https://user-images.githubusercontent.com/71221969/141085430-30dc0acd-0011-47a3-b46a-45ce84b4741d.png)
  dimana password telah ter-enkripsi dengan MD5.
- Langkah 3: Edit konfigurasi squid pada file `/etc/squid/squid.conf` menjadi:
```
http_port 5000
visible_hostname jualbelikapal.b05.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS
```
- Langkah 4: Restart squid
- Langkah 5: uji coba dengan `lynx its.ac.id`
  - Ketika domain telah ditemukan, maka kita akan diminta username dan password untuk authentikasi 
  ![image](https://user-images.githubusercontent.com/71221969/141087665-b7b6c398-2030-4672-bf36-94d2f0813b39.png)
  ![image](https://user-images.githubusercontent.com/71221969/141087788-91ab8e53-dd5e-46b8-b26c-a2235ca38a59.png)
  - Ketika username dan password cocok maka akan masuk ke `its.ac.id`
  ![image](https://user-images.githubusercontent.com/71221969/141087939-bc9caa1a-d027-4ab7-89c5-aa3cb4576765.png)
  - Ketika username dan password tidak cocok maka akan menampilkan:
  ![image](https://user-images.githubusercontent.com/71221969/141088163-abd1b3e5-9f08-4c1c-9344-10634b9bd72a.png)
    kemudian jika memilih untuk tidak mencoba lagi maka akan menampilkan:
  ![image](https://user-images.githubusercontent.com/71221969/141088235-2f5434b9-3be9-44e1-b6a1-a5b72c939438.png)

## 10. Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)
- Langkah 1: Buat file baru bernama `acl.conf` di directory `/etc/squid/` dalam proxy server yaitu `Water7` dengan cara:
```
nano /etc/squid/acl.conf
```
- Langkah 2: tambahkan code berikut: 
```
acl AVAILABLE_WORKING time MTWH 07:00-11:00
acl AVAILABLE_WORKING2 time TWHF 17:00-24:00
acl AVAILABLE_WORKING3 time WHFA 00:00-03:00
```
- Langkah 3: tambahkan konfigurasi pada `squid.conf` pada directory `/etc/squid/`:
```
http_access allow AVAILABLE_WORKING USERS
http_access allow AVAILABLE_WORKING2 USERS
http_access allow AVAILABLE_WORKING3 USERS
```
![image](https://user-images.githubusercontent.com/71221969/141091564-5b5ba0ad-0bdb-40c2-90c2-6606d97272e9.png)
- Langkah 4: restart squid
- Langkah 5: uji coba pada proxy client yaitu `Loguetown`
  - jika date tidak cocok dengan yang ada pada `acl.conf` maka akan menampilkan:
  ![image](https://user-images.githubusercontent.com/71221969/141092422-6dafc4b4-1dbc-41f0-ae8b-c01bd5da101c.png)
  - namun jika date nya cocok maka akan meminta user dan password lalu menampilkan:
  ![image](https://user-images.githubusercontent.com/71221969/141087939-bc9caa1a-d027-4ab7-89c5-aa3cb4576765.png)

## 11. Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie
- Langkah 1: install library yang dibutuhkan pada `Skypie`:
```
apt-get update
apt-get install apache2 -y
```
- Langkah 2: buat sebuah directory baru di dalam `var/www` dengan nama `super.franky.b05.com` lalu isi dengan unzip an dari file yang telah di download:
```
wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip
unzip super.franky.zip
```
- Langkah 3: pindahkan unzip an file tersebut kedalam `/var/www/super.franky.b05.com` sehingga isi folder adalah:
![image](https://user-images.githubusercontent.com/71221969/141103419-501e2e7b-116d-4da0-ab32-5f7098eb6f31.png)

- Langkah 4: buat file `super.franky.b05.com.conf` dengan men-copy file `000-default.conf` pada directory `/etc/apache2/sites-available` lalu menambahkan:
```
ServerAdmin webmaster@localhost
DocumentRoot /var/www/super.franky.b05.com
ServerName super.franky.b05.com
ServerAlias www.super.franky.b05.com
```
![image](https://user-images.githubusercontent.com/71221969/141105085-e5268212-bf4d-4a14-b253-cb8bd3b58654.png)

- Langkah 5: aktifkan konfigurasi `super.franky.b05.com.conf` dengan perintah 
```
a2ensite super.franky.b05.com.conf
```

- Langkah 6: restart apache2
- Langkah 7: melakukan konfigurasi pada DNS server yaitu `EniesLobby` dengan menambahkan file `super.franky.b05.com` pada directory `/etc/bind/kaizoku/` dengan isi file:
![image](https://user-images.githubusercontent.com/71221969/141105557-72100bdd-98c2-443c-9870-aa100d30153c.png)
- Langkah 8: melakukan konfigurasi pada file `/etc/bind/nano named.conf.local` dengan isi file:
```
zone "super.franky.b05.com" {
        type master;
        file "/etc/bind/kaizoku/super.franky.b05.com";
        allow-transfer { 192.179.3.69; };
};
```
![image](https://user-images.githubusercontent.com/71221969/141106275-f6c8c480-1a59-4142-b6d2-fe8290079dca.png)
- Langkah 9: menambahkan `ServerName` dengan IP dari `Skypie` pada file `/etc/apache2/apache2.conf` pada node `Skypie` untuk menandakan jika namaserver nya adalah IP dari `Skypie`:
![image](https://user-images.githubusercontent.com/71221969/141106583-f12723ec-344a-4887-9adb-e584b1250ca1.png)
- Langkah 10: pada file `/etc/nano resolv.conf` ditambahkan `nameserver` dengan ip dari DNS server
![image](https://user-images.githubusercontent.com/71221969/141106974-32e9b076-4994-4027-8d0e-1e77097ac341.png)
- Langkah 11: Kembali pada proxy server yaitu `Water7` lalu tambahkan comand berikut pada file `/etc/squid/squid.conf`
```
acl BLACKLIST dstdomain google.com
deny_info http://super.franky.b05.com/ BLACKLIST
http_reply_access deny BLACKLIST
```
![image](https://user-images.githubusercontent.com/71221969/141107431-ea283106-0689-49ea-bbb9-1ed91b0c9d0f.png)
-Langkah 12: ketika pada `Loguetown` dijalankan `lynx google.com` maka akan me-redirect ke halaman `super.franky.b05.com` yang telah di buat
![image](https://user-images.githubusercontent.com/71221969/141119877-9a49057f-b701-4fec-9af3-28e10005e82f.png)
![image](https://user-images.githubusercontent.com/71221969/141119890-1ea8c389-26bf-44dd-9dbe-868394749e92.png)

## 12. Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps
- Langkah 1: tambahkan file `/etc/squid/acl-bandwidth.conf` dengan isi:
```
acl download url_regex -i \.jpg$ \.png$

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd

acl luffy proxy_auth luffybelikapalb05
acl zoro proxy_auth zorobelikapalb05

delay_pools 2
delay_class 1 1
delay_parameters 1 1250/1250
delay_access 1 allow luffy
delay_access 1 deny zoro
delay_access 1 allow download
delay_access 1 deny all
```
- Langkah 2: include kan `acl-bandwidth.conf` pada file `squid`
```
include /etc/squid/acl-bandwidth.conf
```
![image](https://user-images.githubusercontent.com/71221969/141121500-8fd6d007-797b-439c-90dc-595693203c7b.png)
- Langkah 3: uji coba untuk melakukan download ketika manjadi luffy
![image](https://user-images.githubusercontent.com/71221969/141121886-9fc47ae1-394b-47fb-824b-025af8fe02c4.png)
  Selain itu ketika mencoba download file .js maka kecepatan tidak dibatasi
  ![image](https://user-images.githubusercontent.com/71221969/141122098-123b3609-c8d9-422f-b03b-bafbf16fbb12.png)

## 13. Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya
- Langkah 1: tambahkan isi file `/etc/squid/acl-bandwidth.conf` dengan:
```
delay_class 2 1
delay_parameters 2 -1/-1
delay_access 2 allow zoro
delay_access 2 deny luffy
delay_access 2 deny all
```
- Langkah 2: lakukan uji coba download sebagai zoro maka download akan langsung selesai:
![image](https://user-images.githubusercontent.com/71221969/141122661-f6cb4ec4-e87b-4fef-870b-47aa08d6d6d1.png)
