# ANGGOTA KELOMPOK

| Nama | NRP |
| :--- | :--- |
| Barra Ahza Fakhrullah | 5027251023 |
| Nabila Nafisatus Zuhro | 5027251073 |

## NO 1
Router Lain
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 192.241.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.241.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.241.3.1
    netmask 255.255.255.0
```
alice
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.1.2
    netmask 255.255.255.0
    gateway 192.241.1.1
```

mika
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.1.3
    netmask 255.255.255.0
    gateway 192.241.1.1
```

chisa
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.2.2
    netmask 255.255.255.0
    gateway 192.241.2.1
```

knights
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.3.2
    netmask 255.255.255.0
    gateway 192.241.3.1
```

eiri
```bash
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 192.241.3.3
    netmask 255.255.255.0
    gateway 192.241.3.1
```
# Praktikum Modul 1 — Komunikasi Data & Jaringan Komputer 2026
## Pengerjaan Soal 6–13

---

## Soal 6 — Analisis Traffic DNS dan ICMP

### Tujuan

Pada soal ini dilakukan pengamatan traffic jaringan pada node **Mika**. Traffic DNS dan ICMP dihasilkan dari node Mika, kemudian paket yang melewati interface Mika ditangkap menggunakan **Wireshark**.

Tujuan pengujian ini adalah memahami bagaimana traffic DNS dan ICMP terlihat dalam packet capture serta menggunakan display filter Wireshark untuk menyaring protokol tertentu.

### Langkah Penyelesaian

#### 1. Membuka Console Mika

Buka console node **Mika** pada GNS3.

Script traffic generator disimpan pada directory `/root` dengan nama:

```text
/root/no6.sh
```

Isi script yang digunakan:

```bash
#!/bin/bash

echo "============================================"
echo "  Protocol 7 Traffic Generator v2026"
echo "  Node: Mika Iwakura"
echo "============================================"

echo "[*] Generating DNS & ICMP traffic..."

ping -c 5 8.8.8.8 &
ping -c 5 1.1.1.1 &
ping -c 3 its.ac.id &

nslookup google.com 8.8.8.8 &
nslookup its.ac.id 8.8.8.8 &
nslookup github.com 1.1.1.1 &

dig @8.8.8.8 example.com A &
dig @1.1.1.1 cloudflare.com AAAA &

wait

echo "[*] Traffic generation complete."
echo "[*] Check Wireshark using filter: dns || icmp"
```

#### 2. Memberikan Permission Execute

```bash
chmod +x /root/no6.sh
```

#### 3. Memulai Capture Wireshark

Pada GNS3, lakukan packet capture pada interface node Mika yang terhubung menuju jaringan.

Setelah Wireshark terbuka, jalankan script pada Mika:

```bash
/root/no6.sh
```

Script tersebut menghasilkan beberapa traffic ICMP melalui `ping` serta traffic DNS melalui `nslookup` dan `dig`.

#### 4. Melakukan Filtering

Pada Wireshark gunakan display filter:

```text
dns || icmp
```

Filter tersebut membuat Wireshark hanya menampilkan paket yang menggunakan protokol DNS atau ICMP.

### Hasil dan Analisis

Dari hasil capture ditemukan dua jenis traffic utama, yaitu **DNS** dan **ICMP**.

DNS atau **Domain Name System** digunakan untuk melakukan resolusi nama domain menjadi alamat IP. Ketika Mika melakukan query terhadap suatu domain, dapat diamati adanya DNS query dari client dan DNS response dari DNS server.

ICMP atau **Internet Control Message Protocol** digunakan untuk kebutuhan kontrol dan diagnostik jaringan. Salah satu implementasinya adalah perintah `ping`, yang menghasilkan ICMP Echo Request dan Echo Reply.

Dengan menggunakan filter:

```text
dns || icmp
```

paket yang tidak menggunakan DNS maupun ICMP tidak ditampilkan sehingga proses analisis menjadi lebih terfokus.

### Bukti

> Masukkan screenshot Wireshark setelah menerapkan filter `dns || icmp`.

![Hasil Filter DNS dan ICMP](./img/no6_dns_icmp.png)

### Kesimpulan

Traffic jaringan dapat dipisahkan berdasarkan protokol menggunakan display filter Wireshark. Pada pengujian ini, traffic DNS digunakan untuk proses resolusi domain sedangkan ICMP digunakan untuk komunikasi kontrol dan pengujian konektivitas.

---

## Soal 7 — Konfigurasi FTP Server dan Hak Akses User

### Tujuan

Pada soal ini node **Chisa** dikonfigurasi sebagai FTP Server menggunakan **vsFTPd**.

Shared directory FTP berada pada:

```text
/var/wired/data
```

Terdapat tiga user dengan kebijakan akses berbeda:

| User | Hak Akses |
|---|---|
| `alice` | Read & Write |
| `mika` | Read Only |
| `eiri` | Blacklist / tidak diperbolehkan mengakses FTP |

Tujuan pengujian adalah menerapkan pembatasan akses berbeda terhadap user yang menggunakan layanan FTP yang sama.

### Langkah Penyelesaian

#### 1. Membuka Console Chisa

Buka console node **Chisa**.

Script konfigurasi FTP disimpan pada:

```text
/root/no7.sh
```

#### 2. Menginstal vsFTPd

Pada script dijalankan:

```bash
apk add --no-cache vsftpd
```

#### 3. Membuat Shared Directory

Directory yang digunakan sebagai penyimpanan bersama FTP dibuat menggunakan:

```bash
mkdir -p /var/wired/data
chmod 755 /var/wired
chmod 777 /var/wired/data
```

#### 4. Membuat User FTP

User yang diperlukan adalah `alice`, `mika`, dan `eiri`.

```bash
id alice >/dev/null 2>&1 || adduser alice
id mika >/dev/null 2>&1 || adduser mika
id eiri >/dev/null 2>&1 || adduser eiri
```

Password masing-masing user kemudian dikonfigurasi sesuai kebutuhan pengujian.

#### 5. Membatasi Mika Menjadi Read-Only

Directory konfigurasi per-user dibuat dengan:

```bash
mkdir -p /etc/vsftpd/user_conf
```

Kemudian konfigurasi user Mika dibuat:

```bash
cat > /etc/vsftpd/user_conf/mika <<'MIKA'
write_enable=NO
MIKA
```

Konfigurasi tersebut membuat Mika tidak memiliki hak melakukan operasi penulisan melalui FTP.

#### 6. Melakukan Blacklist terhadap Eiri

User Eiri dimasukkan ke dalam user list:

```bash
echo "eiri" > /etc/vsftpd/user_list
```

Pada konfigurasi vsFTPd digunakan:

```text
userlist_enable=YES
userlist_file=/etc/vsftpd/user_list
userlist_deny=YES
```

Dengan `userlist_deny=YES`, user yang berada di dalam daftar tersebut ditolak oleh FTP Server.

#### 7. Mengatur Konfigurasi Utama vsFTPd

Konfigurasi utama yang digunakan adalah:

```text
listen=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
chroot_local_user=YES
allow_writeable_chroot=YES
local_root=/var/wired/data

user_config_dir=/etc/vsftpd/user_conf
userlist_enable=YES
userlist_file=/etc/vsftpd/user_list
userlist_deny=YES

pasv_enable=YES
pasv_min_port=30000
pasv_max_port=30100

seccomp_sandbox=NO
```

Konfigurasi:

```text
local_root=/var/wired/data
```

membuat user FTP menggunakan `/var/wired/data` sebagai directory utama FTP.

#### 8. Menjalankan FTP Server

```bash
pkill vsftpd 2>/dev/null
vsftpd /etc/vsftpd/vsftpd.conf &
```

#### 9. Melakukan Pengujian Alice

Login ke FTP Server Chisa menggunakan akun `alice`.

Kemudian dibuat atau di-upload file:

```text
signal_alice.txt
```

Keberhasilan operasi tersebut menunjukkan bahwa Alice memiliki hak **read dan write**.

#### 10. Melakukan Pengujian Eiri

Lakukan percobaan login menggunakan akun `eiri`.

Login harus ditolak karena Eiri telah dimasukkan ke dalam blacklist.

### Hasil dan Analisis

FTP Server berhasil dikonfigurasi dengan kebijakan akses berbeda untuk setiap user.

Alice dapat melakukan operasi baca dan tulis. Mika dibatasi menjadi read-only. Sementara itu, Eiri tidak diperbolehkan mengakses FTP Server.

Pengujian ini menunjukkan konsep **authorization**, yaitu pemberian hak akses yang berbeda kepada user setelah identitas user diketahui oleh sistem.

### Bukti

> Masukkan screenshot keberhasilan Alice membuat atau mengirim `signal_alice.txt`.

![Alice Read Write](./img/no7_alice.png)

> Masukkan screenshot penolakan login Eiri.

![Eiri Blacklist](./img/no7_eiri.png)

### Kesimpulan

vsFTPd dapat dikonfigurasi untuk menerapkan kebijakan akses yang berbeda pada masing-masing user. Dengan demikian, administrator dapat mengontrol operasi yang diperbolehkan terhadap setiap pengguna FTP Server.

---

## Soal 8 — Upload File dari Knights ke FTP Server Chisa

### Tujuan

Pada soal ini node **Knights** bertindak sebagai FTP Client dan mengirimkan file menuju FTP Server pada node **Chisa** menggunakan akun `alice`.

Selain melakukan upload, traffic FTP dianalisis menggunakan Wireshark untuk menemukan:

1. Perintah FTP untuk upload, yaitu `STOR`.
2. Response server `226`.
3. Port data TCP yang dinegosiasikan menggunakan Passive Mode.

Alamat IP Chisa:

```text
192.241.2.2
```

### Langkah Penyelesaian

#### 1. Membuka Console Knights

Buka console node **Knights**.

Script pengujian disimpan pada:

```text
/root/no8.sh
```

#### 2. Memastikan File Tersedia

Pastikan file yang akan dikirim tersedia pada node Knights.

Pada pengerjaan digunakan lokasi:

```text
/root/knights_report.txt
```

#### 3. Memulai Packet Capture

Sebelum melakukan koneksi FTP, jalankan packet capture menggunakan Wireshark pada interface yang dilewati traffic Knights menuju Chisa.

Untuk mempermudah analisis, dapat digunakan display filter:

```text
ftp || ftp-data
```

#### 4. Menghubungkan Knights ke Chisa

Koneksi dilakukan menuju:

```text
192.241.2.2
```

menggunakan akun:

```text
alice
```

FTP client dijalankan menggunakan:

```bash
ftp 192.241.2.2
```

Setelah login, file dikirim menuju server menggunakan perintah upload FTP.

#### 5. Mengamati STOR

Pada Wireshark cari paket FTP yang mengandung:

```text
STOR
```

`STOR` merupakan perintah FTP yang meminta server menerima dan menyimpan file yang dikirim oleh client.

#### 6. Mengamati Response 226

Setelah transfer selesai, cari response FTP:

```text
226
```

Response `226` menunjukkan bahwa transfer data telah selesai dengan sukses.

#### 7. Mengamati Passive Mode

Cari komunikasi FTP yang berhubungan dengan:

```text
PASV
```

atau response server terhadap Passive Mode.

Pada Passive Mode, koneksi kontrol dan koneksi data FTP menggunakan jalur yang berbeda. Server memberikan informasi port yang digunakan client untuk membentuk koneksi data.

### Hasil dan Analisis

Upload file dari Knights menuju FTP Server Chisa berhasil dilakukan menggunakan akun Alice.

Pada packet capture ditemukan perintah:

```text
STOR
```

yang menunjukkan proses upload file.

Server kemudian memberikan response:

```text
226
```

yang menunjukkan proses transfer berhasil diselesaikan.

Port data TCP hasil negosiasi PASV:

```text
[ISI PORT DATA TCP BERDASARKAN CAPTURE WIRESHARK]
```

FTP menggunakan **control connection** untuk membawa command seperti login, `PASV`, dan `STOR`, sedangkan isi file dikirim melalui **data connection**.

### Bukti

![FTP STOR](./img/no8_stor.png)

![FTP Response 226](./img/no8_226.png)

![FTP Passive Mode](./img/no8_pasv.png)

### Kesimpulan

FTP memisahkan koneksi kontrol dengan koneksi data. Proses upload dapat diamati melalui command `STOR`, sedangkan response `226` menunjukkan bahwa proses transfer telah selesai dengan sukses.

---

## Soal 9 — Pengujian Hak Akses Read-Only User Mika

### Tujuan

Pada soal ini dilakukan pengujian terhadap kebijakan **read-only** yang telah diberikan kepada user `mika` pada FTP Server Chisa.

Pengujian dilakukan dengan:

1. Mengunduh `protocol7_manifesto.txt`.
2. Mencoba meng-upload file baru.
3. Mengamati response `550 Permission denied`.

### Langkah Penyelesaian

#### 1. Membuka Console Mika

Buka console node **Mika**.

Script pengujian disimpan pada:

```text
/root/no9.sh
```

#### 2. Menyiapkan File untuk Percobaan Upload

File pengujian dapat dibuat menggunakan:

```bash
echo "Mika read-only upload test" > /root/mika_upload_test.txt
```

#### 3. Memulai Capture Wireshark

Capture traffic Mika menuju FTP Server Chisa.

Display filter yang dapat digunakan:

```text
ftp || ftp-data
```

#### 4. Login sebagai Mika

FTP Server Chisa berada pada:

```text
192.241.2.2
```

Koneksi dilakukan menggunakan akun:

```text
mika
```

Pada pengerjaan digunakan FTP client:

```bash
lftp -u mika 192.241.2.2
```

#### 5. Melakukan Download

Unduh file:

```text
protocol7_manifesto.txt
```

Keberhasilan download menunjukkan bahwa Mika memiliki hak membaca file dari FTP Server.

#### 6. Melakukan Percobaan Upload

Selanjutnya coba meng-upload:

```text
mika_upload_test.txt
```

Karena user Mika telah dikonfigurasi read-only, operasi tersebut seharusnya ditolak.

#### 7. Mengamati Response Server

Response yang dicari adalah:

```text
550 Permission denied
```

### Hasil dan Analisis

Mika berhasil melakukan download `protocol7_manifesto.txt`. Hal tersebut menunjukkan bahwa hak akses membaca bekerja.

Ketika Mika mencoba melakukan upload file baru, server memberikan:

```text
550 Permission denied
```

Response tersebut menunjukkan bahwa FTP Server menolak operasi tulis yang dilakukan oleh Mika.

Pengujian ini membuktikan bahwa konfigurasi **read-only** tidak hanya tercantum pada konfigurasi server, tetapi benar-benar diterapkan ketika user mencoba melakukan operasi yang tidak diizinkan.

### Bukti

![Mika Download](./img/no9_download.png)

![Mika 550 Permission Denied](./img/no9_550.png)

### Kesimpulan

User Mika dapat melakukan operasi baca tetapi tidak dapat melakukan operasi tulis. Response `550 Permission denied` menjadi bukti bahwa pembatasan read-only berhasil diterapkan.

---

## Soal 10 — Pengujian ICMP, Packet Loss, dan RTT

### Tujuan

Pada soal ini dilakukan pengujian konektivitas dari node **Knights** menuju **Chisa** menggunakan ICMP dengan parameter tertentu.

Pengujian digunakan untuk mengamati:

1. ICMP Echo Request.
2. ICMP Echo Reply.
3. ICMP Type dan Code.
4. Packet loss.
5. RTT minimum, average, dan maximum.

### Langkah Penyelesaian

#### 1. Membuka Console Knights

Node pengirim adalah:

```text
Knights
```

Sedangkan target adalah Chisa:

```text
192.241.2.2
```

#### 2. Memulai Capture Wireshark

Jalankan packet capture pada interface yang dilewati traffic Knights menuju Chisa.

Gunakan display filter:

```text
icmp
```

#### 3. Menjalankan Ping

Pada Knights jalankan:

```bash
ping -c 77 -s 128 -i 0.3 192.241.2.2
```

Parameter yang digunakan:

| Parameter | Fungsi |
|---|---|
| `-c 77` | Mengirim 77 paket |
| `-s 128` | Menggunakan payload 128 byte |
| `-i 0.3` | Memberikan interval 0,3 detik antar-paket |

#### 4. Mengamati Echo Request

Pada Wireshark pilih salah satu paket:

```text
Echo (ping) request
```

Kemudian buka bagian:

```text
Internet Control Message Protocol
```

Echo Request menggunakan:

```text
Type = 8
Code = 0
```

#### 5. Mengamati Echo Reply

Pilih paket:

```text
Echo (ping) reply
```

Echo Reply menggunakan:

```text
Type = 0
Code = 0
```

#### 6. Mencatat Statistik Ping

Setelah seluruh 77 paket selesai dikirim, output `ping` menampilkan statistik packet loss dan RTT.

Catat nilai:

```text
Packet Loss
RTT min
RTT avg
RTT max
```

### Hasil dan Analisis

Hasil ICMP:

| Jenis | Type | Code |
|---|---:|---:|
| Echo Request | 8 | 0 |
| Echo Reply | 0 | 0 |

Hasil pengujian koneksi:

```text
Packet Loss : [ISI HASIL] %
RTT Minimum : [ISI HASIL] ms
RTT Average : [ISI HASIL] ms
RTT Maximum : [ISI HASIL] ms
```

**Echo Request** merupakan pesan yang dikirim oleh Knights untuk memeriksa keterjangkauan Chisa. Jika Chisa menerima request tersebut dan dapat memberikan respons, Chisa mengirimkan **Echo Reply**.

**Packet loss** menunjukkan persentase paket yang tidak memperoleh balasan.

**RTT (Round Trip Time)** menunjukkan waktu yang diperlukan paket untuk berjalan dari Knights menuju Chisa dan respons kembali diterima oleh Knights.

### Bukti

![Ping Knights Chisa](./img/no10_ping.png)

![ICMP Echo Request](./img/no10_request.png)

![ICMP Echo Reply](./img/no10_reply.png)

### Kesimpulan

ICMP dapat digunakan untuk menguji keterjangkauan dan karakteristik koneksi antar-node. Echo Request dan Echo Reply dapat dibedakan melalui nilai Type, sedangkan output `ping` dapat digunakan untuk melihat packet loss dan RTT.

---

## Soal 11 — Analisis Kelemahan Protokol Telnet

### Tujuan

Pada soal ini dilakukan pengujian terhadap protokol **Telnet** untuk menunjukkan kelemahannya ketika digunakan sebagai remote access.

Telnet Server berada pada node **Chisa**, sedangkan client berada pada node **Eiri**.

Kredensial yang digunakan:

```text
Username : phantom_user
Password : wired_ghost
```

### Langkah Penyelesaian

#### 1. Membuka Console Chisa

Script konfigurasi Telnet disimpan pada:

```text
/root/no11.sh
```

#### 2. Menginstal Telnet Server

Pada Chisa dijalankan:

```bash
apk add --no-cache busybox-extras
```

#### 3. Membuat User

User yang digunakan adalah:

```text
phantom_user
```

Jika user belum tersedia:

```bash
adduser -D phantom_user
```

Password dikonfigurasi menjadi:

```text
wired_ghost
```

menggunakan:

```bash
echo "phantom_user:wired_ghost" | chpasswd
```

#### 4. Menjalankan Telnet Server

Telnet berjalan pada TCP port 23.

```bash
pkill telnetd 2>/dev/null
telnetd -F -p 23 -l /bin/login &
```

#### 5. Memulai Packet Capture

Jalankan Wireshark pada interface yang dilewati traffic Eiri menuju Chisa.

Display filter yang dapat digunakan:

```text
telnet
```

atau:

```text
tcp.port == 23
```

#### 6. Login dari Eiri

Pada console **Eiri**, lakukan:

```bash
telnet 192.241.2.2
```

Masukkan:

```text
Username : phantom_user
Password : wired_ghost
```

#### 7. Membuka Follow TCP Stream

Pada Wireshark:

1. Pilih salah satu paket dari sesi Telnet.
2. Klik kanan paket.
3. Pilih **Follow**.
4. Pilih **TCP Stream**.

Kemudian amati isi komunikasi yang berhasil ditangkap.

### Hasil dan Analisis

Dari hasil **Follow TCP Stream**, komunikasi Telnet dapat direkonstruksi karena Telnet tidak memberikan perlindungan enkripsi seperti SSH.

Informasi sensitif yang dikirim selama sesi berpotensi terlihat dari packet capture apabila penyerang memiliki kemampuan untuk melakukan packet sniffing.

Pada komunikasi Telnet interaktif, input keyboard juga dapat dikirim secara bertahap melalui TCP. Akibatnya karakter yang dimasukkan user dapat tersebar dalam beberapa paket TCP yang berbeda.

Hal ini menunjukkan salah satu alasan Telnet tidak direkomendasikan untuk remote administration pada jaringan yang membutuhkan keamanan.

### Bukti

![Telnet Login](./img/no11_telnet.png)

![Telnet Follow TCP Stream](./img/no11_stream.png)

### Kesimpulan

Telnet tidak menyediakan perlindungan enkripsi seperti SSH. Oleh karena itu, informasi yang dikirim selama sesi dapat terekspos apabila traffic berhasil ditangkap dan dianalisis.

---

## Soal 12 — Port Scanning Menggunakan Netcat

### Tujuan

Pada soal ini node **Alice** digunakan untuk memeriksa status beberapa TCP port pada node **Knights** menggunakan Netcat.

Alamat Knights:

```text
192.241.3.2
```

Port yang diperiksa:

| Port | Service/Keterangan | Kondisi |
|---:|---|---|
| 22 | SSH | Open |
| 80 | HTTP | Open |
| 7777 | Secret Port | Closed |

Selain menentukan status port, traffic dianalisis menggunakan Wireshark untuk membandingkan TCP Flag pada port terbuka dan tertutup.

### Langkah Penyelesaian

#### 1. Memastikan Service pada Knights

Sebelum melakukan scanning, service SSH dan HTTP pada Knights harus berada dalam keadaan aktif sesuai kondisi yang diminta soal.

Port `7777` dibiarkan tidak memiliki service sehingga berada dalam keadaan closed.

#### 2. Membuka Console Alice

Script scanning disimpan pada:

```text
/root/no12.sh
```

#### 3. Memulai Packet Capture

Jalankan Wireshark pada interface yang dilewati traffic Alice menuju Knights.

Display filter yang dapat digunakan:

```text
tcp
```

atau lebih spesifik:

```text
ip.addr == 192.241.3.2
```

#### 4. Memeriksa Port 22

Pada Alice:

```bash
nc -zv -w 2 192.241.3.2 22
```

#### 5. Memeriksa Port 80

```bash
nc -zv -w 2 192.241.3.2 80
```

#### 6. Memeriksa Port 7777

```bash
nc -zv -w 2 192.241.3.2 7777
```

#### 7. Menganalisis Port Terbuka

Pada port terbuka, Alice memulai koneksi TCP dengan mengirim:

```text
SYN
```

Knights memberikan response:

```text
SYN-ACK
```

Secara sederhana:

```text
Alice                       Knights
  |                            |
  | -------- SYN -----------> |
  | <------ SYN-ACK --------- |
```

Response SYN-ACK menunjukkan terdapat service yang menerima koneksi pada port tersebut.

#### 8. Menganalisis Port Tertutup

Ketika SYN dikirim menuju port yang tidak memiliki service yang menerima koneksi, response yang diamati pada skenario ini berupa:

```text
RST / RST-ACK
```

Secara sederhana:

```text
Alice                       Knights
  |                            |
  | -------- SYN -----------> |
  | <------ RST-ACK --------- |
```

### Hasil dan Analisis

Hasil pemeriksaan:

| Port | Hasil |
|---:|---|
| 22 | Open |
| 80 | Open |
| 7777 | Closed |

Perbedaan utama terlihat pada TCP Flag yang diberikan oleh target.

Pada port terbuka:

```text
SYN → SYN-ACK
```

Sedangkan pada port tertutup:

```text
SYN → RST/RST-ACK
```

Hal tersebut memungkinkan status suatu port dianalisis berdasarkan response TCP yang diterima.

### Bukti

![Netcat Scan](./img/no12_nc.png)

![Open Port SYN ACK](./img/no12_open.png)

![Closed Port RST ACK](./img/no12_closed.png)

### Kesimpulan

Netcat dapat digunakan untuk melakukan pemeriksaan konektivitas terhadap TCP port tertentu. Hasil tersebut dapat diverifikasi melalui Wireshark dengan mengamati perbedaan response TCP pada port terbuka dan tertutup.

---

## Soal 13 — SSH Public Key Authentication

### Tujuan

Pada soal ini dilakukan konfigurasi remote administration menggunakan **SSH** dengan **Public Key Authentication**.

Node yang digunakan:

| Fungsi | Node |
|---|---|
| SSH Client | Mika |
| SSH Server | Knights |
| User SSH | `mika_admin` |

Alamat Knights:

```text
192.241.3.2
```

Tujuan konfigurasi adalah membuat Mika dapat melakukan autentikasi menuju Knights menggunakan pasangan public/private key tanpa menggunakan password login.

Traffic SSH kemudian dianalisis menggunakan Wireshark dan dibandingkan dengan Telnet pada Soal 11.

### Langkah Penyelesaian

#### 1. Menginstal OpenSSH pada Knights

Buka console **Knights**.

Install OpenSSH:

```bash
apk add --no-cache openssh
```

#### 2. Membuat Host Key SSH Server

```bash
ssh-keygen -A
```

#### 3. Membuat User mika_admin

```bash
adduser -D mika_admin
```

User tersebut akan digunakan ketika Mika melakukan remote login menuju Knights.

#### 4. Membuat Key Pair pada Mika

Buka console **Mika**.

Buat directory SSH:

```bash
mkdir -p /root/.ssh
```

Kemudian buat pasangan kunci ED25519:

```bash
ssh-keygen -t ed25519 -f /root/.ssh/id_ed25519 -N ""
```

Dari proses tersebut dihasilkan:

```text
/root/.ssh/id_ed25519
/root/.ssh/id_ed25519.pub
```

File:

```text
id_ed25519
```

merupakan **private key** dan harus tetap disimpan pada Mika.

Sedangkan:

```text
id_ed25519.pub
```

merupakan **public key** yang diberikan kepada Knights.

#### 5. Melihat Public Key Mika

Pada Mika:

```bash
cat /root/.ssh/id_ed25519.pub
```

Salin public key yang ditampilkan.

#### 6. Menambahkan Public Key pada Knights

Pada Knights buat directory:

```bash
mkdir -p /home/mika_admin/.ssh
```

Masukkan public key Mika ke:

```text
/home/mika_admin/.ssh/authorized_keys
```

Kemudian atur ownership:

```bash
chown -R mika_admin:mika_admin /home/mika_admin/.ssh
```

Atur permission:

```bash
chmod 700 /home/mika_admin/.ssh
chmod 600 /home/mika_admin/.ssh/authorized_keys
```

#### 7. Mengaktifkan Public Key Authentication

Pada konfigurasi SSH Server pastikan:

```text
PubkeyAuthentication yes
PasswordAuthentication no
```

`PubkeyAuthentication yes` mengizinkan autentikasi menggunakan public key.

`PasswordAuthentication no` menonaktifkan autentikasi login menggunakan password.

#### 8. Menjalankan SSH Server

Restart atau jalankan SSH Server:

```bash
pkill sshd 2>/dev/null
/usr/sbin/sshd
```

#### 9. Memulai Packet Capture

Sebelum melakukan koneksi dari Mika, jalankan Wireshark pada interface yang dilewati traffic Mika menuju Knights.

Gunakan display filter:

```text
tcp.port == 22
```

#### 10. Melakukan Koneksi SSH

Pada Mika:

```bash
ssh -i /root/.ssh/id_ed25519 mika_admin@192.241.3.2
```

Jika konfigurasi public key authentication telah berhasil, autentikasi dilakukan menggunakan key yang dimiliki Mika tanpa menggunakan password login.

#### 11. Mengamati Protocol Version Exchange

Pada awal sesi SSH, client dan server melakukan **Protocol Version Exchange**.

Tahap tersebut digunakan untuk bertukar informasi mengenai versi/implementasi SSH yang digunakan oleh kedua pihak.

#### 12. Mengamati Key Exchange

Setelah pertukaran informasi protokol, dilakukan **Key Exchange**.

Tahap ini merupakan bagian dari proses pembentukan komunikasi terenkripsi antara client dan server.

#### 13. Mengamati Encrypted Packet

Setelah proses pembentukan sesi selesai, paket komunikasi berikutnya tidak dapat dibaca sebagai plaintext secara langsung seperti pada Telnet.

### Hasil dan Analisis

SSH menggunakan mekanisme kriptografi untuk melindungi sesi komunikasi antara Mika dan Knights.

Pada pengujian ini digunakan pasangan:

```text
Private Key → disimpan pada Mika
Public Key  → disimpan pada Knights
```

Public key ditempatkan pada:

```text
/home/mika_admin/.ssh/authorized_keys
```

sehingga Knights dapat menggunakan konfigurasi tersebut sebagai bagian dari proses autentikasi terhadap Mika.

Pada Wireshark dapat diamati tahapan awal komunikasi berupa **Protocol Version Exchange**, kemudian proses **Key Exchange**, dan selanjutnya komunikasi yang telah terenkripsi.

Berbeda dengan Telnet pada Soal 11, isi komunikasi SSH tidak dapat langsung dibaca sebagai plaintext dari packet capture.

### Perbandingan Telnet dan SSH

| Aspek | Telnet | SSH |
|---|---|---|
| Fungsi | Remote Access | Remote Access |
| Port Default | 23 | 22 |
| Enkripsi | Tidak | Ya |
| Perlindungan isi sesi | Tidak terenkripsi seperti SSH | Dienkripsi |
| Kredensial/data dari capture | Berpotensi terekspos | Tidak terlihat langsung sebagai plaintext |
| Remote administration aman | Tidak direkomendasikan | Lebih sesuai |

### Bukti

> Masukkan screenshot keberhasilan koneksi SSH dari Mika menuju Knights.

![SSH Connection](./img/no13_ssh.png)

> Masukkan screenshot Protocol Version Exchange.

![SSH Protocol Version Exchange](./img/no13_protocol.png)

> Masukkan screenshot proses Key Exchange.

![SSH Key Exchange](./img/no13_keyexchange.png)

> Masukkan screenshot paket komunikasi terenkripsi.

![SSH Encrypted Packet](./img/no13_encrypted.png)

### Kesimpulan

SSH memberikan perlindungan komunikasi yang lebih baik dibandingkan Telnet karena sesi komunikasi dilindungi menggunakan mekanisme kriptografi.

Public Key Authentication memungkinkan autentikasi dilakukan menggunakan pasangan public/private key. Private key tetap disimpan pada client Mika, sedangkan public key ditempatkan pada server Knights.

Dari packet capture dapat diamati proses awal pembentukan sesi SSH, tetapi komunikasi setelah sesi terenkripsi tidak dapat dibaca sebagai plaintext seperti pada Telnet.

---

# Kesimpulan Pengerjaan Soal 6–13

Pada Soal 6 sampai 13 dilakukan implementasi dan analisis berbagai protokol serta layanan jaringan menggunakan **GNS3** dan **Wireshark**.

Pada **Soal 6**, traffic DNS dan ICMP dihasilkan pada node Mika dan dianalisis menggunakan display filter Wireshark. Pengujian menunjukkan bahwa Wireshark dapat digunakan untuk menyaring dan menganalisis protokol tertentu dari keseluruhan traffic jaringan.

Pada **Soal 7 sampai 9**, dilakukan implementasi FTP Server pada node Chisa beserta kebijakan hak akses yang berbeda. Alice memperoleh akses read-write, Mika memperoleh akses read-only, sedangkan Eiri dimasukkan ke dalam blacklist. Proses upload dan download FTP kemudian dianalisis melalui command serta response yang terlihat pada packet capture.

Pada **Soal 10**, dilakukan pengujian ICMP dari Knights menuju Chisa. Echo Request memiliki Type 8 Code 0, sedangkan Echo Reply memiliki Type 0 Code 0. Selain itu, output `ping` dapat digunakan untuk mengamati packet loss dan Round Trip Time.

Pada **Soal 11**, dilakukan analisis terhadap Telnet. Packet capture menunjukkan bahwa komunikasi Telnet tidak memberikan perlindungan enkripsi seperti SSH sehingga informasi yang dikirim selama sesi berpotensi terekspos apabila traffic berhasil ditangkap.

Pada **Soal 12**, dilakukan pemeriksaan TCP port menggunakan Netcat. Port terbuka dan tertutup dapat dibedakan berdasarkan response TCP yang terlihat pada Wireshark. Port terbuka memberikan response SYN-ACK, sedangkan port tertutup memberikan RST/RST-ACK pada skenario pengujian.

Pada **Soal 13**, dilakukan implementasi SSH Public Key Authentication dari Mika menuju Knights. Pasangan public/private key digunakan untuk autentikasi, sedangkan password authentication dinonaktifkan. Packet capture menunjukkan adanya proses Protocol Version Exchange dan Key Exchange sebelum komunikasi terenkripsi berlangsung.

Secara keseluruhan, rangkaian praktikum ini menunjukkan bahwa keberhasilan komunikasi jaringan tidak hanya dapat diperiksa dari sisi aplikasi, tetapi juga dapat dianalisis hingga tingkat paket menggunakan Wireshark. Analisis tersebut dapat digunakan untuk memahami mekanisme kerja protokol, melakukan troubleshooting, memverifikasi kebijakan akses, serta membandingkan aspek keamanan dari berbagai layanan jaringan.
