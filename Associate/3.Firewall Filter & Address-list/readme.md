# Konsep Firewall Filter

Okeh, kita mulai dari konsep dahulu. Apa itu firewall filter di MikroTik Router? Firewall filter adalah sebuah mekanisme *filtering* (penyaringan) yang ada pada perangkat router MikroTik yang berguna untuk melakukan penyaringan terhadap alur *traffic* (lalu lintas data) tertentu.

Baik itu *traffic* yang menuju router, yang keluar dari router, ataupun yang hanya melewati router.

Secara ringkas, ada 3 jenis *chain* (alur) firewall filter yang digunakan pada router MikroTik, yakni:

1.  **input**
    = Jenis *chain* yang melakukan filtering berdasarkan koneksi atau *traffic* yang **menuju** ke router.
    Simpelnya: `src=laptop, dst=router` (Contoh: saat laptop ping ke IP router, atau akses Winbox ke router).

2.  **output**
    = Jenis *chain* yang melakukan filtering berdasarkan koneksi atau *traffic* yang **keluar** dari router.
    Simpelnya: `src=router, dst=laptop,internet,dll` (Contoh: saat router melakukan ping ke laptop, atau router mencari update firmware ke internet).

3.  **forward**
    = Jenis *chain* yang melakukan filtering berdasarkan koneksi atau *traffic* yang **melewati** router.
    Simpelnya: `src=laptop, dst=internet,youtube,dll` (Contoh: saat laptop browsing ke internet, atau laptop akses ke server di jaringan lain). Ini adalah *chain* yang paling sering digunakan untuk mengatur klien.

Dalam penerapannya sendiri, ada 2 strategi utama yang bisa kita gunakan, yakni:

* **Accept Few, Drop Any** (Terima Sedikit, Tolak Sisanya)
    = Strategi ini digunakan ketika kita ingin *mengizinkan* HANYA beberapa komputer untuk mengakses sebuah *traffic* atau jaringan tertentu, di mana komputer lainnya yang tidak termasuk akan langsung di-*drop* (ditolak).
    Contoh: Kita ingin membuat agar hanya 2 komputer (dari 100 komputer) yang bisa melakukan ping ke 8.8.8.8. Maka, kita akan membuat 2 *rule* dengan *action* `accept` untuk 2 PC tersebut, dan satu *rule* paling bawah dengan *action* `drop` untuk memblokir semua komputer lainnya.

* **Drop Few, Accept Any** (Tolak Sedikit, Terima Sisanya)
    = Strategi ini digunakan ketika kita ingin *membatasi* HANYA beberapa komputer untuk mengakses sebuah *traffic* atau jaringan tertentu, di mana komputer lainnya yang tidak termasuk akan tetap bisa mengakses jaringan.
    Contoh: Kita ingin membuat agar hanya 2 komputer (dari 100 komputer) yang diblok agar tidak bisa melakukan ping ke 8.8.8.8. Maka, kita akan membuat 2 *rule* dengan *action* `drop` untuk 2 PC tersebut, dan satu *rule* paling bawah dengan *action* `accept` untuk mengizinkan semua komputer lainnya.

*Next*, untuk lebih memahami mekanisme firewall filter, silakan coba jawab beberapa pertanyaan berikut:

1.  Bila ada lebih dari 3 PC yang disambungkan dalam 1 switch, yang mana kita ingin salah satu PC (dalam hal ini PC1) saja yang bisa melakukan ping ke router, *chain* apa yang akan kita gunakan?

    > **Answer:** `chain input` dengan strategi `accept few drop any`.
    >
    > **Alasan:** Karena *traffic* ping itu tujuannya adalah **router itu sendiri** (dari PC1 ke Router). Paket data "masuk" ke router, jadi kita filter di gerbang 'masuk' router, yaitu *chain input*. Strateginya `accept few drop any` karena kita hanya ingin *menerima* satu PC (PC1) dan *menolak* semua PC lainnya.

2.  Dalam sebuah topologi yang terdiri dari beberapa PC yang terhubung ke satu Switch (yang terhubung juga ke router) dan 1 Server yang terhubung ke router yang sama (di port berbeda), kita ingin membuat agar hanya beberapa komputer yang bisa mengakses server (dalam hal ini PC2 & 4). Pertanyaannya:
    * *Chain* dan strategi apa yang digunakan?

    > **Answer:** `chain forward` & strategi `Accept few drop any`.
    >
    > **Alasan:** Karena *traffic*-nya **melewati** router. Alurnya dari PC (misal di ether2) menuju Server (misal di ether3). Router cuma "meneruskan" paketnya, tidak "memiliki" paket itu. Jadi, kita pakai *chain forward*. Strateginya `accept few drop any` karena kita hanya ingin *menerima* PC2 & PC4, dan *menolak* PC lainnya yang mau akses ke Server.

3.  Dalam sebuah topologi yang terdiri dari beberapa PC yang terhubung ke satu Switch (yang terhubung juga ke router) dan 1 Server yang terhubung ke router yang sama, kita ingin membatasi beberapa komputer untuk terhubung ke internet (dalam hal ini PC1 & 3). Pertanyaannya:
    * *Chain* dan strategi apa yang digunakan?

    > **Answer:** `chain forward` & strategi `drop few accept any`.
    >
    > **Alasan:** Ini juga **melewati** router. Alurnya dari PC (ether2) menuju Internet (ether1/WAN). Jadi, kita tetap pakai *chain forward*. Strateginya `drop few accept any` karena kita hanya ingin *menolak* PC1 & PC3, tapi *mengizinkan* semua PC lainnya untuk tetap bisa ke internet (jika tidak ada *rule* `accept` di bawahnya, *default policy* biasanya `accept` jika tidak ada *rule drop* di akhir).

# cara mengkonfigurasi firewall filtering (LABS Virtual)

Di sini saya akan menggunakan PNETLAB dan menggunakan topologi sederhana berikut:
[Topologi PNETLAB](https://drive.google.com/open?id=1O8iCKSctsTXg7CeY8as9sJWmMRuiSPG0&usp=drive_fs)

Sebelumnya, pada router virtual pastikan sudah terhubung ke cloud internet via ether1, lalu konfigurasikan NAT-nya. Berikut cara melakukannya via Winbox:

* Masuk ke menu **IP --> Firewall --> NAT**.
* Klik tanda '+', lalu ikuti arahan sesuai pada gambar:
    [Gambar 1 - NAT](https://drive.google.com/open?id=1S3WLiC6WNj-FZdFW7DeXoBZ9EdRrZotH&usp=drive_fs)
    [Gambar 2 - NAT](https://drive.google.com/open?id=159PgT5Yx_dpxznl_QC3iwRUhpne3VeIE&usp=drive_fs)

*Next*, kita akan mengkonfigurasikan firewall filternya. Masih di menu **Firewall**, pergi ke menu **Filter Rules** lalu klik '+'. *Next*, ikuti arahan seperti pada gambar:

[Gambar 1 - Filter Rules](https://drive.google.com/open?id=1VmxVvOzormUAKVEEn4_L63xy0pT7py-_&usp=drive_fs)
[Gambar 2 - Filter Rules](https://drive.google.com/open?id=1ESIJZGi9DH_o0gGMVhQ1bbzs5IMPTpsQ&usp=drive_fs)
[Gambar 3 - Filter Rules](https://drive.google.com/open?id=1D88_N8qpWhF2COwjviOXSy3sIuxLveoP&usp=drive_fs)
[Gambar 4 - Filter Rules](https://drive.google.com/open?id=1_1KYnr30osRjlfChcwO08a1unjjJGSXj&usp=drive_fs)

Selanjutnya, kita akan melakukan pengujian. Pada device laptop, kita akan mencoba pergi ke 'youtube.com' dan lihat apa yang terjadi.

[Gambar 1 - Pengujian](https://drive.google.com/open?id=1khw3wbfixFoexH_mZrQjk0_kMVOTok4X&usp=drive_fs)

Bisa dilihat di sini bahwa laptop tidak berhasil konek ke YouTube. Ini berarti *filter rules* kita berhasil diterapkan. Untuk lebih memastikan, mari lihat keadaan *filter rules* kita di Winbox:

[Gambar 1 - Cek Statistik Rule](https://drive.google.com/open?id=1LwLgfl6Jeqq_rYIg4BNyRoGytMOGB4SR&usp=drive_fs)

Perhatikan bagian 'Bytes' dan 'Packets', itu terus bertambah yang mengartikan bahwa paket yang mengarah ke YouTube dari device berhasil dideteksi dan diblok oleh router.

*Nah*, cara ini bisa kita gunakan juga untuk memblokir platform lain seperti Facebook, dan lainnya. Namun, cara sederhana ini (memakai filter *content*) hanya berhasil diterapkan pada lingkungan virtual. Untuk *real device* (perangkat asli), kita harus menggunakan konfigurasi yang lebih canggih dan kompleks lagi (seperti Layer 7 Protocol atau filter DNS), dikarenakan sistem di *real device* (terutama HTTPS) lebih sulit untuk dideteksi oleh *rule* firewall sederhana.

*Next*, sebelum lanjut, beberapa hal kecil yang baiknya kita lakukan ketika melakukan konfigurasi yakni:

* **Memastikan zona waktu sudah sesuai.** Caranya dengan pergi ke menu **System --> Clock**, lalu pilih 'Time Zone Name' yang sesuai. Bila di Indonesia, pilih 'Asia/Jakarta'. Nanti *timezone* nya akan langsung berubah dan mengikuti jam sebenarnya saat perangkat dikonfigurasikan.

* **Bila kita ingin melakukan modifikasi lebih lanjut,** seperti ingin agar *rule* aktif di jam tertentu, kita bisa melakukannya dengan mengedit *rule* di bagian **Filter Rules** --> (pilih *rule*-nya) --> tab **Extra** --> **Time**. Di sini kita bisa bebas memilih hari dan jamnya mulai dari kapan saja. Silakan bereksperimen!
    [Gambar 1 - Konfigurasi Waktu](https://drive.google.com/open?id=1Cr5VNHZhp6KA6sslwjvZAZQdQe2ruu90&usp=drive_fs)

## Tambahan (Firewall Address List)

Okeh, *next* ada fitur namanya `Address List`. Ini penting banget.

Simpelnya, **Address List** adalah fitur di Firewall MikroTik untuk *mengelompokkan* beberapa alamat IP ke dalam satu 'label' atau 'nama'.

Kenapa ini berguna? Bayangin kalo kita mau ngeblok 10 PC kayak di contoh tadi. Ribet kan bikin 10 *rule* 'drop' satu per satu?

Nah, dengan *Address List*, kita bisa:

1.  Bikin satu *list* baru di menu **IP --> Firewall --> Address Lists**.
2.  Misalnya, kita beri nama 'PC\_DIBLOK'.
3.  Masukkan 10 alamat IP PC tadi ke dalam *list* 'PC\_DIBLOK' tersebut.
4.  Di **Filter Rules**, kita CUKUP bikin **SATU RULE** saja:
    * `chain=forward`
    * Di tab **Advanced**, pilih `src-address-list=PC_DIBLOK`
    * Di tab **Action**, pilih `action=drop`.

Jauh lebih simpel, lebih rapi, dan lebih gampang di-manage! *Address list* ini bisa diisi IP manual, atau bahkan bisa diisi secara dinamis (misalnya, IP yang mencoba *scanning port* akan otomatis masuk *list* blokir).

## Ringkasan-LABS & materi

Okeh, jadi kita udah belajar lumayan banyak. Mari kita ringkas poin-poin pentingnya:

Firewall Filter di MikroTik adalah penjaga gerbang *traffic* data. Kuncinya ada di 3 *chain* (alur) yang harus dipahami:

* **Input:** *Traffic* yang tujuannya **ke router** (Contoh: ping ke IP router, akses Winbox/Webfig).
* **Output:** *Traffic* yang asalnya **dari router** (Contoh: router ping ke internet, router cari *update*).
* **Forward:** *Traffic* yang cuma **melewati router** (Contoh: PC klien akses ke internet, PC klien akses ke server di *network* lain). Ini yang paling sering kita pakai untuk mengatur klien.

Dalam praktiknya (LABS), kita belajar:

* Untuk memblokir klien ke internet (seperti ke YouTube), kita menggunakan *chain* `forward` karena *traffic*-nya *melewati* router (dari LAN ke WAN).
* Kita bisa membuat *rule* berdasarkan banyak hal: IP (src/dst), port, protokol.
* Di tab 'Advanced' atau 'Extra', kita bisa memfilter berdasarkan konten (seperti 'youtube.com' di *field* `content`) atau bahkan berdasarkan waktu/jam tertentu.
* Jangan lupa, sebelum klien bisa internetan, NAT (*Masquerade*) harus dikonfigurasikan dulu di `IP --> Firewall --> NAT`.
* Untuk manajemen *rule* yang lebih gampang, terutama kalau IP-nya banyak, sangat disarankan pakai `Address List` (dijelaskan di bagian tambahan).

Intinya, pahami dulu *alur traffic*-nya mau ke mana, baru tentukan *chain* (input, output, atau forward) yang tepat.