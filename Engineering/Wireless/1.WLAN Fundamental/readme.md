# Apa itu Wireless

Wireless adalah kemampuan yang dimiliki oleh suatu router untuk mengizinkan komunikasi melalui sinyal radio yang bervariasi. 
Khusus di MikroTik RouterOS, beberapa Standar Wireless yang didukung yakni:
- **IEEE 802.11a**
- **IEEE 802.11b**
- **IEEE 802.11g**
- **IEEE 802.11n**
- **IEEE 802.11ac** (update terbaru)

---

## Standar Wireless

| Standar | Frekuensi | Kecepatan |
|----------|------------|------------|
| IEEE 802.11b | 2.4GHz | hingga 11Mbps |
| IEEE 802.11g | 2.4GHz | hingga 54Mbps |
| IEEE 802.11a | 5GHz | hingga 54Mbps |
| IEEE 802.11n | 2.4GHz dan 5GHz | hingga 150Mbps (dapat mencapai 300Mbps) |
| IEEE 802.11ac | 5GHz | hingga 1Gbps |

---

## Frekuensi

Tergantung pada negara tempat Anda tinggal, macam frekuensi yang diizinkan akan berbeda pula.  
Contohnya, di **Belanda** mengizinkan penggunaan frekuensi dari channel **1-13**, sedangkan di **Amerika** hanya mengizinkan penggunaan channel **1-11**.  
Masing-masing jenis frekuensi ini akan menghasilkan gelombang sinyal sepanjang **22 MHz**.

📎 **Detail Frekuensi Channel:** [Link GDrive](https://drive.google.com/open?id=1AH7t6bgWXSkVI0db_D_MhO8KQnNKKQO-&usp=drive_fs)

> Catatan: Channel **1**, **6**, dan **11** tidak saling bertabrakan (overlapping).  
> Jika Anda memiliki 3 access point dengan masing-masing channel 1, 6, dan 11, koneksi wireless akan berjalan optimal.

---

## Frekuensi yang Didukung di MikroTik RouterOS

- **2.4GHz**: 2312 MHz – 2499 MHz  
- **5GHz**: 4920 MHz – 6100 MHz

📎 **Topology1:** [Link GDrive](https://drive.google.com/open?id=1Mdrd5o5sL9NV9lA4137-iym4q8DkJZky&usp=drive_fs)

---

## Lab Scenario

1. Hubungkan ke perangkat Router (R1) menggunakan **Winbox**, lalu masuk ke menu **Frequency Usage** pada antarmuka *WLAN1*.  
   Amati frekuensi yang paling sedikit digunakan.
2. Pilih rentang frekuensi yang ingin dipindai dan gunakan kembali fitur **Frequency Usage**.  
   Catat hasilnya.
3. Jalankan perintah berikut di terminal:
   ```bash
   interface wireless spectral-scan
   ```
   Catat frekuensi yang paling sedikit digunakan.
4. Jalankan perintah berikut:
   ```bash
   interface wireless spectral-history
   ```
   Catat frekuensi yang paling sedikit digunakan.

---

## Hasil Konfigurasi

1. Pergi ke R1 (Access Point) → **Wireless → wlan1 → Frequency usage**  
   Cek frekuensi dengan noise paling kecil.  
   📎 [Gambar](https://drive.google.com/open?id=1wUhUaKWoUQ1BNlZT-ZRh8F8WFDsgVlPW&usp=drive_fs)  
   → Pilih channel **2422 MHz** (noise kecil setelah 2417 MHz).

2. Atur **scan list** untuk dua channel dengan noise kecil (2417–2422).  
   📎 [Gambar](https://drive.google.com/open?id=1ktsDYUupDFdrXqUjjIUV4_e766lKOl3m&usp=drive_fs)

3. Cek Wireless dengan perintah:
   ```bash
   interface wireless spectral-scan
   ```
   📎 [Gambar](https://drive.google.com/open?id=1b_OMTQtS4SRJRNiPEQjZCbCb_O7cjhfh&usp=drive_fs)

4. Cek juga dengan:
   ```bash
   interface wireless spectral-history
   ```
   📎 [Gambar](https://drive.google.com/open?id=1z66SiQwJlNH0ljjaJ8SjOo5hOEOtGIKP&usp=drive_fs)

> Note: Warna **biru-hitam** menunjukkan noise kecil, sedangkan **merah** menunjukkan noise besar.  
> Pilih sinyal yang berwarna biru-hitam untuk kondisi terbaik.

---

## Mikrotik Wireless Alignment Tool

**MikroTik Wireless Alignment Tool** membantu menyelaraskan antena pada perangkat wireless, khususnya untuk koneksi **PtP** atau **PtMP**.

### Cara Setting:
1. Pada R1, di **Interface Wireless**, pilih mode **Alignment Only**.  
   Pastikan *wlan1* aktif dan pilih channel frequency yang diinginkan.
2. Pada R2, buka menu **Alignment**, pilih **Wireless Alignment Settings**, dan masukkan **MAC Address R1**.  
   📎 [Gambar](https://drive.google.com/open?id=1nv5ofZZoDsPFZoZvBWbh5PT7nCpVKHPV&usp=drive_fs)

> Klik *Start* dan amati *Correct Percentage*. Jika mencapai 100%, koneksi dan jarak ideal.

---

## Wireless Snooper and Sniffer Tool

### A. Snooper
Snooper berfungsi melakukan scanning terhadap jaringan di sekitar, memberikan informasi detail seperti SSID, MAC Address, Channel, dan Mode.

**Cara menggunakan:**
1. Pergi ke menu **Wireless**.  
2. Klik dua kali pada *wlan1*, pilih **Snooper**, lalu klik **Start**.  
   📎 [Gambar](https://drive.google.com/open?id=1wNe9dXr-1HGq2wP3utQwv5oQbVi-xNrq&usp=drive_fs)

### B. Sniffer
Sniffer menangkap frame paket data (Layer 2) di jaringan Wi-Fi untuk troubleshooting dan analisis keamanan.

**Cara menggunakan:**
1. Klik dua kali pada *wlan1*, pilih **Sniff**, lalu klik **Start**.  
   📎 [Gambar](https://drive.google.com/open?id=1pVeK8iPDVs6NIeA-Ex37OE01YdR800ff&usp=drive_fs)

> Hasil scan dapat disimpan (*Save*) dan dianalisis menggunakan **Wireshark**.

---

## Wireless Scan Tool and Reset Configuration

### A. Scan Tool
Fitur ini digunakan agar router dengan mode **Station** dapat terhubung ke router **Access Point (AP Bridge)**.

**Cara menggunakan:**
1. Pergi ke **Interface Wireless → Scanner → wlan1 → Start**  
   📎 [Gambar](https://drive.google.com/open?id=1dXTcUnRCgnbhHNO9O14YJ791c2r8FDW1&usp=drive_fs)

### B. Reset Configuration Wireless
Fitur ini mereset semua konfigurasi wireless ke default.  
**Cara menggunakan:** klik dua kali pada *wlan1*, lalu pilih **Reset Configuration**.  
📎 [Gambar](https://drive.google.com/open?id=1VtIPjbc33yaSzJkqWsot7Dkgh_3L5cWV&usp=drive_fs)

---

## Wireless Frequency Mode and TX Power Change

Terdapat **3 mode frequency** di Mikrotik:

1. **Regulatory Domain**  
   - Pembatasan jenis frekuensi sesuai regulasi negara.  
   - Misal di Indonesia tersedia 13 channel.  
   - Ada batasan *txpower* maksimum.

2. **Manual-TXPower**  
   - Pembatasan hanya pada frekuensi standar (2417–2462 MHz).  
   - Kelebihan: dapat mengatur *txpower* secara bebas.  
   📎 [Gambar](https://drive.google.com/open?id=1LwGoHheZWghcuk7ngro5wrpQZdZcUt5u&usp=drive_fs)

3. **Superchannel**  
   - Semua channel (standar dan non-standar) tersedia.  
   - *TX Power* dapat diatur sesuka hati.

> Note: Penggunaan **Superchannel** tetap harus sesuai regulasi negara.

---

## Mengubah TX Power untuk Koneksi Lebih Baik

**R1 Configuration:**
```bash
/interface wireless
set [ find default-name=wlan1 ] band=2ghz-b/g/n country=indonesia disabled=no     frequency=2417 installation=indoor mode=ap-bridge radio-name="Router Ajie"     ssid=HOM123 tx-power=0 tx-power-mode=all-rates-fixed
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
add authentication-types=wpa2-psk group-ciphers=tkip,aes-ccm mode=dynamic-keys     name=HOM123 supplicant-identity="" unicast-ciphers=tkip,aes-ccm     wpa2-pre-shared-key=PALEMBANG_2005
```

**R2 Configuration:**
```bash
/interface wireless
set [ find default-name=wlan1 ] band=2ghz-b/g/n country=no_country_set     disabled=no frequency=2417 frequency-mode=manual-txpower installation=    indoor radio-name=R2 ssid=HOM123 tx-power=0 tx-power-mode=all-rates-fixed
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
```

Konfigurasi Diatas bertujuan untuk mengatur daya pancar (Transmit Power) pada kedua router (R1 dan R2) ke nilai spesifik yang tetap. Dengan menggunakan tx-power-mode=all-rates-fixed, daya pancar yang ditetapkan akan berlaku secara seragam untuk semua kecepatan data. Nilai tx-power=0 mengatur daya pancar ke 0 dBm, yang setara dengan 1 milliwatt (mW). Ini adalah tingkat daya yang sangat rendah. Akibatnya, sinyal nirkabel yang dipancarkan oleh kedua perangkat akan menjadi sangat lemah, sehingga jangkauan koneksi menjadi sangat pendek. Koneksi nirkabel antara R1 dan R2 kemungkinan besar akan tidak stabil atau bahkan gagal terhubung kecuali kedua perangkat diletakkan dalam jarak yang sangat berdekatan. Selain itu, perlu dicatat bahwa R2 tidak akan berhasil terhubung ke R1 karena konfigurasi profil keamanannya tidak cocok; R2 masih menggunakan profil default tanpa wpa2-pre-shared-key yang sesuai dengan yang diatur pada R1.

---

## Ringkasan Materi dan LABS


1. **Dasar Wireless:** Pahami standar (802.11b/g/n/ac) dan pemilihan frekuensi yang tepat.  
2. **Analisis adalah Kunci:** Gunakan Frequency Usage, Spectral Scan, dan Spectral History.  
3. **Alat Bantu Instalasi:** Alignment Tool wajib untuk PtP/PtMP.  
4. **Troubleshooting:** Gunakan Snooper dan Sniffer untuk diagnosa.  
5. **Fleksibilitas:** Kuasai mode regulatory-domain, manual-txpower, dan superchannel.

---

© WLAN Terminology — MikroTik RouterOS Guide