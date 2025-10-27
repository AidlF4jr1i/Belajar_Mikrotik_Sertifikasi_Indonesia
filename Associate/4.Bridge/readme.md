# Konsep Bridge di MikroTik

## 🧠 Konsep Bridge

**Bridge** pada MikroTik adalah sebuah mekanisme untuk membuat satu interface *virtual* (logis) yang mewakili beberapa interface fisik sekaligus, seperti `ether2`, `ether3`, atau `wlan1`.  
Sederhananya, kita menggabungkan beberapa port fisik agar bertindak sebagai satu kesatuan.

Tujuan utamanya adalah agar port-port router yang biasanya bekerja di Layer 3 (routing) dapat berfungsi layaknya sebuah *Switch* (Layer 2).  
Fitur ini sangat berguna ketika router memiliki banyak port dan kita membutuhkan fungsi switch tambahan.

Biasanya, konfigurasi bridge digunakan di lingkungan rumah atau kantor kecil, di mana cukup menggunakan satu konfigurasi IP untuk seluruh port (yang terhubung ke TV, PC, Access Point, dll), sehingga memudahkan koneksi internet dan manajemen jaringan.

---

## 🧪 LABS Bridge

**[Topologi PNETLAB](https://drive.google.com/open?id=1O8iCKSctsTXg7CeY8as9sJWmMRuiSPG0&usp=drive_fs)**

Pada Lab ini, kita akan mengkonfigurasikan *Bridge* sederhana di router MikroTik. Caranya mudah!

1. **Login ke Winbox** melalui IP DHCP yang diperoleh dari Cloud NAT.  
   Contoh: [Login Winbox via IP DHCP dari NAT](https://drive.google.com/open?id=1_uG3neDCbTlKBJqWBSqzkFu1m3-CST4X&usp=drive_fs)

2. Masuk ke menu **Bridge** di Winbox.

3. Pada tab **Bridge**, klik tombol **“+”** untuk menambahkan bridge baru.  
   Kamu bisa menamai sesuai keinginan atau gunakan default `bridge1`.  
   Contoh: [Menambahkan Bridge Baru “bridge1”](https://drive.google.com/open?id=1mKUSUzqRCH8RYdj6fQXEBM3eNPQYAleq&usp=drive_fs)

4. Buka tab **Ports** untuk menambahkan port interface fisik (misalnya `ether2`, `ether3`) yang akan digabung ke dalam `bridge1`.  
   Contoh: [Menambahkan Port Interface ke Bridge](https://drive.google.com/open?id=13PXywWJftyq1VIFx6BlZu01DbQlpK6wu&usp=drive_fs)

5. Selesai! Sekarang `bridge1` sudah berisi port fisik (`ether2` dan `ether3`). Semua perangkat yang terhubung ke port tersebut akan dianggap berada pada jaringan yang sama.

6. Selanjutnya, tambahkan **IP Address** ke interface `bridge1`.  
   Masuk ke **IP → Addresses**, lalu buat IP baru dan pilih interface `bridge1`.  
   Contoh: [Setting IP Address untuk interface bridge1](https://drive.google.com/open?id=1-oYiiIlNZICh5M2lOoOLsn8zvyH7u7of&usp=drive_fs)

7. Buat **DHCP Server** agar perangkat yang terhubung dapat memperoleh IP otomatis.  
   Masuk ke **IP → DHCP Server → DHCP Setup**, dan jalankan setup di interface `bridge1`.  
   Contoh: [Menjalankan DHCP Setup](https://drive.google.com/open?id=10H3iKQoeNcsc9TUL1LDQuJfLjSXRbNQQ&usp=drive_fs)

8. Uji koneksi di perangkat yang terhubung (contoh: VPCs). Jalankan perintah berikut:
   ```
   ip dhcp
   ```
   Contoh hasil: [VPCs Mendapatkan IP DHCP](https://drive.google.com/open?id=1vAtHnVSuHS_g-S5Jd6xRrcwEkws4Cj_Y&usp=drive_fs)

9. Untuk memastikan koneksi antar device berhasil, jalankan perintah `trace` dari VPC1 ke IP VPC2.  
   Contoh: [Hasil Trace antar VPCs - Direct](https://drive.google.com/open?id=17IAK8HWOvBRVu9rcQMTocSEMWHIzsevL&usp=drive_fs)

Hasil trace menunjukkan bahwa koneksi langsung terjadi tanpa *next hop*! Ini membuktikan bahwa kedua perangkat (`ether2` dan `ether3`) berada dalam jaringan Layer 2 yang sama — persis seperti terhubung ke switch. 🎉

---

## 📋 Ringkasan LABS

Secara singkat, konsep **Bridge** di MikroTik sangat sederhana:  
Kita “meleburkan” beberapa port fisik (misal `ether2`, `ether3`) menjadi satu interface virtual (`bridge1`).

**Pada LAB ini:**
- IP Address dan DHCP Server dikonfigurasi di interface `bridge1`, bukan di masing-masing `ether`.
- Semua perangkat yang terhubung ke port anggota bridge mendapatkan IP dari DHCP yang sama dan berada di satu segmen jaringan.

### 🔑 Poin Penting

| Komponen | Penjelasan |
|-----------|-------------|
| **Konsep** | Bridge adalah interface virtual yang menggabungkan beberapa port fisik (`ether2`, `ether3`, `wlan1`) menjadi satu kesatuan. |
| **Tujuan** | Mengubah port router Layer 3 menjadi berfungsi seperti port switch Layer 2. |
| **Langkah LABS** | 1️⃣ Membuat `bridge` baru → 2️⃣ Menambahkan port (`ether2`, `ether3`) → 3️⃣ Mengatur IP & DHCP di `bridge1`. |
| **Hasil** | Semua perangkat di port anggota bridge berada di satu jaringan yang sama dan bisa saling komunikasi langsung (direct). |

---
