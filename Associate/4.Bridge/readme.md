# Konsep Bridge

Bridge adalah mekanisme di MikroTik di mana kita membuat satu interface *virtual* (logika) yang mewakili beberapa interface fisik (misalnya `ether2`, `ether3`, `wlan1`) sekaligus. Gampangnya, kita “menggabungkan” port-port fisik itu agar menjadi satu.

Tujuannya apa? Agar port-port router yang aslinya bekerja di Layer 3 (routing) bisa berfungsi seperti *Switch* (Layer 2).

Ini berguna banget kalau router kita punya banyak port tetapi kita membutuhkan fungsionalitas switch tambahan. Jadi, port-port router itu bisa kita alihfungsikan sebagai alternatif switch.

Penggunaan bridge yang umum biasanya ada di rumah, di mana kita hanya perlu satu konfigurasi IP untuk semua port (yang terhubung ke TV, PC, Access Point, dan sebagainya) agar lebih mudah untuk kebutuhan internetan dan konfigurasi sederhana lainnya.

---

## LABS Bridge

**Topology Bridge:**  
<https://drive.google.com/open?id=1_EmkIs9_lacQ04zO4Rw072cQBKASTQlr&usp=drive_fs>

Pada lab ini, kita akan mengonfigurasikan Bridge secara sederhana di router MikroTik. Caranya sangat mudah!

1. **Login ke Winbox** melalui IP address DHCP yang didapat dari Cloud NAT.  
   Contoh: <https://drive.google.com/open?id=1_uG3neDCbTlKBJqWBSqzkFu1m3-CST4X&usp=drive_fs>

2. **Buka menu “Bridge”** di Winbox.

3. Di tab **“Bridge”**, klik tombol **“+”** untuk menambahkan bridge baru.  
   Kamu bisa memberikan nama sesuai keinginan atau menggunakan nama default (`bridge1`).  
   Contoh: <https://drive.google.com/open?id=1mKUSUzqRCH8RYdj6fQXEBM3eNPQYAleq&usp=drive_fs>

4. **Tambahkan port fisik** pada tab **“Ports”** untuk digabungkan ke dalam interface `bridge1`.  
   Contoh: <https://drive.google.com/open?id=13PXywWJftyq1VIFx6BlZu01DbQlpK6wu&usp=drive_fs>  
   (Ulangi langkah ini untuk `ether2`, `ether3`, dst.)

5. Selesai! Sekarang kita memiliki interface bridge (`bridge1`) yang berisi port fisik (`ether2` & `ether3`).  
   Perangkat yang tersambung ke port berbeda (asal port tersebut anggota bridge) akan dianggap sebagai satu port yang sama.

6. **Setting IP Address** untuk bridge: masuk ke menu **“IP” → “Addresses”**.  
   Tambahkan IP baru, lalu pada kolom `Interface`, pilih `bridge1` (bukan `ether2` atau `ether3`).  
   Contoh: <https://drive.google.com/open?id=1-oYiiIlNZICh5M2lOoOLsn8zvyH7u7of&usp=drive_fs>

7. **Buat DHCP Server** agar device bisa mendapat IP otomatis.  
   Pergi ke **“IP” → “DHCP Server”**, lalu klik **“DHCP Setup”**.  
   Jalankan setup di atas interface `bridge1`.  
   Contoh: <https://drive.google.com/open?id=10H3iKQoeNcsc9TUL1LDQuJfLjSXRbNQQ&usp=drive_fs>

8. **Uji DHCP di Device (VPCs):**  
   Jalankan perintah berikut:
   ```
   ip dhcp
   ```
   Hasilnya:  
   <https://drive.google.com/open?id=1vAtHnVSuHS_g-S5Jd6xRrcwEkws4Cj_Y&usp=drive_fs>  
   (VPC1 dan VPC2 akan mendapat IP dalam satu segmen, misalnya `192.168.10.254` dan `192.168.10.253`).

9. **Uji konektivitas antar device:**  
   Jalankan command `trace` dari VPC1 ke IP VPC2.  
   Hasil: <https://drive.google.com/open?id=17IAK8HWOvBRVu9rcQMTocSEMWHIzsevL&usp=drive_fs>

   Dari hasil trace terlihat bahwa koneksi langsung tanpa *next hop*! Artinya kedua device berada di jaringan Layer 2 yang sama—persis seperti tersambung ke switch. Mantap! 🎉  
   **LABS Bridge sederhana berhasil!**

---

## Ringkasan LABS

Intinya, konsep Bridge di MikroTik sangat mudah. Kita “menggabungkan” beberapa port fisik (seperti `ether2`, `ether3`) menjadi satu interface virtual (`bridge1`). Interface `bridge1` inilah yang bertindak sebagai “otak” atau “induk” jaringannya.

Pada LABS tadi kita sudah membuktikan:
- IP Address dan DHCP Server dipasang langsung di `bridge1` (bukan di `ether` satu per satu).
- Semua perangkat yang tersambung ke port anggota `bridge` mendapat IP dari DHCP yang sama dan berada di satu segmen jaringan.

### Poin Penting:

- **Konsep:** Bridge adalah interface virtual yang “mengikat” beberapa port fisik (`ether2`, `ether3`, `wlan1`) menjadi satu.  
- **Tujuan:** Mengubah port-port router (Layer 3) agar berfungsi seperti port switch (Layer 2).  
- **Langkah LABS:**
  1. Membuat `bridge` baru di menu **Bridge**.
  2. Menambahkan port fisik (`ether2`, `ether3`) ke dalam `bridge`.
  3. Memasang konfigurasi IP & DHCP Server langsung di `bridge1`.
- **Hasil:** Semua perangkat (VPCs) yang terhubung ke port anggota `bridge` akan berada di satu jaringan yang sama dan dapat berkomunikasi langsung seolah-olah tersambung ke satu switch fisik.
