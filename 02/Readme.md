# Rangkuman 02: Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

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

- Menjelaskan konsep proses pada sistem terdistribusi.
- Menjelaskan berbagai jenis komunikasi antarproses (*Inter-Process Communication* atau IPC).
- Menganalisis masalah sinkronisasi, reliabilitas, dan keamanan.
- Menghubungkan konsep IPC dengan contoh nyata, seperti microservices, cloud, dan *distributed messaging*.

## 2. Apa Itu Proses?

Slide 5 menampilkan *htop*, yaitu alat di Linux untuk melihat proses yang sedang berjalan. Setiap baris mewakili satu proses dan menampilkan informasi seperti PID, pengguna, penggunaan CPU dan memori, serta perintah yang dijalankan.

Perbedaan program dan proses:

- **Program** adalah kumpulan instruksi yang tersimpan sebagai berkas di disk; sifatnya pasif.
- **Proses** adalah program yang sedang dijalankan dan berada di memori (RAM).

Satu proses dapat memiliki beberapa *thread*. Sistem operasi menggunakan **scheduler** untuk memilih thread yang akan dijalankan CPU berdasarkan prioritasnya. Perpindahan CPU dari satu thread ke thread lain disebut **context switch**. **Preemption** adalah penghentian sementara secara paksa atas thread yang sedang berjalan agar thread lain dapat dijalankan.

## 3. Manajemen Proses

Manajemen proses mencakup tiga aktivitas utama:

| Aktivitas | Penjelasan |
|---|---|
| **Process creation** | Sistem operasi membuat proses baru dengan menerima permintaan, menyediakan sumber daya seperti memori dan berkas, lalu menyiapkan informasi proses (PCB, register, dan stack) agar dapat dijalankan. |
| **Process migration** | Proses dipindahkan dari satu CPU atau node ke CPU atau node lain, misalnya untuk menyeimbangkan beban, meningkatkan kinerja, atau menangani kegagalan. Status dan lokasi proses diperbarui. |
| **Process monitoring** | Sistem memantau status proses, penggunaan sumber daya, dan kinerjanya, kemudian dapat mengambil tindakan korektif jika diperlukan. |

## 4. Sistem Terdistribusi dan Komunikasi

Sistem terdistribusi adalah kumpulan komputer atau node independen yang bekerja sama dan terkoordinasi sebagai satu kesatuan logis. Setiap node menjalankan proses. Agar sistem dapat bekerja sebagai satu kesatuan, proses di berbagai node perlu berkomunikasi.

### Komunikasi antar-node

Diagram pada slide 10 menunjukkan bahwa setiap node (A, B, C, dan D) memiliki tiga lapisan: **Application/Service**, **Communication Layer**, dan **Local Data/Storage**. Lapisan komunikasi dapat menggunakan HTTP, gRPC, atau *message queue*.

Bentuk komunikasi yang ditunjukkan:

- **Request**: permintaan layanan atau data.
- **Response**: balasan atau hasil permintaan.
- **Publish**: pengiriman event atau pembaruan.
- **Subscribe**: pendaftaran untuk menerima notifikasi.

Protokol dan layanan yang umum digunakan antara lain HTTP/HTTPS, gRPC, *message queue* (misalnya Kafka dan RabbitMQ), serta DNS/*service discovery*.

Komunikasi antar-node bermanfaat untuk:

1. Bertukar data.
2. Berkoordinasi, termasuk sinkronisasi, konsistensi data, dan transaksi.
3. Meningkatkan keandalan sehingga sistem dapat tetap berjalan saat satu node gagal.
4. Mendukung skalabilitas dengan menambah atau mengurangi node sesuai beban.

### Contoh penerapan

- **Game multiplayer**: perangkat pemain bertukar informasi selama permainan.
- **Gojek/Grab**: antarmuka pengguna berkomunikasi dengan layanan driver, pelacakan lokasi, penghitungan biaya, dan layanan lainnya.
- **Chat atau video call WhatsApp**: komunikasi antarpengguna berjalan di atas infrastruktur terdistribusi.

## 5. Model Komunikasi Pesan

| Model | Penjelasan | Contoh |
|---|---|---|
| **Langsung** | Proses berkomunikasi langsung menggunakan socket. | Program socket Python. |
| **Tidak langsung** | Pesan dikirim melalui perantara, seperti *mailbox* atau *message queue*. | Perintah `write` di Linux, menurut modul. |
| **Publish/Subscribe (Pub/Sub)** | Publisher mengirim pesan ke suatu *topic*. Subscriber yang berlangganan topic tersebut akan menerima pesan. | Publisher–Topic–Subscriber. |

### Contoh program socket

Contoh berikut membuat server TCP yang menerima pesan dan mengirimkan kembali pesan yang sama (*echo*).

**Server:**

```python
import socket

HOST = '127.0.0.1'   # alamat localhost
PORT = 65432         # port non-privileged (> 1023)

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((HOST, PORT))
    s.listen()
    conn, addr = s.accept()
    with conn:
        print(f"Connected by {addr}")
        while True:
            data = conn.recv(1024)
            if not data:
                break
            print(f"Received: {data.decode()}")
            conn.sendall(data)   # kirim balik data yang diterima
```

**Client:**

```python
import socket

HOST = '127.0.0.1'
PORT = 65432

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect((HOST, PORT))
    s.sendall(b"Hello, world!")
    data = s.recv(1024)

print(f"Received from server: {data.decode()}")
```

**Cara kerja:**

- `socket.AF_INET` memilih alamat IPv4, sedangkan `socket.SOCK_STREAM` menggunakan TCP.
- Server menjalankan `bind` untuk mengikat socket ke alamat dan port, `listen` untuk menunggu koneksi, dan `accept` untuk menerima koneksi.
- Server menggunakan `recv` untuk menerima data dan `sendall` untuk mengirimkan kembali data tersebut.
- Client menggunakan `connect` untuk terhubung, `sendall` untuk mengirim “Hello, world!”, dan `recv` untuk menerima balasan.
- Hasilnya, server mencetak pesan yang diterima dan client mendapatkan kembali pesan yang sama.

## 6. Proses pada Sistem Terdistribusi

Proses dapat berjalan di node yang berbeda. Karena node-node tersebut tidak memiliki **shared memory** yang dapat diakses bersama, komunikasi memerlukan mekanisme IPC yang sesuai untuk lingkungan terdistribusi.

## 7. IPC (*Inter-Process Communication*)

Modul membahas empat jenis IPC:

1. **Shared memory**
2. **Message passing**
3. **Remote Procedure Call (RPC)**
4. **Middleware**

### 7.1 Shared Memory

Dalam shared memory, Process A menulis ke area memori bersama dan Process B membaca dari area tersebut (diagram slide 16). Mekanisme ini tidak cocok untuk komunikasi antar-node pada sistem terdistribusi karena memori pada satu node tidak dapat diakses langsung oleh node lain.

### 7.2 Message Passing

Proses bertukar informasi dengan mengirim dan menerima pesan melalui *communication link*. Secara konseptual, Process P dapat mengirim pesan dengan `send(Q, message)`, lalu Process Q menerimanya dengan `receive(P, message)`.

Standar yang disebutkan adalah **MPI (Message Passing Interface)**, yang informasinya tersedia melalui mpi-forum.org. Modul menyebut versi 5.0 sebagai versi terakhir hingga Oktober 2026. Implementasi Python yang disebutkan adalah `mpi4py`. Diagram modul juga menunjukkan OpenMPI yang berjalan di atas jaringan berkecepatan tinggi.

### 7.3 RPC (*Remote Procedure Call*)

RPC memungkinkan klien memanggil prosedur yang berjalan di server seolah-olah memanggil fungsi lokal. **Stub** pada sisi klien dan server menjadi perantara untuk mengirim pemanggilan melalui jaringan dan mengembalikan hasilnya.

Contoh yang disebutkan adalah **gRPC**. Server, misalnya layanan C++, dapat dipanggil oleh klien dengan bahasa berbeda, seperti Ruby atau Android-Java. Stub masing-masing membantu pertukaran **Proto Request** dan **Proto Response**.

### 7.4 MOM (*Message-Oriented Middleware*)

MOM adalah infrastruktur untuk membantu aplikasi berkomunikasi dan bertukar data melalui pesan. Setiap pesan merupakan unit informasi mandiri. Pengiriman dan penerimaan pesan berlangsung secara **asinkron**, sehingga pengirim dan penerima tidak harus bekerja pada waktu yang sama.

Pada diagram, client mengirim pesan melalui API ke **Messaging Provider** dan tujuan (*destination*); client lain kemudian menerima pesan tersebut.

| No. | Protokol/standar | Catatan dari modul |
|---:|---|---|
| 1 | X/Open XATMI | *Distributed Transaction Processing*. |
| 2 | AMQP (*Advanced Message Queuing Protocol*) | Biasanya untuk aplikasi enterprise. |
| 3 | HLA IEEE 1516 (*High Level Architecture*) | Dari IEEE dan SISO. |
| 4 | MQTT (*MQ Telemetry Transport*) | Biasanya untuk perangkat dengan sumber daya terbatas. |
| 5 | DDS (*Data Distribution Service*) | Dari OMG (*Object Management Group*). |
| 6 | XMPP (*eXtensible Messaging and Presence Protocol*) | — |

## 8. Studi Kasus: Microservices

Dalam diagram slide 23, satu antarmuka pengguna berkomunikasi dengan beberapa microservice yang terpisah. Setiap microservice mengelola basis datanya sendiri. Komponen-komponen tersebut berkomunikasi menggunakan mekanisme IPC yang telah dibahas.

Microservices merupakan contoh penerapan sistem terdistribusi yang bergantung pada komunikasi antarproses.

## 9. Komunikasi Sinkron dan Asinkron

| Jenis | Cara kerja |
|---|---|
| **Sinkron** | Client mengirim request lalu menunggu respons dari server sebelum melanjutkan. |
| **Asinkron** | Client mengirim request dan dapat tetap bekerja. Respons diproses ketika sudah diterima. |

## 10. Berbagai Masalah pada Komunikasi Antarproses

| Masalah | Penjelasan | Solusi dalam modul |
|---|---|---|
| **Reliability** | Pesan perlu terkirim secara lengkap dan berurutan, termasuk ketika paket hilang atau sistem mengalami crash. | Acknowledgement, transmisi ulang, dan protokol transport andal seperti TCP. |
| **Ordering** | Pesan perlu diproses dalam urutan yang logis dan dapat diprediksi. | Pipes (FIFO), message queues, shared memory, sockets dan message passing, serta RPC. |
| **Security** | Pesan perlu dilindungi agar komunikasi aman. | Enkripsi dan autentikasi. |
| **Performance** | Komunikasi perlu berlangsung dengan cepat. | Perhatikan latency dan bandwidth. |

## 11. Protokol Komunikasi

Modul menyebut TCP, UDP, dan HTTP sebagai protokol yang mendasari komunikasi. Protokol tingkat lebih tinggi yang berjalan di atas HTTP meliputi gRPC, GraphQL, dan REST.

Perbandingan lapisan HTTP/2 dan HTTP/3 pada slide 27:

| Lapisan | HTTP/2 | HTTP/3 |
|---|---|---|
| Application | HTTPS | HTTPS |
| Security | TLS | QUIC |
| Transport | TCP | UDP |
| Network | IP | IP |

Dengan demikian, menurut diagram modul, HTTP/2 menggunakan TLS di atas TCP, sedangkan HTTP/3 menggunakan QUIC di atas UDP.

## 12. Konsep Sinkronisasi

Setiap node memiliki **clock** atau jam lokal sendiri. **Sinkronisasi clock** adalah upaya menyelaraskan jam lokal beberapa komputer melalui jaringan agar waktunya sama dan konsisten.

Sinkronisasi waktu penting untuk mengurutkan kejadian, menjaga konsistensi data, dan mengoordinasikan operasi. Jam antarnode dapat menyimpang karena **latensi jaringan** dan **clock drift** (pergeseran jam perangkat keras).

Metode yang dijelaskan dalam modul:

- Sinkronisasi eksternal dapat menggunakan atomic clock yang menghasilkan TAI (*International Atomic Time*), serta GPS dan UTC bila diperlukan.
- Penyelarasan melalui jaringan dapat menggunakan **NTP (Network Time Protocol)** atau **PTP (Precision Time Protocol)**.
- PTP dinyatakan lebih presisi daripada NTP.

## 13. Logical Clock

### Masalah urutan kejadian

Slide 29–30 menggambarkan tiga layanan yang mengirim log ke **Central Logging Service**. Urutan kejadian yang dimaksud adalah:

| Waktu | Kejadian |
|---|---|
| 12:00:01 | Sales Service mencatat transaksi penjualan. |
| 12:00:02 | Product Service memperbarui jumlah produk terjual. |
| 12:00:03 | Invoice Service membuat faktur. |

Jika urutan ditentukan hanya dari timestamp jam asli, urutannya dapat tampak berubah akibat latensi, masalah jaringan, atau masalah perangkat keras.

### Lamport Logical Clock

Lamport Logical Clock menggunakan penghitung logis yang tidak bergantung langsung pada jam sebenarnya. Aturannya:

1. Setiap terjadi kejadian, nilai logical clock bertambah 1.
2. Saat mengirim pesan, proses menyertakan nilai logical clock di dalam pesan.
3. Saat menerima pesan, proses membandingkan nilai pada pesan dengan clock lokal, mengambil nilai terbesar, lalu menambahnya 1.

Contoh berikut dimulai dengan clock bernilai 0 pada setiap service:

| Langkah | Service | Proses | Clock |
|---:|---|---|---:|
| 1 | Sales | Mencatat transaksi, menaikkan clock dari 0 menjadi 1, lalu mengirim pesan ke Product dengan timestamp 1. | 1 |
| 2 | Product | Menerima timestamp 1, membandingkannya dengan clock lokal 0, mengambil nilai terbesar, lalu menambah 1 dan mengirim pesan ke Invoice. | 2 |
| 3 | Invoice | Menerima timestamp 2, membandingkannya dengan clock lokal 0, mengambil nilai terbesar, lalu menambah 1 saat membuat faktur. | 3 |

Hasil urutan logisnya adalah **1 (Sales) → 2 (Product) → 3 (Invoice)**.

### Pengembangan dari Lamport

Modul menyebut beberapa pengembangan logical clock:

- **Vector clocks** — Colin Fidge dan Friedemann Mattern (1988).
- **Plausible clocks** — Torres-Rojas dan Ahamad (1999).
- **Chain clocks** — Agarwal dan Garg (2005).
- **Interval tree clocks** — Almeida dan rekan-rekan (2008).
- **Blum clocks** — Lum Ramabaja (2019).

## 14. Koordinasi Proses

Koordinasi proses adalah mekanisme yang membuat proses-proses independen di berbagai komputer dapat bekerja sama untuk mencapai tujuan bersama. Koordinasi mengelola komunikasi, sinkronisasi, dan akses ke sumber daya bersama agar tidak terjadi konflik seperti **deadlock** atau **race condition**.

Perangkat yang disebutkan dalam modul: Apache ZooKeeper, etcd, Consul, ClickHouse Keeper, Oxia, dan KRaft.

## 15. Tantangan dan Solusi

| Tantangan | Solusi dalam modul |
|---|---|
| **Message loss** (pesan hilang) | Retransmission dan acknowledgement. |
| **Deadlock** | Timeout dan algoritma deteksi (*detection algorithm*). |
| **Data inconsistency** | Protokol konsensus seperti Raft dan Paxos. |
| **Security** | SSL/TLS dan OAuth2. |

## 16. Isu Terkini (*Current Issues*)

- **Edge dan fog computing**.
- **Observability dan tracing**, termasuk telemetry.
- **Container orchestration**, misalnya Kubernetes dan Docker Swarm.

## 17. Bahan Diskusi dari Modul

Modul mengajukan pertanyaan berikut tanpa memberikan jawabannya:

1. Apakah GraphQL merupakan contoh RPC?
2. Apakah REST merupakan contoh RPC?
3. Apakah telemetry merupakan solusi yang etis?

Untuk diskusi, modul menyarankan membandingkan definisi RPC—klien memanggil prosedur jarak jauh melalui stub—dengan cara kerja GraphQL dan REST. Untuk telemetry, pertimbangkan manfaat pemantauan sistem serta isu privasi pengguna. Pertanyaan-pertanyaan ini sebaiknya dibahas bersama dosen.

## 18. Poin Penting untuk UTS/UAS

1. Bedakan program (berkas di disk) dengan proses (program yang berjalan di RAM); pahami scheduler, context switch, dan preemption.
2. Hafalkan tiga aktivitas manajemen proses: creation, migration, dan monitoring.
3. Pahami alasan shared memory tidak cocok untuk komunikasi lintas node.
4. Kenali empat jenis IPC: shared memory, message passing, RPC, dan middleware.
5. Bedakan komunikasi langsung (socket), tidak langsung (mailbox/queue), dan Pub/Sub.
6. Pahami alur socket server: `bind` → `listen` → `accept` → `recv` → `sendall`; dan client: `connect` → `sendall` → `recv`.
7. Kenali MPI sebagai standar message passing, gRPC sebagai contoh RPC, dan peran stub.
8. Pahami MOM sebagai komunikasi asinkron dan kenali protokol yang dicantumkan, termasuk AMQP, MQTT, DDS, dan XMPP.
9. Bedakan komunikasi sinkron dengan asinkron.
10. Pahami masalah IPC—reliability, ordering, security, dan performance—beserta solusi yang disebutkan.
11. Kenali TCP, UDP, HTTP, gRPC, GraphQL, REST, serta perbedaan susunan protokol HTTP/2 dan HTTP/3 dalam modul.
12. Pahami sinkronisasi clock, penyebab deviasi, NTP dan PTP, serta peran atomic clock, TAI, GPS, dan UTC.
13. Pelajari tiga aturan Lamport Logical Clock dan latih perhitungan nilai clock.
14. Kenali pengembangan logical clock, seperti vector clock, plausible clock, chain clock, interval tree clock, dan Blum clock.
15. Pahami koordinasi proses serta solusi untuk message loss, deadlock, inkonsistensi data, dan masalah keamanan.
