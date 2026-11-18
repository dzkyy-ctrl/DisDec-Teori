# Rangkuman 01: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

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

Setelah mempelajari modul ini, mahasiswa diharapkan mampu:

- Menjelaskan konsep sistem terdistribusi dan sistem terdesentralisasi.
- Menjelaskan perbedaan antara kedua sistem tersebut.
- Menyebutkan contoh penerapan nyata.
- Menjelaskan tantangan dan tren terbaru di bidang ini.

## 2. Arti Penting Sistem Terdistribusi dan Terdesentralisasi

Sistem terdistribusi dan terdesentralisasi dibutuhkan karena jumlah data dan pengguna terus bertambah. Layanan modern perlu mampu berkembang (**skalabel**), tetap berjalan ketika sebagian komponennya gagal (**fault-tolerant**), memiliki kinerja baik, dan tersedia sepanjang waktu.

Modul mencontohkan Google, Netflix, dan blockchain sebagai layanan yang bergantung pada pendekatan sistem ini.

| Istilah | Arti |
|---|---|
| **Skalabilitas** | Kemampuan sistem, jaringan, proses, atau organisasi untuk berkembang dan menangani peningkatan beban kerja, pengguna, atau permintaan tanpa menurunkan kinerja atau kualitas. |
| **Fault-tolerant** | Kemampuan sistem untuk tetap beroperasi meskipun sebagian komponen perangkat keras atau perangkat lunaknya gagal. |

## 3. Pengertian Sistem Terdistribusi

Menurut Tanenbaum dan van Steen (2007), sistem terdistribusi adalah kumpulan komputer independen yang bagi penggunanya tampak sebagai **satu sistem yang koheren**.

Tiga ciri utamanya:

1. **Komponen independen**: setiap komputer dapat beroperasi sendiri.
2. **Berkomunikasi melalui jaringan**: komponen bertukar informasi melalui jaringan.
3. **Transparansi**: pengguna tidak perlu mengetahui detail komponen yang bekerja di balik layar. Jenis transparansi yang disebutkan dalam modul meliputi lokasi, akses, replikasi, dan kegagalan.

### Contoh: sistem e-commerce

Diagram pada slide 8 menggambarkan pengguna yang mengakses layanan melalui aplikasi seluler (melewati **API Gateway**) atau browser (melalui **Storefront WebApp**). Di belakangnya terdapat layanan terpisah, seperti **Account Service**, **Inventory Service**, dan **Shipping Service**, yang terhubung melalui REST API. Setiap layanan memiliki basis datanya sendiri.

Walaupun banyak komponen bekerja sama, pengguna merasakan seolah-olah sedang menggunakan satu aplikasi.

## 4. Arsitektur Sistem Dasar

| Arsitektur | Cara kerja | Contoh dalam modul |
|---|---|---|
| **Client-Server** | Banyak klien (misalnya laptop, ponsel, atau PC) terhubung melalui internet ke server pusat. | Diagram client-server pada slide 9. |
| **Peer-to-Peer (P2P)** | Komputer yang disebut *peer* saling terhubung dan memiliki kedudukan setara, tanpa server pusat. | BitTorrent dan mata uang kripto seperti Bitcoin, Ethereum, Ripple, Uniswap, Cardano, Doge, Chainlink, Litecoin, dan Theta. |
| **Hybrid** | Menggabungkan client-server dan P2P. *Supernode* menghubungkan banyak klien, sementara peer dapat berbagi berkas secara langsung. | Signal, WhatsApp, dan Telegram. |

Pada diagram arsitektur hybrid, garis hitam menunjukkan koneksi klien-server, garis oranye menunjukkan koneksi antar-*supernode*, dan garis biru putus-putus menunjukkan berbagi berkas secara P2P.

## 5. Karakteristik Sistem Terdistribusi

1. **Transparansi lokasi**: pengguna tidak perlu mengetahui lokasi sumber daya.
2. **Skalabilitas**: sistem mampu menangani pertumbuhan beban.
3. **Fault tolerance**: sistem tetap berjalan ketika ada komponen yang gagal.
4. **Kinerja tinggi**.
5. **Heterogenitas**: sistem dapat terdiri atas perangkat, sistem, dan komponen yang beragam.

## 6. Pengertian Sistem Terdesentralisasi

- **Sentralisasi** berarti kendali berada pada satu titik.
- **Desentralisasi** berarti kendali dibagikan ke beberapa pihak atau titik, sehingga tidak ada satu titik kegagalan (*single point of failure*) yang dapat melumpuhkan seluruh sistem.
- Keputusan dibuat secara kolektif melalui **konsensus**.
- Contoh yang disebutkan dalam modul: blockchain, DNS, dan BitTorrent.

### Diagram Paul Baran (1964)

Diagram pada slide 14 membandingkan tiga bentuk jaringan:

- **(A) Centralized**: semua stasiun terhubung ke satu pusat.
- **(B) Decentralized**: terdapat beberapa pusat atau *hub* yang saling terhubung.
- **(C) Distributed**: jaringan menyerupai jaring, tanpa satu pusat dominan, dan memiliki banyak jalur antartitik.

## 7. Sistem Terdistribusi dan Terdesentralisasi

| Aspek | Sistem terdistribusi | Sistem terdesentralisasi |
|---|---|---|
| **Fokus** | Pembagian beban kerja. | Pembagian kendali. |
| **Contoh** | Google File System dan MinIO Object Storage (yang menurut modul telah di-*fork* menjadi Silo). | Blockchain. |

**Cara mudah mengingat:** terdistribusi berarti berbagi beban kerja, sedangkan terdesentralisasi berarti berbagi kendali. Keduanya berkaitan, tetapi memiliki fokus yang berbeda.

## 8. Studi Kasus

**Contoh sistem terdistribusi:**

- Google File System dan HDFS untuk penyimpanan data besar.
- Apache Kafka dan Apache Pulsar untuk *messaging* dan *streaming*.
- Netflix, Twitch, dan YouTube.
- Instagram, Threads, Facebook, dan TikTok.

**Contoh sistem terdesentralisasi:**

- Blockchain, seperti Bitcoin, Ethereum, Algorand, Sui, Near, dan Aptos.
- DNS (*Domain Name System*).
- BitTorrent.

## 9. CAP Theorem

CAP Theorem (disebut juga Brewer’s Theorem) dikemukakan Eric Brewer pada tahun 2000. Teorema ini menjelaskan tiga karakteristik sistem penyimpanan data terdistribusi:

| Huruf | Istilah | Arti |
|---|---|---|
| **C** | **Consistency** (konsistensi) | Operasi baca mengembalikan hasil penulisan terbaru atau menghasilkan galat. |
| **A** | **Availability** (ketersediaan) | Setiap permintaan ke node yang tidak gagal memperoleh respons yang bukan galat, meskipun data yang diterima mungkin belum terbaru. |
| **P** | **Partition Tolerance** (toleransi partisi) | Sistem tetap berfungsi ketika komunikasi antar-node terputus atau jaringan terpecah. |

Inti teorema dalam modul: pada *distributed data store*, **Consistency dan Availability tidak dapat dijamin sekaligus ketika terjadi partition**. Karena partition dapat terjadi, perancang sistem harus menentukan prioritasnya.

| Pilihan | Perilaku saat terjadi partition | Hal yang dikorbankan | Contoh menurut modul |
|---|---|---|---|
| **AP** (*Availability* + *Partition Tolerance*) | Permintaan tetap dilayani. | Konsistensi; data mungkin belum sinkron. | MongoDB. |
| **CP** (*Consistency* + *Partition Tolerance*) | Permintaan baca atau tulis dapat ditolak demi menjaga konsistensi. | Ketersediaan. | Apache Cassandra. |

**Catatan:** modul mencantumkan MongoDB sebagai contoh AP dan Cassandra sebagai contoh CP. Klasifikasi dalam literatur dapat berbeda atau bergantung pada konfigurasi. Untuk ujian, ikuti klasifikasi modul dan konfirmasikan kepada dosen jika diperlukan.

## 10. Konsistensi Data

| Jenis | Arti | Contoh |
|---|---|---|
| **Strong consistency** | Data memberikan hasil yang konsisten di mana pun dan kapan pun. | Sistem perbankan yang memerlukan data akurat. |
| **Eventual consistency** | Data akan menjadi konsisten setelah beberapa waktu. | Media sosial; misalnya, jumlah *like* mungkin terlambat tersinkronisasi. |

## 11. Tantangan

1. **Latency dan throughput**
   - *Latency* adalah waktu yang diperlukan suatu proses untuk selesai.
   - *Throughput* adalah banyaknya pekerjaan atau permintaan yang dapat diproses dalam suatu periode.
2. **Keamanan**, termasuk ancaman seperti DDoS dan *Sybil attack*.
3. **Kompleksitas pengelolaan node**.
4. **Governance (tata kelola)** pada sistem terdesentralisasi.

### Sybil attack

*Sybil attack* adalah serangan ketika pelaku membuat banyak identitas palsu—misalnya akun, node, atau pseudonim—untuk mendominasi pengaruh, suara, atau reputasi dalam jaringan. Serangan ini dapat terjadi pada sistem terdesentralisasi seperti P2P dan blockchain. Dengan menyamar sebagai banyak pengguna sah, pelaku dapat memanipulasi sistem dan merusak integritasnya.

Modul menyebutkan bahwa nama “Sybil” berasal dari nama seorang perempuan dengan *identity disorder* dalam sebuah buku tahun 1973.

## 12. Tren Masa Depan

- **Cloud-native**, dengan teknologi seperti Kubernetes dan Docker.
- **Edge computing** dan **fog computing**.
- **Web3** dan **DeFi**.
- **Federated data/learning**, termasuk AI terdistribusi.

## 13. Ringkasan Modul

- Sistem terdistribusi berfokus pada **pembagian beban kerja**.
- Sistem terdesentralisasi berfokus pada **pembagian kendali**.
- Keduanya berkaitan, tetapi tidak memiliki fokus yang sama.
- Keduanya menjadi fondasi bagi berbagai teknologi modern.

## 14. Poin Penting untuk UTS/UAS

1. Pahami definisi sistem terdistribusi menurut Tanenbaum dan van Steen serta tiga ciri utamanya.
2. Bedakan sistem terdistribusi (berbagi beban kerja) dari sistem terdesentralisasi (berbagi kendali), termasuk contohnya.
3. Pahami arsitektur client-server, P2P, dan hybrid beserta cara kerjanya.
4. Kenali perbedaan jaringan centralized, decentralized, dan distributed pada diagram Baran.
5. Pahami C, A, dan P dalam CAP Theorem, alasan trade-off ketika terjadi partition, serta perbedaan AP dan CP.
6. Bedakan strong consistency dan eventual consistency beserta contohnya.
7. Pelajari tantangan latency, throughput, DDoS, *Sybil attack*, pengelolaan node, dan governance.
8. Kenali tren cloud-native, edge/fog computing, Web3/DeFi, dan federated learning.
