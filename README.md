# Jarkom-Modul-3-B05-2021

## 1. Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server
- Langkah 1: Buat topologi nya
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
- Langkah 3: Set interfaces pada file `/etc/default/isc-dhcp-server` menjadi `eth0` supaya dia dapat terhubung ke switch yang menghubungkannya dengan DHCP relay
![image](https://user-images.githubusercontent.com/71221969/141052960-5e865626-a521-4bda-9fba-5d28b924d400.png)
