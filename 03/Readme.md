# Rangkuman 03: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

**AI:** Claude

**Prompt:**

> Saya ingin kamu menganalisis dan membuat rangkuman dari modul yang saya lampirkan. Buat rangkuman berdasarkan isi modul asli secara menyeluruh, sistematis, dan mudah dipahami oleh mahasiswa.
>
> Ketentuan rangkuman:
> 1. Gunakan bahasa Indonesia yang sederhana, jelas, dan mudah dipahami, seolah-olah menjelaskan materi kepada seseorang yang baru pertama kali mempelajarinya.
> 2. Susun rangkuman sesuai urutan bab dan subbab dalam modul.
> 3. Jelaskan setiap konsep, teori, dan istilah penting tanpa mengubah maksud aslinya.
> 4. Sertakan rumus, sintaks, contoh program, tabel, atau diagram jika terdapat dalam modul, beserta penjelasannya.
> 5. Jelaskan fungsi, cara kerja, dan contoh penerapan konsep jika diperlukan.
> 6. Ambil poin-poin penting yang perlu dipahami dan dipelajari.
> 7. Jangan menghilangkan materi penting, terlalu banyak menyalin teks asli, atau menambahkan teori dari luar modul tanpa keterangan.
>
> Hasil yang diharapkan: buat rangkuman yang lengkap tetapi tetap ringkas, terstruktur, dan mudah dipelajari. Pastikan seluruh penjelasan sesuai dengan modul asli sehingga dapat digunakan sebagai bahan belajar mandiri maupun persiapan UTS dan UAS. Sebelum merangkum, analisis terlebih dahulu keseluruhan isi modul agar tidak ada materi penting yang terlewat.

---

## 1. Tujuan Pembelajaran

Setelah mempelajari modul ini, mahasiswa diharapkan mampu menjelaskan:

- Mengapa sinkronisasi diperlukan untuk koordinasi dan apa saja tantangannya.
- Sinkronisasi waktu fisik, termasuk algoritma dan protokolnya.
- Sinkronisasi waktu logis dan algoritma yang terkait.
- Masalah *mutual exclusion* dan algoritma penyelesaiannya.
- Masalah pemilihan koordinator dan cara mengatasinya.
- Studi kasus penerapan konsep-konsep tersebut di industri.

## 2. Apa Itu Sinkronisasi?

Sinkronisasi adalah koordinasi proses, data, dan sumber daya di berbagai node agar operasi sistem tetap konsisten dan andal. Sinkronisasi membantu menjaga konsistensi data, mengelola proses yang berjalan bersamaan, membuat sistem berperilaku koheren, dan mencegah konflik data seperti *data race*.

### Contoh race condition pada e-commerce

Misalnya, stok produk X awalnya 10. User A dan User B sama-sama membaca stok 10 sebelum salah satunya menyelesaikan pembelian. Setelah A membeli, stok menjadi 9. Karena B juga menggunakan nilai awal 10, pembelian B kembali membuat stok tercatat 9. Padahal, setelah dua pembelian, stok seharusnya 8.

| Tahap | Kejadian |
|---|---|
| Awal | Stok produk X = 10. |
| 1 | User A membaca stok 10. |
| 2 | User B juga membaca stok 10. |
| 3 | Proses A membeli; stok menjadi 9. |
| 4 | Proses B membeli berdasarkan nilai awal; stok tetap tercatat 9, bukan 8. |

Masalah terjadi karena kedua proses membaca nilai yang sama sebelum salah satunya selesai memperbarui data.

### Data race dan race condition

Modul membedakan keduanya sebagai berikut:

| Parameter | Data race | Race condition |
|---|---|---|
| **Fokus** | Akses bersamaan ke memori. | Urutan dan waktu eksekusi instruksi. |
| **Level** | Tingkat rendah, seperti CPU atau memori fisik. | Tingkat tinggi, seperti alur logika aplikasi atau business logic. |
| **Syarat terjadi** | Minimal dua thread mengakses memori yang sama, setidaknya satu akses berupa penulisan, dan tidak ada sinkronisasi seperti mutex/lock. | Kebenaran program bergantung pada urutan eksekusi yang tidak dapat diprediksi. |
| **Dampak** | Perilaku tidak terdefinisi, kerusakan memori, atau data tidak konsisten. | Hasil salah atau bug logika bisnis, seperti saldo minus atau transaksi ganda. |
| **Solusi** | Mutex/lock atau tipe data atomic. | Rancang ulang alur kontrol, misalnya dengan *atomic check-and-act* atau *database transaction lock*. |

**Contoh data race:** nilai `x` mula-mula 10. Thread 1 dan Thread 2 sama-sama membaca 10, menghitung 11, lalu menulis 11. Hasilnya `x = 11`, padahal setelah dua increment seharusnya 12.

**Contoh race condition:** saldo bank $100. Thread A dan B sama-sama memeriksa apakah saldo cukup untuk menarik $80. Keduanya lolos karena membaca saldo $100, lalu sama-sama menarik uang. Saldo akhirnya menjadi -$60.

### Contoh program multithreading

Modul menampilkan contoh `multithreads-example.py` dengan lima thread (A–E). Masing-masing menjalankan `task(name, delay)`, mencetak pesan “starting”, tidur selama lima detik untuk menyimulasikan operasi I/O, lalu mencetak “finished”.

```python
t1 = threading.Thread(target=task, args=("A", 5))
...
t1.start() ... t5.start()   # jalankan semua thread
t1.join() ... t5.join()     # tunggu semua thread selesai
print("All tasks completed.")
```

`start()` memulai thread, sedangkan `join()` menunggu thread selesai. Modul menjalankan program dua kali dan urutan pesan “finished” berubah pada setiap eksekusi. Ini menunjukkan bahwa urutan eksekusi thread tidak selalu dapat diprediksi dan dapat menjadi sumber race condition.

### Mengapa sinkronisasi penting?

Sinkronisasi membantu:

1. Menjaga integritas data dengan mencegah data race dan race condition.
2. Menyamakan state di seluruh node, misalnya posisi, kesehatan, dan inventaris pemain pada game multiplayer atau isi keranjang belanja antara browser dan database.
3. Mengoordinasikan tugas.
4. Mengelola sumber daya.
5. Mendukung pemulihan sistem dengan mempertahankan state.
6. Menggunakan sumber daya secara efisien dan mencegah pekerjaan berulang.
7. Melakukan load balancing.
8. Mencegah deadlock.
9. Mendukung operasi yang skalabel.

### Deadlock

Deadlock terjadi ketika proses saling menunggu sumber daya sehingga tidak ada proses yang dapat melanjutkan. Diagram pada slide 12 menunjukkan Process 1, 2, dan 3 serta sumber daya R1, R2, dan R3. Panah **Assigned to** menunjukkan sumber daya yang sedang dipegang proses; panah **Waiting for** menunjukkan sumber daya yang sedang ditunggu. Jika hubungan tersebut membentuk lingkaran tunggu, proses-proses terkait dapat terhenti.

## 3. Tantangan Sinkronisasi

Tantangan yang disebutkan dalam modul:

- Latensi jaringan dan *partitioning*.
- Skalabilitas.
- Sistem yang toleran terhadap kegagalan (*fault-tolerant system*).
- Pengendalian konkurensi (*concurrency control*).
- Konsistensi data.
- Sinkronisasi waktu.

## 4. Sinkronisasi Waktu Fisik

Sinkronisasi waktu fisik adalah penyelarasan *real-time clock* pada sistem terdistribusi dengan standar waktu bersama. Hal ini berguna untuk menghubungkan log, menjadwalkan event, dan menjaga konsistensi sistem. Sumber waktu dapat berasal dari atomic clock atau GPS yang tersinkronisasi dengan atomic clock, lalu disebarkan melalui protokol jaringan.

### Standar waktu

| Standar | Keterangan menurut modul |
|---|---|
| **GMT (Greenwich Mean Time)** | Sudah tidak digunakan sebagai standar yang dibahas modul. |
| **UTC (Universal Coordinated Time)** | Didefinisikan oleh ITU; teks modul menyebut digunakan sebagai standar sejak 1963. |
| **TAI (International Atomic Time; Temps Atomique International)** | Disebut sebagai dasar dari UTC. |
| **LTC (Coordinated Lunar Time)** | Masih dalam pengembangan. |

Slide yang mengutip Wikipedia dan NIST menjelaskan bahwa singkatan UTC merupakan kompromi: singkatan bahasa Inggris akan menjadi CUT, sedangkan bahasa Prancis menjadi TUC. UTC dipilih agar tidak berpihak pada salah satu bahasa.

**Catatan modul:** teks slide menyebut UTC menjadi standar pada 1963, sedangkan kutipan pada gambar menyebut sistemnya dirancang pada 1970 dan namanya disetujui pada 1967. Untuk ujian, ikuti angka pada teks slide dan konfirmasikan kepada dosen jika diperlukan.

### Rantai sinkronisasi waktu

Diagram slide 18–19 menunjukkan alur:

**Master atomic clock → atomic clock pada satelit GPS → sinyal waktu GPS → GPS dongle → server NTP/PTP → client (sistem operasi, ponsel, dan perangkat lain).**

Modul menyebut beberapa sumber dan perangkat terkait waktu:

- Satelit GPS dioperasikan oleh U.S. Space Force; modul menyebut 31 satelit di *Medium Earth Orbit* (MEO).
- **DSAC (Deep Space Atomic Clock)** diluncurkan NASA pada 2019 untuk menguji stabilitas.
- **ACES (Atomic Clock Ensemble in Space)** menempatkan *cold-atom clock* di orbit rendah Bumi melalui misi di ISS.
- *Ground control* mengelola master clock di U.S. Naval Observatory (Washington, D.C.) dan Schriever Space Force Base (Colorado).

### Praktik: GPS dongle sebagai sumber waktu

Diagram proyek `gps_ntp_timeserver` menggambarkan GPS dongle (U-Blox G7020) yang terhubung melalui `/dev/ttyACM0` ke `gpsd` (*GPS daemon*) di Linux. Data waktu diteruskan melalui shared memory (RAM) ke NTP, lalu didistribusikan ke LAN.

Modul juga menunjukkan langkah pemeriksaan dan informasi berikut:

- Paket `gpsd` (versi 3.27.5-1) mendukung perangkat GPS USB/serial. Contoh pemeriksaan paket menggunakan `pacman -Ss gpsd` dan `pacman -Si gpsd`.
- Perangkat dapat dikenali di Linux, misalnya sebagai `/dev/ttyACM0`; daftar perangkat dapat diperiksa dengan `ls /dev/tty*`.
- Modul memberikan contoh GPS dongle USB GPS/GLONASS yang tersedia di marketplace dengan harga sekitar Rp150.000.

### Network Time Protocol (NTP)

NTP adalah protokol sinkronisasi waktu yang merujuk pada standar internasional RFC 5905. Modul menyebut protokol ini dikembangkan oleh David L. Mills dan Harlan Stenn (Network Time Foundation) serta diperkenalkan pada 1985.

NTP menggunakan hierarki **stratum**: perangkat atau jam referensi berada di bagian atas, diikuti server stratum 1, 2, 3, dan seterusnya.

Implementasi yang dibahas adalah **chrony**. Menurut modul, chrony dapat menyinkronkan waktu dari server NTP, jam referensi seperti penerima GPS, atau input manual, serta dapat berfungsi sebagai server NTPv4. Akurasi tipikal yang dicantumkan: beberapa milidetik melalui Internet, mikrodetik pada LAN, dan sub-mikrodetik dengan *hardware timestamping* atau jam referensi perangkat keras.

Komponen chrony:

- `chronyd`: daemon yang menjalankan layanan sinkronisasi.
- `chronyc`: alat command-line untuk memeriksa dan mengendalikan layanan.

Contoh langkah praktik di Linux (Artix/dinit):

```sh
sudo pacman -S chrony chrony-dinit
sudo dinitctl enable chrony
chronyc -n sources
chronyc -n tracking
```

- Perintah pertama memasang chrony dan integrasi dinit.
- Perintah kedua mengaktifkan layanan.
- `chronyc -n sources` menampilkan sumber waktu, termasuk kolom Stratum, Poll, Reach, LastRx, dan Last sample.
- `chronyc -n tracking` menampilkan status sinkronisasi, seperti Reference ID, Stratum, System time, Last offset, RMS offset, Update interval, dan Leap status.

### Precision Time Protocol (PTP)

PTP adalah protokol sinkronisasi waktu yang distandardisasi dalam keluarga **IEEE 1588**. Modul menyebut:

- IEEE 1588-2002.
- IEEE 1588-2008 (PTP versi 2), yang tidak kompatibel ke belakang dengan versi 2002 dan memperkenalkan konsep *profile*.
- IEEE 1588-2019, yang mencakup perbaikan yang kompatibel ke belakang dengan versi 2008.

**IEEE 802.1AS (gPTP)** merupakan adaptasi PTP untuk *Audio Video Bridging* (AVB) dan *Time-Sensitive Networking* (TSN).

Implementasi Linux yang dibahas adalah **LinuxPTP**:

- `ptp4l` menjalankan PTP sebagai *Boundary Clock*, *Ordinary Clock*, atau *Transparent Clock*.
- `phc2sys` menyinkronkan dua atau lebih jam, misalnya jam perangkat keras NIC dengan jam sistem.
- Modul mencantumkan pemasangan melalui AUR dengan `trizen -S linuxptp` (versi 4.4-1).

PTP memerlukan NIC yang mendukung *hardware timestamping*. Dukungan dapat diperiksa dengan:

```sh
sudo ethtool -T eth0
```

Dalam contoh modul, NIC hanya mendukung software transmit/receive dan system clock, sedangkan **PTP Hardware Clock** bernilai `none`; artinya NIC tersebut tidak mendukung *hardware timestamping*. Modul juga menekankan bahwa PTP lebih presisi daripada NTP.

## 5. Sinkronisasi Waktu Logis

Sinkronisasi waktu logis menetapkan urutan sebab-akibat (*causal order*) antarperistiwa tanpa bergantung pada waktu fisik dunia nyata. Urutan ini bersifat transitif: jika A terjadi sebelum B, dan B terjadi sebelum C, maka A terjadi sebelum C.

Leslie Lamport merupakan perintis konsep ini; Lamport Logical Clock telah dibahas pada Modul 2.

### Vector clocks

Modul mencatat perbedaan istilah: *vector* berukuran dinamis, sedangkan *array* berukuran tetap. Dalam contoh vector clock, setiap proses menyimpan satu vektor yang mencatat hitungan semua proses. Posisi indeks ditetapkan: A pada indeks 0, B pada indeks 1, dan C pada indeks 2. Kondisi awal ketiganya adalah `[0,0,0]`.

Aturannya:

1. Pada setiap event—event internal, pengiriman, atau penerimaan pesan—proses menambah 1 pada posisi miliknya sendiri.
2. Sebelum mengirim pesan, proses memperbarui clock-nya lalu mengirim seluruh vektor.
3. Saat menerima pesan, proses mengambil nilai maksimum untuk setiap elemen antara vektor lokal dan vektor yang diterima (*element-wise max*), lalu menambah posisi miliknya sendiri.

Contoh langkah dalam modul:

| Langkah | Kejadian | Hasil |
|---:|---|---|
| 1 | A melakukan event internal. | A = `[1,0,0]` |
| 2 | A menambah clock sebelum mengirim ke B. | A = `[2,0,0]` |
| 3 | B menerima pesan dan mengambil maksimum tiap elemen. | B = `[2,0,0]` |
| 4 | B menambah posisi miliknya lalu mengirim ke C. | B = `[2,1,0]` |
| 5 | C menerima pesan, mengambil maksimum tiap elemen, lalu melakukan event internal. | C: `[2,1,0]` → `[2,1,1]` |
| 6 | C melakukan event internal lagi. | C = `[2,1,2]` |

Vektor akhir yang dicantumkan: A `[2,0,0]`, B `[2,1,0]`, dan C `[2,1,2]`.

### Membandingkan dua vector clock

- Event A terjadi sebelum event B jika **setiap** angka pada clock A lebih kecil atau sama dengan angka terkait pada clock B, dan **minimal satu** angka pada A lebih kecil.
- Dua event dinyatakan *concurrent* (independen) jika hubungan “terjadi sebelum” tersebut tidak berlaku ke salah satu arah.

Contoh pembandingan dalam modul:

- A `[1,0,0]` dibandingkan dengan B `[0,0,0]`: modul menyebut B lebih dulu.
- A setelah mengirim `[2,0,0]` dibandingkan dengan B `[2,1,0]`: A lebih dulu.
- C akhir `[2,1,2]` dibandingkan dengan A `[2,0,0]`: modul menyebut keduanya “independen”.

**Catatan penting:** contoh terakhir tampak tidak sesuai dengan aturan perbandingan yang diberikan. Karena `[2,0,0]` ≤ `[2,1,2]` pada setiap elemen dan ada elemen yang lebih kecil, menurut aturan tersebut A seharusnya terjadi sebelum C, bukan independen. Konfirmasikan contoh ini kepada dosen dan gunakan aturan resmi saat mengerjakan soal.

Modul juga memperlihatkan implementasi `vclocks.py` (slide 42), yang mencetak perubahan vektor proses P0, P1, dan P2 serta hasil perbandingan, seperti:

```text
VC0 happens before VC1: True
VC1 happens before VC0: False
VC0 is concurrent with VC2: False
```

## 6. Tantangan *Mutual Exclusion*

**Mutual exclusion (mutex)** memastikan hanya satu proses atau thread yang mengakses sumber daya bersama—seperti printer, berkas, atau database—pada satu waktu. Tujuannya adalah mencegah konflik dan menjaga integritas data.

**Critical Section (CS)** adalah bagian program yang mengakses sumber daya bersama. Dengan mutex, eksekusi critical section tidak boleh saling tumpang tindih.

### Prinsip mutual exclusion

| Prinsip | Arti |
|---|---|
| **Mutual Exclusion** | Hanya satu proses pada satu waktu yang menggunakan sumber daya bersama. |
| **Progress** | Jika tidak ada proses di critical section, proses yang ingin masuk dapat memperoleh akses. |
| **Bounded Waiting** | Tidak ada proses yang menunggu tanpa batas waktu. |
| **Fairness** | Permintaan dilayani dengan urutan yang wajar. |

### Pendekatan penyelesaian

| Algoritma | Cara kerja | Kelebihan | Kekurangan |
|---|---|---|---|
| **Centralized** | Satu node bertindak sebagai koordinator. Proses meminta izin; jika CS kosong, koordinator memberikan akses. Setelah proses selesai dan melepas akses, koordinator dapat mengizinkan proses berikutnya. | Sederhana dan biaya rendah. | Koordinator menjadi *single point of failure* dan dapat menjadi bottleneck. |
| **Ricart–Agrawala** | Tidak menggunakan koordinator. Setiap proses mengirim permintaan bertimestamp dan baru masuk CS setelah semua proses lain menjawab OK. Biayanya `2 × (N − 1)` pesan per entri CS: `N − 1` permintaan dan sedikitnya `N − 1` balasan. | Tersebar penuh dan *fault tolerant* menurut modul. | Overhead pesan tinggi. |
| **Token-Based** | Satu token unik beredar di antara proses. Hanya pemegang token yang boleh masuk CS. | Hanya satu pesan per entri CS menurut modul. | Token yang hilang sulit dideteksi dan memerlukan mekanisme pemulihan. |

## 7. Pemilihan Koordinator

Koordinator dapat dibutuhkan untuk mengelola sumber daya bersama, menyinkronkan operasi, dan membuat keputusan global. Jika koordinator gagal atau node baru bergabung, sistem perlu memilih pemimpin baru secara otomatis.

Algoritma yang disebutkan:

- **Bully Algorithm**.
- **Ring Algorithm**.
- **Raft** atau **Paxos**, yang berbasis konsensus.

Contoh penerapan industri dalam modul:

- Apache ZooKeeper menggunakan **Zab (ZooKeeper Atomic Broadcast)**, yang disebut mirip Paxos.
- Kubernetes menggunakan **Raft** melalui etcd.

**Catatan:** modul hanya mencantumkan nama Bully Algorithm dan Ring Algorithm tanpa menjelaskan cara kerjanya. Karena itu, rangkuman ini tidak menambahkan penjelasan di luar modul.

## 8. Poin Penting untuk UTS/UAS

1. Jelaskan alasan sinkronisasi penting dan enam tantangannya.
2. Bedakan *data race* dan *race condition*, syarat terjadinya, dampaknya, serta solusi masing-masing.
3. Pahami contoh stok e-commerce: stok awal 10, dua pembelian seharusnya menyisakan 8, tetapi tanpa sinkronisasi dapat tercatat 9; pahami juga contoh saldo bank yang menjadi -$60.
4. Ingat syarat *data race*: minimal dua thread mengakses memori yang sama, sedikitnya satu akses berupa penulisan, dan tidak ada sinkronisasi.
5. Pahami deadlock melalui diagram lingkaran tunggu antara proses dan sumber daya.
6. Kenali standar waktu yang dibahas: GMT, UTC, TAI, dan LTC, termasuk catatan perbedaan tahun UTC pada materi.
7. Hafalkan rantai sinkronisasi: atomic clock → GPS → GPS dongle → NTP/PTP → client; kenali sumber atomic clock yang disebutkan.
8. Bandingkan NTP dan PTP: RFC 5905 dan IEEE 1588, chrony dan LinuxPTP (`ptp4l`, `phc2sys`), serta kebutuhan NIC dengan *hardware timestamping* untuk PTP.
9. Pahami aturan vector clock, cara membandingkan “terjadi sebelum” dan *concurrent*, serta latih perhitungan vektor A–B–C.
10. Hafalkan empat prinsip mutex dan tiga pendekatan algoritmanya, termasuk biaya `2 × (N − 1)` pesan pada Ricart–Agrawala dan satu pesan per entri CS pada pendekatan token menurut modul.
11. Kenali algoritma pemilihan koordinator Bully, Ring, Raft, dan Paxos, serta contoh ZooKeeper (Zab) dan Kubernetes (Raft melalui etcd).
