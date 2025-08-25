# Pengenalan Networking & Subnetting

Simpelnya, **networking** adalah cara agar minimal dua komputer atau lebih bisa saling terhubung dan berkomunikasi 💻. Ibaratnya, kalau mau kirim surat, setiap rumah butuh alamat unik agar surat tidak salah tujuan 📬. Nah, di dunia jaringan, "alamat" unik ini disebut **IP Address**. IP Address menjadi jembatan utama agar data yang kita kirim bisa sampai ke perangkat yang tepat.

Lalu, apa itu **subnetting**? Subnetting adalah teknik untuk memecah satu blok IP yang besar menjadi beberapa blok yang lebih kecil dan efisien. Tujuannya adalah untuk memaksimalkan penggunaan IP, meningkatkan keamanan, dan membuat pengelolaan jaringan menjadi lebih rapi.

Kenapa ini penting? Bayangkan dalam satu ruangan hanya ada 5 komputer. Jika kita langsung menggunakan prefix `/24`, kita akan mendapatkan total 256 alamat IP (254 yang bisa dipakai). Untuk 5 komputer, sisa 249 alamat IP jadi terbuang sia-sia, kan? Padahal, stok alamat IPv4 di dunia ini sudah sangat langka.

Nah, di sinilah subnetting berperan! Dengan menggunakan prefix yang lebih pas seperti `/29` (menyediakan 6 IP yang bisa dipakai), penggunaan alamat menjadi jauh lebih efisien.

---

### # Studi Kasus Subnetting: `192.168.10.1/25`

Berikut adalah hasil analisis lengkap untuk IP address tersebut:

| Kategori | Perhitungan/Hasil | Keterangan |
| :--- | :--- | :--- |
| **Total IP** | `2^(32-25)` = `2^7` = **128** | Jumlah total alamat dalam subnet ini. |
| **Subnet Mask** | `255.255.255.128` | Representasi desimal dari prefix `/25`. |
| **Blok Subnet** | Blok 1: `0-127`, Blok 2: `128-255` | IP `192.168.10.1` berada di **Blok 1**. |
| **IP Network** | `192.168.10.0` | Alamat **pertama** dalam blok, berfungsi sebagai identitas jaringan. |
| **IP Broadcast** | `192.168.10.127` | Alamat **terakhir** dalam blok, berfungsi untuk siaran ke semua perangkat. |
| **IP Range** | `192.168.10.1 - 192.168.10.126` | Rentang IP yang **valid** untuk digunakan oleh perangkat. |

---

### # IP Public vs. IP Private: Alamat Global vs. Lokal

Untuk bisa terhubung ke internet, dunia jaringan membagi IP Address menjadi dua jenis utama. Mari kita gunakan analogi sebuah komplek perumahan.

#### **IP Private (Alamat Internal)**
Ini adalah IP yang digunakan **di dalam jaringan lokal** (seperti di rumah atau kantor Anda) dan tidak bisa diakses langsung dari internet.

* **Analogi:** Anggap IP Private sebagai **nomor rumah** di dalam sebuah komplek perumahan (`No. 1`, `No. 2`, dst.). Nomor ini hanya unik dan relevan di dalam komplek tersebut.
* **Contoh:** Laptop Anda mungkin punya IP `192.168.1.10`, dan HP Anda `192.168.1.11`.
* **Range IP Private:**
    * `10.0.0.0` - `10.255.255.255` (Kelas A)
    * `172.16.0.0` - `172.31.255.255` (Kelas B)
    * `192.168.0.0` - `192.168.255.255` (Kelas C)

#### **IP Public (Alamat Global)**
Ini adalah IP yang diberikan oleh Internet Service Provider (ISP) dan dapat **diakses secara global melalui internet**. Setiap IP Public bersifat unik di seluruh dunia.

* **Analogi:** IP Public adalah **alamat komplek perumahan** itu sendiri di dunia nyata (misal: "Komplek Griya Indah, Jalan Merdeka No. 45, Palembang"). Inilah alamat yang dikenali oleh 'kantor pos' (internet).
* **Fungsi:** Router di rumah/kantor Anda menggunakan satu IP Public ini untuk berkomunikasi dengan internet atas nama semua perangkat di jaringan lokal Anda melalui proses yang disebut **NAT (Network Address Translation)**.



---

### # OSI Layer: Fondasi Komunikasi Jaringan

OSI Layer adalah sebuah model konseptual yang membagi proses komunikasi jaringan menjadi 7 lapisan. Sebagai seorang *network engineer*, memahami fungsi perangkat di 3 lapisan bawah ini sangat krusial.

#### **Layer 1: Physical Layer 🔌**
Lapisan ini berurusan dengan media transmisi fisik. Fokus utamanya adalah mengirimkan data dalam bentuk bit mentah (1 dan 0) melalui sinyal listrik, cahaya, atau gelombang radio.
* **Kata Kunci:** Kabel, Sinyal, Bit.
* **Contoh Perangkat:** **Hub**.
* **Cara Kerja Hub:** Sebuah Hub adalah perangkat "bodoh". Ketika menerima sinyal data di satu port, ia hanya akan menyalin dan mengirimkan sinyal tersebut ke **semua port lain** yang terhubung, tanpa peduli siapa tujuannya.

#### **Layer 2: Data Link Layer ↔️**
Lapisan ini bertugas mengemas bit dari Layer 1 menjadi format yang disebut **Frame**. Di sinilah konsep alamat fisik atau **MAC Address** digunakan untuk pengiriman data yang lebih cerdas di dalam satu jaringan lokal.
* **Kata Kunci:** MAC Address, Frame, Switch.
* **Contoh Perangkat:** **Switch**.
* **Cara Kerja Switch (Perbedaan Kunci dengan Hub):** Switch jauh lebih cerdas. Ia menyimpan tabel MAC Address dari setiap perangkat yang terhubung. Saat menerima data, Switch akan melihat MAC Address tujuan dan meneruskan data tersebut **hanya ke port spesifik yang dituju**. Ini membuat komunikasi jauh lebih efisien dan tidak "berisik" dibandingkan Hub.

#### **Layer 3: Network Layer 🌐**
Lapisan ini bertanggung jawab untuk pengalamatan logis menggunakan **IP Address** dan menentukan rute terbaik untuk mengirimkan data **antar jaringan (subnet) yang berbeda**. Data di lapisan ini disebut **Packet**.
* **Kata Kunci:** IP Address, Packet, Router.
* **Contoh Perangkat:** **Router**.
* **Cara Kerja Router:** Fungsi utama Router adalah menghubungkan dua atau lebih jaringan yang berbeda (misalnya, jaringan lokal Anda dengan jaringan internet). Ia membuat keputusan berdasarkan IP Address tujuan untuk meneruskan paket ke jaringan yang benar.