## Konsep Bridge

Bridge itu intinya adalah sebuah mekanisme di MikroTik dimana kita membuat satu interface *virtual* (logika) yang mewakili beberapa interface fisik (misal `ether2`, `ether3`, `wlan1`) sekaligus. Gampangnya, kita 'menggabungkan' port-port fisik itu biar jadi satu.

Tujuannya apa? Biar port-port router yang aslinya bekerja di Layer 3 (routing) bisa berfungsi persis kayak *Switch* (Layer 2).

Ini berguna banget kalau router kita port-nya banyak tapi kita butuh fungsionalitas switch tambahan. Jadi, port-port router itu kita alih fungsikan sebagai alternatif switch.

Penggunaan bridge yang umum biasanya ada di rumah, dimana kita cuma perlu satu konfigurasi IP aja untuk semua port (yang terhubung ke TV, PC, Access Point, dll) biar gampang buat internetan dan kebutuhan simpel lainnya.

## LABS Bridge

Topology Bridge: <https://drive.google.com/open?id=1_EmkIs9_lacQ04zO4Rw072cQBKASTQlr&usp=drive_fs>

Pada Lab ini kita akan mengkonfigurasikan Bridge secara sederhana di router mikrotik. Caranya gampang!

1.  Cukup masuk ke Winbox melalui IP address DHCP yang didapat dari Cloud Nat (Contoh: Login Winbox via IP DHCP dari NAT: <https://drive.google.com/open?id=1_uG3neDCbTlKBJqWBSqzkFu1m3-CST4X&usp=drive_fs>).

2.  Nah, di Winbox pergi ke menu **'Bridge'**.

3.  Di tab **'Bridge'** (Contoh: Menu Bridge di Winbox: <https://drive.google.com/open?id=1rvG8cCcPRMzjdwlJg0qAGJkGlnNc6F1j&usp=drive_fs>), klik tombol **"+"** untuk menambahkan bridge baru. Kamu bisa memberikan nama sesuai yang kamu inginkan atau menggunakan nama default (`bridge1`) yang langsung terbuat (Contoh: Menambahkan bridge baru 'bridge1': <https://drive.google.com/open?id=1mKUSUzqRCH8RYdj6fQXEBM3eNPQYAleq&usp=drive_fs>).

4.  Next, pergi ke tab di sampingnya yakni **'Ports'**. Di sinilah kita menambahkan port interface fisik mana saja yang akan 'digabungkan' ke dalam interface `bridge1` yang baru kita buat (Contoh: Menambahkan port interface ke bridge: <https://drive.google.com/open?id=13PXywWJftyq1VIFx6BlZu01DbQlpK6wu&usp=drive_fs>). (Ulangi langkah ini untuk `ether2`, `ether3`, dst.)

5.  Voila! Kita sudah berhasil membuat interface bridge (`bridge1`) yang berisi port-port fisik (`ether2` & `ether3`). Dengan ini, perangkat apapun yang disambungkan di port interface yang berbeda (asal port tersebut sudah dimasukkan ke port bridge) akan dianggap sebagai 1 port yang sama.

6.  Soo, untuk mengujinya mari kita setting IP address untuk bridgenya! Masuk ke menu **'IP'** -> **'Addresses'**. Lalu tambahkan IP baru, tapi pastikan di bagian `Interface`, kamu memilih `bridge1` (bukan `ether2` atau `ether3` lagi!) seperti berikut (Contoh: Setting IP Address untuk interface bridge1: <https://drive.google.com/open?id=1-oYiiIlNZICh5M2lOoOLsn8zvyH7u7of&usp=drive_fs>).

7.  Selanjutnya, kita harus membuat DHCP server agar device yang dihubungkan dapat mendapat IP secara otomatis. Caranya cukup pergi ke **'IP'** -> **'DHCP Server'** lalu klik **'DHCP Setup'** (Contoh: Menjalankan DHCP Setup: <https://drive.google.com/open?id=10H3iKQoeNcsc9TUL1LDQuJfLjSXRbNQQ&usp=drive_fs>). Pastikan kamu menjalankan setup ini di atas interface `bridge1`.

8.  Kalau sudah, kita pergi ke Device yang kita sambungkan (dalam hal ini 'VPCs'). Coba request IP DHCP-nya dengan command:

    ```
    ip dhcp
    
    ```

    Hasilnya akan seperti pada (Contoh: Hasil VPCs mendapatkan IP DHCP: <https://drive.google.com/open?id=1vAtHnVSuHS_g-S5Jd6xRrcwEkws4Cj_Y&usp=drive_fs>) (VPC1 dan VPC2 akan dapat IP di satu segmen yang sama, misal 192.168.10.254 dan 192.168.10.253).

9.  Terakhir, untuk menguji apakah benar device yang disambungkan dari port yang berbeda tersebut benar-benar sudah berada di satu "kamar" yang sama, kita cukup jalankan command `trace` dari VPC1 ke IP VPC2.
    Hasilnya akan seperti pada (Contoh: Hasil trace antar VPCs - direct: <https://drive.google.com/open?id=17IAK8HWOvBRVu9rcQMTocSEMWHIzsevL&usp=drive_fs>).

Nah, di gambar hasil trace itu kelihatan hasilnya: `trace` langsung nyambung ke IP device tetangga, nggak ada *next hop* lagi! Ini adalah bukti kalau kedua device (VPCs) kita, biarpun nyolok di port fisik yang beda (`ether2` dan `ether3`), sekarang dianggap ada di satu jaringan Layer 2 yang sama, persis kayak nyolok ke switch. Mantap! LABS Bridge sederhana kita sudah beres.

## Ringkasan LABS

Jadi, intinya konsep Bridge di MikroTik itu gampang banget. Kita 'mengorbankan' beberapa port fisik (kayak `ether2`, `ether3` di lab tadi) untuk 'dilebur' jadi satu interface virtual baru yang kita sebut `bridge1`. Nah, interface `bridge1` inilah yang sekarang bertindak sebagai "otak" atau "induk" jaringannya.

Di LABS tadi, kita udah buktikan: kita pasang IP Address dan DHCP Server-nya langsung ke `bridge1`. Hasilnya? Semua device yang nyolok ke `ether2` atau `ether3` (yang udah jadi anggota bridge) bakal dapat IP dari DHCP yang sama dan berada di satu segmen jaringan yang sama, seolah-olah mereka semua nyolok ke switch yang sama.

Secara singkat, poin-poin pentingnya:

* **Konsep:** Bridge adalah interface virtual yang 'mengikat' beberapa port fisik (misal `ether2`, `ether3`, `wlan1`) menjadi satu kesatuan.
* **Tujuan:** Mengubah port-port router (Layer 3) tersebut agar berfungsi seperti port switch (Layer 2).
* **Proses di LABS:**
    1.  Kita buat `bridge` baru di menu `Bridge`.
    2.  Kita tambahkan `port` fisik (`ether2`, `ether3`) ke dalam `bridge` yang baru dibuat.
    3.  Konfigurasi IP (IP Address & DHCP Server) *tidak lagi* dipasang di `ether` satu per satu, tapi langsung dipasang di interface `bridge` virtualnya (`bridge1`).
* **Hasil:** Semua perangkat (VPCs) yang terhubung ke port anggota `bridge` akan berada di satu jaringan yang sama dan bisa saling komunikasi langsung (direct) seolah terhubung ke satu switch fisik.