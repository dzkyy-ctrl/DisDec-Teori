# Rangkuman 04: Konsistensi dan Replikasi pada Sistem Terdistribusi

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

## 1. Konsistensi pada Sistem Terdistribusi

### 1.1 Apa itu konsistensi?

Konsistensi adalah jaminan bahwa node-node dalam sistem memiliki data yang sama atau tampak sama pada waktu tertentu, meskipun data disimpan di beberapa lokasi dan dapat diubah oleh banyak pengguna atau proses.

Jika data diperbarui pada satu server, sistem perlu memastikan salinan di server lain juga diperbarui. Hal tersebut menjadi lebih menantang ketika perubahan dapat terjadi secara bersamaan.

### 1.2 Mengapa konsistensi penting?

Tanpa konsistensi, pengguna dapat melihat versi data yang berbeda bergantung pada server yang melayani permintaannya.

**Contoh transfer bank:** nasabah mentransfer uang dari rekening A ke rekening B. Server 1 sudah mengurangi saldo rekening A, tetapi server 2 belum menambahkan saldo rekening B. Akibatnya, nasabah belum dapat menggunakan saldo yang seharusnya sudah masuk.

Intinya:

- Data yang tersimpan di banyak node dapat berbeda antar-node.
- Sistem berupaya agar pengguna melihat data yang benar dan terbaru.
- Konsistensi berhubungan dengan trade-off **Consistency, Availability, dan Partition Tolerance (CAP)**.

## 2. Teorema CAP

CAP digambarkan sebagai tiga karakteristik sistem:

| Huruf | Istilah | Arti |
|---|---|---|
| **C** | **Consistency** (konsistensi) | Node-node memiliki data yang sama. |
| **A** | **Availability** (ketersediaan) | Data tetap dapat diakses ketika pengguna membutuhkannya. |
| **P** | **Partition Tolerance** (toleransi partisi) | Sistem tetap beroperasi saat sebagian komunikasi jaringan gagal atau terputus. |

Modul menekankan bahwa partisi jaringan dapat terjadi. Karena itu, ketika partisi terjadi, sistem harus menentukan prioritas antara konsistensi dan ketersediaan.

**Contoh e-commerce:** Server A melayani Indonesia barat dan Server B melayani Indonesia timur. Jika jaringan antarwilayah terputus:

- **AP (Availability + Partition Tolerance):** pengguna di kedua wilayah tetap dapat berbelanja, tetapi data stok mungkin sementara tidak sinkron.
- **CP (Consistency + Partition Tolerance):** sistem menolak transaksi sampai jaringan pulih agar stok tetap konsisten; ketersediaan layanan dikorbankan.

## 3. Model Konsistensi

Model konsistensi adalah aturan yang menjelaskan bagaimana dan kapan perubahan data terlihat oleh node atau klien lain. Setiap model memberikan jaminan berbeda mengenai kapan salinan data menjadi sama.

Urutan model dari kuat ke lemah menurut diagram modul:

**Linearizable → Sequential → Causal → Read-your-writes → Monotonic read → Eventual**

Modul juga membahas **Strong Consistency**. Linearizability dan sequential consistency berada di sisi kuat, sedangkan eventual consistency berada di sisi lemah.

| Model | Penjelasan | Contoh dalam modul |
|---|---|---|
| **Strong Consistency** | Setelah data diperbarui, semua node langsung melihat versi terbaru. Pembacaan setelah penulisan mendapatkan nilai yang sama. Modul menyebut Two-Phase Commit atau Paxos sebagai protokol yang biasa digunakan. | Perbankan: setelah saldo berubah, semua server segera mengetahui perubahan tersebut. |
| **Eventual Consistency** | Data tidak harus langsung sama, tetapi akhirnya menjadi sama jika tidak ada pembaruan baru dalam suatu periode. Cocok untuk sistem yang mengutamakan ketersediaan dan kecepatan. Dikaitkan dengan BASE. | Amazon DynamoDB dan Apache Cassandra. |
| **Causal Consistency** | Urutan operasi yang memiliki hubungan sebab-akibat tetap dijaga. Operasi yang tidak berhubungan dapat terlihat dalam urutan berbeda pada node lain. | Komentar tidak boleh muncul sebelum posting yang dikomentari. |
| **Read-Your-Writes** | Setelah menulis data, pengguna yang sama akan melihat hasil tulisannya pada pembacaan berikutnya. Pengguna lain belum tentu langsung melihatnya. | Pesan langsung muncul di layar pengirim pada aplikasi messaging, meski penerima belum melihatnya. |
| **Monotonic Read** | Pengguna yang membaca berulang kali tidak akan melihat versi yang lebih lama daripada versi yang pernah dilihat sebelumnya. | Timeline media sosial tidak kembali ke versi lama setelah di-refresh. |
| **Sequential Consistency** | Semua operasi tampak berlangsung dalam urutan yang sama bagi semua node, tetapi urutannya tidak harus sesuai waktu nyata. Urutan penulisan dijaga, tetapi sinkronisasi tidak harus langsung. | Semua pengguna melihat urutan posting yang sama, meskipun ada sedikit keterlambatan. |
| **Linearizability** | Model yang sangat kuat: operasi tampak atomik dan langsung, dengan urutan yang sama pada semua node. Setelah penulisan selesai, pembacaan berikutnya oleh klien mana pun menunjukkan penulisan tersebut atau yang lebih baru. Sistem tampak seperti satu node atomik. | Modul menyebut etcd, ZooKeeper, dan Spanner; contohnya mencegah dua pengguna mendaftarkan username/ID yang sama. |

### BASE

Modul mengaitkan BASE dengan eventual consistency:

- **Basically Available**: data tetap dapat dibaca atau ditulis meskipun terjadi kegagalan sebagian atau pemisahan jaringan.
- **Soft State**: replika tidak harus konsisten setiap saat; keadaan sistem dapat berubah seiring waktu.
- **Eventual Consistency**: semua node pada akhirnya menjadi konsisten jika tidak ada pembaruan baru setelah suatu waktu.

## 4. Trade-off Latensi dengan Konsistensi

Semakin tinggi konsistensi yang dijamin, biasanya semakin tinggi pula latensi (waktu respons):

- **Konsistensi naik → latensi naik.**
- **Konsistensi turun → latensi turun.**

| Aspek | Strong Consistency | Eventual Consistency |
|---|---|---|
| **Cara kerja** | Sistem memastikan node-node tersinkronisasi sebelum memberi respons. | Sistem merespons tanpa menunggu semua replika tersinkronisasi. |
| **Dampak** | Data konsisten, tetapi respons dapat lebih lambat karena komunikasi ant server. | Respons lebih cepat, tetapi data mungkin sementara belum terbaru di semua tempat. |

**Contoh dua pusat data (Jakarta dan Yogyakarta):** ketika pengguna memperbarui data di Jakarta lalu ada permintaan baca:

- Dengan strong consistency, sistem menunggu kedua pusat data memiliki data yang sama sebelum menjawab; data konsisten tetapi respons lebih lambat.
- Dengan eventual consistency, sistem dapat langsung menjawab dari salah satu pusat data sementara pembaruan diteruskan ke pusat data lainnya; respons lebih cepat tetapi data dapat berbeda untuk sementara.

## 5. Replikasi Data

### 5.1 Pengertian

Replikasi adalah proses membuat dan memelihara salinan data yang sama pada beberapa node atau server. Tujuannya adalah menjaga ketersediaan, keandalan, dan kecepatan layanan, termasuk saat satu node gagal.

Contohnya, database utama berada di Jakarta dan salinannya berada di Yogyakarta serta Denpasar. Menjaga agar salinan-salinan tersebut tetap sesuai merupakan bagian penting dari replikasi.

Diagram bergaya MySQL dalam modul menunjukkan pengguna melakukan **write** ke Source, sedangkan permintaan **read** dapat dilayani oleh Replica #1 sampai Replica #N. Proses replikasi mengalirkan data dari Source ke replika.

### 5.2 Tujuan replikasi

- **Availability**: jika satu server gagal, server lain masih memiliki salinan data.
- **Performance**: pengguna dapat membaca dari server yang lebih dekat.
- **Reliability**: salinan di beberapa tempat mengurangi risiko kehilangan data.
- **Skalabilitas**: beban pembacaan dapat dibagi ke beberapa server.

### 5.3 Jenis replikasi

| Jenis | Penjelasan | Kelebihan/kekurangan | Contoh dalam modul |
|---|---|---|---|
| **Master–Slave (Primary–Secondary)** | Satu master menangani penulisan; slave hanya membaca. | Konsistensi kuat, tetapi master dapat menjadi bottleneck. | PostgreSQL streaming replication. |
| **Multi-Master (Peer-to-Peer)** | Semua node dapat membaca dan menulis. | Fleksibel, tetapi berisiko mengalami konflik data. | PostgreSQL logical replication, Cassandra, MongoDB, Apache Ignite. |
| **Synchronous** | Pembaruan ditulis ke semua node sebelum transaksi dianggap selesai. | Konsisten, tetapi lebih lambat. | Google Spanner. |
| **Asynchronous** | Pembaruan dikirim ke node lain setelah transaksi di master selesai. | Cepat, tetapi replika dapat tertinggal. | MySQL async mode dan DynamoDB. |

## 6. Strategi Sinkronisasi Replika

Modul membagi strategi replikasi menurut empat sudut pandang:

1. **Waktu sinkronisasi**: synchronous, asynchronous, dan semi-synchronous.
2. **Arah replikasi**: master-slave, master-master, dan master-master asymmetric (peran atau sumber daya read-write berbeda).
3. **Cara sinkronisasi**: pull-based, push-based, dan hybrid.
4. **Penanganan konflik**: LWW, Vector Clock, CRDT, dan Operational Transformation.

### 6.1 Waktu sinkronisasi

| Mode | Cara kerja | Konsistensi dan performa |
|---|---|---|
| **Asynchronous** | Source melakukan commit tanpa menunggu replika. | Performa paling cepat, tetapi risiko kehilangan data paling tinggi. |
| **Semi-synchronous** | Source menunggu satu replika menerima data. | Menjadi titik tengah; modul menyebut penggunaannya pada MySQL. |
| **Synchronous** | Source menunggu semua replika melakukan commit. | Konsistensi paling tinggi, tetapi performa paling rendah. |

### 6.2 Cara sinkronisasi

- **Pull-based**: replika memeriksa Source secara berkala. Jika ada pembaruan, replika mengambilnya; jika tidak, tidak ada perubahan.
- **Push-based**: Source aktif mengirim pembaruan saat terjadi perubahan. Modul menyebut **CDC (Change Data Capture)** di Source serta WebSockets atau **SSE (Server-Sent Events)** untuk pengiriman.
- **Hybrid**: menggabungkan pendekatan pull dan push.

### 6.3 Penanganan konflik akibat beberapa pembaruan

- **LWW (Last Write Wins)**: pembaruan dengan timestamp terbaru dipakai, sedangkan pembaruan lain diabaikan.
- **Vector Clock**: menggunakan vector clock untuk mencatat dan membandingkan urutan pembaruan; konsep ini dibahas pada modul sebelumnya.
- **CRDT (Conflict-free Replicated Data Type)**: menggunakan struktur data di tiap node, bukan mengandalkan satu server pusat. Modul memberikan contoh Yjs (`yjs.dev`) untuk editor kolaboratif seperti Google Docs.
- **OT (Operational Transformation)**: mengubah operasi yang diterima berdasarkan informasi atau kondisi lokal.

#### Contoh CRDT: penghitung pengunjung

Agnes Monica dan Bambang masing-masing menyimpan hitungan akses pada map lokal. Setiap orang hanya memperbarui slot miliknya sendiri. Saat sinkronisasi, nilai tiap slot digabung menggunakan aturan maksimum.

| Tahap | Map Agnes | Map Bambang |
|---|---|---|
| Awal | `{Agnes: 0, Bambang: 0}` | `{Agnes: 0, Bambang: 0}` |
| Pencatatan lokal | `{Agnes: 5, Bambang: 0}` | `{Agnes: 0, Bambang: 3}` |
| Sinkronisasi dengan aturan max per orang | Agnes = `max(5,0)` = 5; Bambang = `max(0,3)` = 3 | Hasil yang sama. |
| Hasil akhir | `{Agnes: 5, Bambang: 3}` | `{Agnes: 5, Bambang: 3}` |

Total pengunjung adalah **5 + 3 = 8**. Karena setiap orang hanya mengubah slot miliknya, kedua map dapat digabung dengan aturan maksimum tanpa konflik. Catatan modul: slide menggunakan nama “Alice/Bob” pada penjelasan kunci, tetapi contoh yang dimaksud adalah Agnes/Bambang.

#### Contoh OT: mengedit dokumen bersama

Dokumen awal adalah `abc`. Dua orang mengedit secara bersamaan:

- Agnes menyisipkan `x` pada indeks 0 sehingga dokumennya menjadi `xabc`.
- Bambang menghapus karakter `c` pada indeks 2 sehingga dokumennya menjadi `ab`.

Tanpa OT, operasi yang diterima dapat menghasilkan dokumen berbeda:

- Agnes menerapkan “hapus indeks 2” pada `xabc`; karakter `b` terhapus sehingga hasilnya `xac`.
- Bambang menerapkan “sisipkan `x` pada indeks 0” pada `ab`; hasilnya `xab`.

Karena `xac` berbeda dari `xab`, hasil kedua pengguna tidak konsisten.

Dengan OT, operasi yang diterima disesuaikan dengan kondisi dokumen lokal terbaru:

- Di Agnes, operasi hapus indeks 2 diubah menjadi hapus indeks 3 karena `x` telah ditambahkan di depan; hasilnya `xab`.
- Di Bambang, penyisipan `x` di indeks 0 tidak mengubah posisi karakter yang dihapus; hasilnya juga `xab`.

Kedua pengguna akhirnya memperoleh dokumen yang sama, yaitu `xab`.

## 7. Protokol Replikasi

Modul membahas lima protokol: **Primary-based**, **Quorum-based**, **State Machine Replication**, **Gossip Protocol**, dan **Chain Replication**.

### 7.1 Primary-based (Master-Slave)

Satu node menjadi primary yang melayani operasi write dan read, sedangkan node lain hanya melayani read.

### 7.2 Quorum-based

Operasi write atau read dinyatakan berhasil setelah mendapat konfirmasi dari jumlah node minimum yang ditentukan.

Syarat kuorum:

```text
W + R > N
```

Keterangan:

- `N` = jumlah seluruh node.
- `W` = jumlah minimal node yang harus menyetujui write.
- `R` = jumlah minimal node yang harus menjawab read.

**Contoh:** terdapat tiga node (A, B, C), `W = 2`, dan `R = 2`. Syaratnya terpenuhi karena `2 + 2 > 3`.

**Skenario write** untuk `x = 100`:

1. Klien mengirim write secara paralel ke A, B, dan C.
2. A dan B berhasil menyimpan data dan mengirim ACK.
3. C lambat atau timeout.
4. Karena dua ACK sudah diterima dan `W = 2`, write dianggap berhasil meskipun C belum selesai.

**Skenario read** untuk `x`:

1. Karena `R = 2`, sistem meminta data dari minimal dua node, misalnya A dan C.
2. A memiliki nilai terbaru (`x = 100`), sedangkan C mungkin masih menyimpan versi lama.
3. Karena `W + R > N`, himpunan node write dan read pasti beririsan. Menurut penjelasan modul, irisan tersebut memastikan read bertemu setidaknya satu node yang memiliki data terbaru.

### 7.3 State Machine Replication

Beberapa replika menjalankan perintah yang sama dalam urutan yang sama untuk mendukung ketersediaan tinggi. Leader menerima permintaan klien, memberi urutan dan mencatatnya ke log, lalu replika menerapkan input dalam urutan tersebut.

**Contoh `SET x = 20`:**

1. Replika A, B, dan C awalnya memiliki `x = 10`.
2. Klien mengirim `SET x = 20` ke leader (Replika A).
3. A memberi nomor urut, mencatat perintah ke log, lalu mengirimkannya ke B dan C.
4. Setelah menerima konfirmasi bahwa perintah tercatat pada B dan C, A menganggap perintah telah dikomit.
5. Semua replika menjalankan `SET x = 20`, sehingga nilai lokal berubah menjadi 20.
6. Leader mengirim hasil akhir kepada klien.

### 7.4 Gossip Protocol

Gossip digunakan pada beberapa distributed database, termasuk Apache Cassandra, untuk memeriksa keberadaan node dan menyebarkan informasi replikasi.

Alur yang dijelaskan modul:

1. Node A mendeteksi perubahan, misalnya pembaruan data atau node tetangga yang mati.
2. Setiap detik, A memilih satu sampai tiga peer secara acak dan membagikan statusnya.
3. Peer yang menerima informasi memperbarui statusnya, lalu memilih peer lain untuk meneruskan informasi.
4. Setelah beberapa putaran, seluruh node dalam cluster mengetahui status terbaru.

### 7.5 Chain Replication

Node disusun dalam rantai, misalnya **head → A → B → C → tail**.

- Write masuk melalui head lalu diteruskan sepanjang rantai.
- Read dilayani oleh tail.
- Dalam diagram modul, ACK dikirim kepada klien dari head.

Sumber gambar pada modul disebut sebagai artikel Medium tentang replikasi Azure Storage.

## 8. Studi Kasus

Modul menyebut dua contoh:

- **PostgreSQL 18 Streaming Replication** sebagai contoh Master–Slave.
- **Apache Ignite 3 Master-Master** sebagai contoh Multi-Master.

Detail studi kasus tidak terdapat pada slide yang dirangkum; modul merujuk ke lecture notes dan source code pendamping. Klasifikasi di atas adalah informasi yang disebutkan pada slide.

## 9. Soal Diskusi dari Modul

Modul mengajukan pertanyaan:

1. Apa kelebihan dan kekurangan strong consistency dibanding eventual consistency?
2. Apa yang terjadi jika replikasi gagal pada satu node?

Panduan jawaban berdasarkan materi:

- **Strong consistency** memastikan data selalu sama dan terbaru, sehingga cocok untuk perbankan. Kekurangannya, latensi dapat tinggi dan ketersediaan dapat berkurang saat terjadi partisi.
- **Eventual consistency** mendukung respons cepat dan ketersediaan tinggi, tetapi data dapat sementara belum terbaru.
- Pada replikasi quorum, write dapat berhasil selama jumlah ACK memenuhi `W`, seperti contoh ketika Node C timeout tetapi dua node lain sudah memberikan ACK.
- Replikasi membantu mempertahankan salinan pada node lain saat satu server gagal.
- Gossip Protocol dapat menyebarkan informasi kegagalan node ke cluster.

## 10. Poin Penting untuk UTS/UAS

1. Pahami definisi konsistensi dan contoh transfer bank yang menunjukkan ketidakkonsistenan.
2. Pahami CAP: ketika terjadi partisi, sistem menentukan prioritas antara CP dan AP; pelajari contoh e-commerce.
3. Hafalkan urutan model konsistensi dari kuat ke lemah beserta contoh penerapannya.
4. Hafalkan kepanjangan BASE dan arti setiap komponennya.
5. Ingat trade-off: konsistensi yang lebih tinggi biasanya meningkatkan latensi.
6. Hafalkan tujuan replikasi: availability, performance, reliability, dan skalabilitas.
7. Bedakan Master–Slave dan Multi-Master, serta replikasi synchronous dan asynchronous beserta contoh dalam modul.
8. Pahami asynchronous, semi-synchronous, dan synchronous berdasarkan kapan source menunggu replika.
9. Bedakan pull dan push; kenali CDC, WebSockets, dan SSE.
10. Kenali empat metode penanganan konflik: LWW, Vector Clock, CRDT, dan OT. Pahami contoh CRDT (hasil 5 dan 3, total 8) serta OT (hasil akhir `xab`).
11. Hafalkan rumus quorum `W + R > N` dan contoh `N = 3`, `W = 2`, `R = 2`.
12. Kenali lima protokol replikasi dan ciri utamanya: primary, quorum, leader dan log pada State Machine Replication, peer acak pada Gossip, serta head dan tail pada Chain Replication.
