# INTRO TO INTERNET GATEWAY

Secara singkat, **Internet Gateway** adalah sebuah perangkat (dalam kasus ini, router MikroTik) yang berfungsi sebagai jembatan penghubung antara jaringan lokal (LAN) dengan jaringan internet. Gateway inilah yang memungkinkan semua komputer di dalam jaringan lokal kita bisa berkomunikasi dan mengakses internet.

Untuk memahaminya lebih dalam, kita akan mempraktikkannya dengan membuat topologi sederhana menggunakan platform GNS3 dan Router MikroTik virtual. Topologi yang akan kita gunakan bisa dilihat pada tautan berikut:
[https://drive.google.com/open?id=1yj8fZPzr_bcyQbtXklfT3J2taTuy0PmL&usp=drive_fs](https://drive.google.com/open?id=1yj8fZPzr_bcyQbtXklfT3J2taTuy0PmL&usp=drive_fs)


## LAB 1: Internet Gateway
Untuk mengkonfigurasikan Internet Gateway pada jaringan kita, ada beberapa langkah utama yang perlu dilakukan. Konfigurasinya cukup sederhana, mari kita mulai!

Langkah-langkah konfigurasi:
- **DHCP Client:** Memastikan router sudah mendapatkan IP Address dari ISP (Internet Service Provider).
- **IP Address Lokal:** Membuat IP Address yang mengarah ke jaringan lokal (switch) untuk dijadikan gateway bagi para client.
- **DHCP Server:** Memberikan IP Address secara dinamis kepada komputer client menggunakan DHCP Server.
- **NAT (Network Address Translation):** Membuat aturan NAT pada Firewall agar client bisa terhubung ke internet.
- **Pengujian:** Menguji koneksi internet dari sisi client.

Mari kita mulai dari langkah pertama.

### 1. Memeriksa Koneksi ke ISP (DHCP Client)
Untuk mengecek apakah router kita sudah mendapatkan IP dari ISP, jalankan perintah berikut di terminal MikroTik:

```bash
ip dhcp-client pr detail
```

Hasilnya akan terlihat seperti di bawah ini:

```text
Flags: X - disabled, I - invalid, D - dynamic
0   interface=ether1 add-default-route=yes default-route-distance=1
use-peer-dns=yes use-peer-ntp=yes dhcp-options=hostname,clientid
status=bound address=192.168.122.76/24 gateway=192.168.122.1
dhcp-server=192.168.122.1 primary-dns=192.168.122.1 expires-after=57m18s
```

Dari output di atas, kita bisa lihat router mendapat IP Dinamis `192.168.122.76` dari ISP (dalam lab ini adalah GNS3 VM) melalui interface `ether1`. Gateway menuju internet adalah `192.168.122.1`. Setelah ini terkonfirmasi, kita bisa lanjut ke langkah berikutnya.

### 2. Konfigurasi IP Address Lokal
Sekarang, kita konfigurasikan IP Address untuk jaringan lokal yang mengarah ke Switch (dan komputer client) melalui `ether2`. IP ini akan menjadi gateway bagi client.

```bash
ip address add address=192.168.1.1/24 interface=ether2
```

Cek kembali apakah IP sudah terpasang dengan benar:

```bash
ip address pr
```

### 3. Konfigurasi DHCP Server
Agar client mendapatkan IP secara otomatis, kita perlu membuat DHCP Server. Kita akan menggunakan setup interaktif di terminal.

```bash
ip dhcp-server setup
```

Setelah menjalankan perintah di atas, Anda akan diminta mengisi beberapa informasi. Ikuti panduan berikut:

```text
Select interface to run DHCP server on
dhcp server interface: ether2

Select network for DHCP addresses
dhcp address space: 192.168.1.0/24

Select gateway for given network
gateway for dhcp network: 192.168.1.1

Select pool of ip addresses given out by DHCP server
addresses to give out: 192.168.1.2-192.168.1.254

Select DNS servers
dns servers: 192.168.122.1,8.8.8.8

Select lease time
lease time: 10m
```

Setelah selesai, cek konfigurasi DHCP Server dengan perintah:

```bash
ip dhcp-server pr detail
```

Hasilnya akan seperti ini:

```text
Flags: D - dynamic, X - disabled, I - invalid
0    name="dhcp1" interface=ether2 lease-time=10m address-pool=dhcp_pool0 authoritative=yes use-radius=no lease-script=""
```

**Catatan:** `address-pool` dengan nama `dhcp_pool0` dibuat secara otomatis oleh RouterOS saat kita memasukkan `dhcp address space`. Pool ini berisi rentang IP yang akan dibagikan ke client. Untuk mengeceknya, gunakan perintah `ip pool pr detail`.

### 4. Pengujian DHCP pada Client
Pada komputer client (di GNS3 kita menggunakan VPCS), jalankan perintah `ip dhcp` untuk meminta IP dari router.

```text
PC1> ip dhcp
DORA IP 192.168.1.254/24 GW 192.168.1.1

PC1> sh ip

NAME        : PC1[1]
IP/MASK     : 192.168.1.254/24
GATEWAY     : 192.168.1.1
DNS         : 192.168.122.1  8.8.8.8
DHCP SERVER : 192.168.1.1
DHCP LEASE  : 597, 600/300/525
MAC         : 00:50:79:66:68:00
```

Terlihat PC1 sudah mendapatkan IP, Gateway, dan DNS dengan benar.

### 5. Konfigurasi NAT (Network Address Translation)
Langkah terakhir dan paling krusial adalah membuat aturan NAT. Tanpa NAT, client tidak akan bisa mengakses internet. Aturan ini akan "menerjemahkan" IP privat client menjadi IP publik yang ada di router.

```bash
ip firewall nat add chain=srcnat action=masquerade out-interface=ether1
```

- **`chain=srcnat`**: Aturan berlaku untuk paket yang berasal dari jaringan kita.
- **`action=masquerade`**: "Menyembunyikan" semua IP privat di belakang satu IP publik yang ada di `out-interface`.
- **`out-interface=ether1`**: Interface yang terhubung ke internet/ISP.

Lihat hasil konfigurasinya dengan:

```bash
ip firewall nat pr detail
```

```text
Flags: X - disabled, I - invalid, D - dynamic
0    chain=srcnat action=masquerade out-interface=ether1 log=no log-prefix=""
```

### 6. Pengujian Koneksi Internet
Sekarang, mari kita uji koneksi internet dari salah satu PC, misalnya dengan `ping` ke `google.com`.

```text
PC2> ping google.com
google.com resolved to 74.125.68.138

84 bytes from 74.125.68.138 icmp_seq=1 ttl=103 time=21.589 ms
84 bytes from 74.125.68.138 icmp_seq=2 ttl=103 time=21.623 ms
```

Berhasil! Komputer client sudah bisa terhubung ke internet. Kita juga bisa melacak rute paketnya dengan `trace`:

```text
PC2> trace google.com
google.com resolved to 74.125.68.139
trace to google.com, 8 hops max, press Ctrl+C to stop
1   192.168.1.1       2.356 ms  0.353 ms  0.300 ms --> Gateway Lokal (Router kita)
2   192.168.122.1     0.870 ms  0.415 ms  0.436 ms --> Gateway ISP
3   10.0.3.2          0.559 ms  0.400 ms  1.293 ms --> Internet
```

Sampai di sini, kita sudah berhasil mengkonfigurasikan basic Internet Gateway menggunakan MikroTik.

## Bonus Lab: NAT untuk User Tertentu

Sebelumnya, kita membuat aturan NAT yang berlaku untuk semua komputer di jaringan. Sekarang, kita akan coba memodifikasinya agar hanya berlaku untuk user tertentu.

### Mengizinkan Internet Hanya untuk Satu User
Jika kita ingin hanya PC1 dengan IP `192.168.1.254` yang bisa mengakses internet, kita cukup menambahkan `src-address` pada aturan NAT sebelumnya.

```bash
ip firewall nat add chain=srcnat src-address=192.168.1.254 action=masquerade out-interface=ether1
```

Dengan aturan ini, hanya trafik yang berasal dari IP `192.168.1.254` yang akan di-NAT. Komputer lain secara otomatis tidak akan bisa terhubung ke internet.

### Memblokir Internet untuk Satu User
Bagaimana jika sebaliknya? Kita ingin semua bisa internetan, *kecuali* PC1. Kita cukup menambahkan tanda seru (`!`) di depan IP address-nya.

```bash
ip firewall nat add chain=srcnat src-address=!192.168.1.254 action=masquerade out-interface=ether1
```

Tanda `!` berarti "kecuali" atau "not". Jadi, aturan ini akan berlaku untuk semua alamat IP sumber, *kecuali* `192.168.1.254`. Jika menggunakan Winbox, Anda bisa mencentang kotak kecil di sebelah kiri kolom isian IP untuk memunculkan tanda `!`.

# Ringkasan Lab dan Materi
Dalam rangkaian lab ini, kita telah berhasil mengkonfigurasi sebuah router MikroTik agar berfungsi sebagai Internet Gateway. Konfigurasi ini memungkinkan komputer di dalam jaringan lokal untuk dapat terhubung ke internet. Proses ini melibatkan beberapa langkah kunci yang saling berkaitan dan membangun pondasi dasar jaringan yang fungsional.

Berikut adalah kesimpulan dari setiap tahapan yang telah kita kerjakan:
* **Lab 1 - Internet Gateway:**
    * **Koneksi ke ISP:** Router dikonfigurasi sebagai DHCP Client pada `ether1` untuk menerima alokasi IP, gateway, dan DNS secara otomatis dari Internet Service Provider (ISP). Ini adalah langkah pertama untuk memastikan router itu sendiri memiliki akses ke internet.
    * **Jaringan Lokal (LAN):** Dibuat sebuah segmen jaringan baru untuk klien dengan memberikan IP Address (`192.168.1.1/24`) pada `ether2`. IP ini berfungsi sebagai *gateway* atau pintu keluar bagi semua perangkat di jaringan lokal yang ingin mengakses internet.
    * **Distribusi IP Dinamis:** DHCP Server diaktifkan pada interface LAN untuk memberikan alamat IP, gateway, dan DNS secara otomatis kepada komputer klien. Ini sangat mempermudah manajemen jaringan karena admin tidak perlu mengatur IP di setiap komputer secara manual.
    * **Network Address Translation (NAT):** Aturan NAT dengan `action=masquerade` diterapkan. Aturan ini sangat penting karena berfungsi untuk "menerjemahkan" semua alamat IP privat dari jaringan lokal di belakang satu IP publik yang didapat router dari ISP. Tanpa NAT, komputer dengan IP privat tidak akan bisa berkomunikasi di internet.

* **Lab Bonus - NAT Spesifik:**
    * **Mengizinkan Satu User:** Dengan menambahkan parameter `src-address` pada aturan NAT, kita dapat menentukan bahwa hanya komputer dengan IP tersebut yang paketnya akan di-NAT. Ini memberikan kontrol akses internet yang spesifik, misalnya hanya untuk server atau user tertentu.
    * **Memblokir Satu User:** Dengan menggunakan negasi (`!`) pada `src-address`, kita membuat aturan NAT yang berlaku untuk semua komputer *kecuali* komputer dengan IP yang ditentukan. Ini adalah cara efektif untuk memblokir akses internet bagi pengguna tertentu tanpa harus membuat banyak aturan.
