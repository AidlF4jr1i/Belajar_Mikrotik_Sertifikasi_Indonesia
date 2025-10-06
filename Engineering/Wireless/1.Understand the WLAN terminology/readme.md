Apa itu Wireless?
Wireless adalah kemampuan yang dimiliki oleh suatu router untuk mengizinkan komunikasi melalui sinyal radio yang bervariasi. Khusus di MikroTik RouterOS, beberapa Standar Wireless yang didukung yakni "IEEE 802.11a", "IEEE 802.11b", "IEEE 802.11g", "IEEE 802.11n", dan "IEEE 802.11ac" (update terbaru).
Standar Wireless
Berikut adalah rincian deskripsi dari standar wireless yang didukung:
IEEE 802.11b: Bekerja pada frekuensi 2.4GHz dengan kecepatan hingga 11Mbps.
IEEE 802.11g: Bekerja pada frekuensi 2.4GHz dengan kecepatan hingga 54Mbps.
IEEE 802.11a: Bekerja pada frekuensi 5GHz dengan kecepatan hingga 54Mbps.
IEEE 802.11n: Bekerja pada frekuensi 2.4GHz dan 5GHz dengan kecepatan 150Mbps (dapat mencapai 300Mbps).
IEEE 802.11ac: Bekerja pada frekuensi 5GHz dengan kecepatan hingga 1Gbps.

Frekuensi
Tergantung pada negara tempat Anda tinggal, macam frekuensi yang diizinkan akan berbeda pula. Contohnya, di Belanda mengizinkan penggunaan frekuensi dari channel 1-13, sedangkan di Amerika hanya mengizinkan penggunaan frekuensi dari channel 1-11. Masing-masing jenis frekuensi ini akan menghasilkan gelombang sinyal sepanjang 22 MegaHertz (MHz).
Untuk detail nama setiap frekuensi, silakan dilihat pada gambar di tautan ini: Detail Frekuensi Channel(https://drive.google.com/open?id=1AH7t6bgWXSkVI0db_D_MhO8KQnNKKQO-&usp=drive_fs)
Catatan: Bisa dilihat bahwa Channel 1, 6, dan 11 tidak saling bertabrakan (overlapping) dengan channel lain. Jika Anda memiliki 3 access point dan masing-masing menggunakan channel 1, 6, dan 11, maka koneksi wireless akan berjalan tanpa ada masalah sama sekali.

Frekuensi yang Didukung di MikroTik RouterOS
Berikut adalah frekuensi yang didukung oleh MikroTik RouterOS:
2.4GHz: Rentang frekuensi dari 2312 MHz hingga 2499 MHz.
5GHz: Rentang frekuensi dari 4920 MHz hingga 6100 MHz.

# Frequency usage, Spectral Scan, Spectral History
Topology1:https://drive.google.com/open?id=1Mdrd5o5sL9NV9lA4137-iym4q8DkJZky&usp=drive_fs

Lab Scenario:
1.  Hubungkan ke perangkat Router (R1) menggunakan Winbox, lalu masuk ke menu *Frequency Usage* pada antarmuka WLAN1. Amati, frekuensi manakah yang paling sedikit digunakan?
2.  Pilih rentang frekuensi yang ingin Anda pindai, lalu gunakan kembali fitur *Frequency Usage*. Apa yang Anda amati atau lihat dari hasil pemindaian tersebut?
3.  Buka terminal pada Winbox dan jalankan perintah berikut:
    ```
    interface wireless spectral-scan
    ```
    Dari hasil yang muncul, frekuensi manakah yang paling sedikit digunakan?
4.  Masih di terminal yang sama, jalankan perintah berikut:
    ```
    interface wireless spectral-history
    ```
    Dari hasil yang muncul, frekuensi manakah yang paling sedikit digunakan? 

hasil Konfig:

1.Pergi ke R1(Access Point) lalu --> Wireless --> wlan1 --> double klik lalu pilih (Frequency usage) untuk mengecek frequensi yang paling sedikit noise nya
gambar: "https://drive.google.com/open?id=1wUhUaKWoUQ1BNlZT-ZRh8F8WFDsgVlPW&usp=drive_fs", disini saya akan mengambil channel 2422 yang memiliki noise paling kecil setelah 2417

2. selanjutnya kita akan mengatur scan list untuk melihat ke-2 Channel yang memiliki noise paling kecil tersebut dan mengabaikan channel lainnya, pergi ke wlan1 lalu double klik --> klik Advanced mode --> lalu masukkan 2 channel yang akan kamu pantau 2417-2422, lalu kembali klik Frequency usage, Gambar: "https://drive.google.com/open?id=1ktsDYUupDFdrXqUjjIUV4_e766lKOl3m&usp=drive_fs"

3. kita bisa mengecek Wireless dengan command "interface wireless spectrall scan", Gambar: "https://drive.google.com/open?id=1b_OMTQtS4SRJRNiPEQjZCbCb_O7cjhfh&usp=drive_fs"

4. kita juga bisa mengecek nya dengan command "interface wireless spectral-history", Gambar: "https://drive.google.com/open?id=1z66SiQwJlNH0ljjaJ8SjOo5hOEOtGIKP&usp=drive_fs", note : yang paling kiri(biru hitam), itu berarti Noise nya kecil, sementara yang paling kanan(merah) Noise nya yang paling besar, untuk kondisi sinyal yg bagus pilih yang biru hitam

# Mikrotik Wireless Aligment Tool
MikroTik Wireless Alignment Tools adalah fitur di RouterOS yang digunakan untuk membantu menyelaraskan (align) antena pada perangkat wireless. Fitur ini berguna terutama untuk link Point-to-Point (PtP) atau Point-to-Multipoint (PtMP) agar mendapatkan sinyal terbaik.Fitur ini sangat berguna saat melakukan instalasi perangkat wireless outdoor seperti SXT, LHG, atau Dish untuk memastikan koneksi stabil dan kuat.

Cara setting :
1. Maish menggunakan topology sebelumnya, kita akan langsung menuju pada R1, dibagian Interface wireless pilih mode "Alignment Only", pastikan "wlan1" menyala, lalu pilih channel frequency yang diinginkan, setelah itu pada R2 lakukan hal yang sama
2. pada R2 pergi ke menu "alignment" lalu pilih wireless Alignment Settings di bagian filter mac address, masukkan mac address dari R1 
Gambar:"https://drive.google.com/open?id=1nv5ofZZoDsPFZoZvBWbh5PT7nCpVKHPV&usp=drive_fs",
klik apply lalu tinggal pencet start dan amati bagian "Correct percentage", bila 100% maka koneksi dan jarak antara R1 dan R2 sudah sangat ideal dan memberikan kekuatan sinyal yang besar

# Wireless Snooper and Sniffer Tool

A. Snooper 
Snooper adalah Tools yang ada pada wireless yang berfungsi untuk melakukan Scanning terhadap jaringan yang ada di sekitar kita, output yang diberikan juga sangat detail mulai dari informasi tentang SSID, MAC Address, Channel, dan Mode dari jaringan yang terdeteksi.
cara menggunakan :
1. Pergi ke menu wireless
2. klik 2x pada wlan1 lalu pilih Snooper
3. Klik start
Gambar: "https://drive.google.com/open?id=1wNe9dXr-1HGq2wP3utQwv5oQbVi-xNrq&usp=drive_fs"

B. Sniff
Wireless Sniffer adalah alat untuk menangkap Frame paket data(layer 2 Data link) yang lewat di jaringan Wi-Fi. tools ini sangat berguna untuk troubleshooting mendalam atau analisis keamanan jaringan. cara pakai:
1.setelah klik 2x pada wlan1 cukup pilih "Sniff"
2. klik start
Gambar: "https://drive.google.com/open?id=1pVeK8iPDVs6NIeA-Ex37OE01YdR800ff&usp=drive_fs"

kamu bisa menyimpan hasil scan nya dengan klik menu "save" dan bisa kamu analisis menggunakan wireshark

# Wireless Scan Tool and Reset Configuration
A. Scan Tool
fitur ini berfungsi agar Router(wireless mode: Station) bisa terhubung ke Router lain yang bertindak sebagai Access Point(wireless Mode: AP Bridge), yang mana bertujuan agar bisa berbagi Internet yang sama, cara memakai nya cukup mudah cukup pergi ke interface wireless dan klik "Scanner", lalu pilih wlan1 dan klik start

Gambar:"https://drive.google.com/open?id=1dXTcUnRCgnbhHNO9O14YJ791c2r8FDW1&usp=drive_fs"

B. Reset Configuration wireless
menu ini berfungsi untuk mereset ulang semua konfigurasi wireless yang kita buat dan mengembalikannya ke default, cara pakainya cukup klik 2x pada 'wlan1' dan pilih reset configuration
Gambar:"https://drive.google.com/open?id=1VtIPjbc33yaSzJkqWsot7Dkgh_3L5cWV&usp=drive_fs"


# Wireless frequency Mode and TX power change
di Mikrotik ada 3 frequency mode yang tersedia yakni:

1. regulatory domain
pada mode ini ada pembatasan mengenai jenis frekuensi yang bisa digunakan based on negara tempat kamu berada sekarang, di Indonesia misalnya ada sebanyak 13 macam frekuensi yang bisa dipakai, selain itu mode ini juga memiliki pembatasan terkait txpower yang bisa dipakai
2. manual-txpower
Pada mode ini pembatasan yang dilakukan berupa pembatasan jenis frekuensi Standard saja yang bisa dipakai, FYI Standard frekuensi yakni(dari 2417 - 2462)
Gambar: "https://drive.google.com/open?id=1LwGoHheZWghcuk7ngro5wrpQZdZcUt5u&usp=drive_fs"
, plus nya di mode ini kita bisa mensetting txpower sebesar yang kita inginkan
3. superchannel
Pada mode ini tidak ada pembatasan terkait Channel frekuensi(seluruh channel baik yang standard maupun non-standard semua nya muncul) yang bs digunakan dan juga disini kita bisa mengatur seberapa besar txpower yang kita inginkan sesuka kita

Note: "untuk penggunaan superchannel itu juga masih memperhatikan regulasi yang ada bila diizinkan maka kita bisa menggunakan superchannel secara bebas"

Changing the TX power for a better signal reception
Command:
R1:"
/interface wireless
set [ find default-name=wlan1 ] band=2ghz-b/g/n country=indonesia disabled=no \
    frequency=2417 installation=indoor mode=ap-bridge radio-name="Router Ajie" \
    ssid=HOM123 tx-power=0 tx-power-mode=all-rates-fixed
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
add authentication-types=wpa2-psk group-ciphers=tkip,aes-ccm mode=dynamic-keys \
    name=HOM123 supplicant-identity="" unicast-ciphers=tkip,aes-ccm \
    wpa2-pre-shared-key=PALEMBANG_2005
",
R2: "
/interface wireless
set [ find default-name=wlan1 ] band=2ghz-b/g/n country=no_country_set \
    disabled=no frequency=2417 frequency-mode=manual-txpower installation=\
    indoor radio-name=R2 ssid=HOM123 tx-power=0 tx-power-mode=all-rates-fixed
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik" 

<Masukkan penjelasan mengenai hasil konfigurasi yang dijalankan diatas untuk mengubah TX Power disini!>


# Ringkasan Materi dan LABS

<Masukkan penjelasan mengenai Ringkasan dari hasil konfigurasi yang dijalankan diatas  disini!>