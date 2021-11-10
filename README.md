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
## 1. Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
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

## 2. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
