# Rangkuman 05: Fault Tolerance

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

## 1. Pengertian Fault Tolerance

**Fault tolerance** adalah kemampuan sistem untuk tetap beroperasi dengan benar meskipun satu atau lebih komponennya mengalami kegagalan atau kesalahan. Sistem dengan kemampuan ini disebut **fault-tolerant system**.

Kemampuan tersebut penting bagi sistem yang membutuhkan **high availability**, sistem **mission-critical**, dan sistem **life-critical**.

## 2. Arti Penting Fault Tolerance pada Sistem Terdistribusi dan Terdesentralisasi

Sistem terdistribusi dan terdesentralisasi terdiri atas banyak komponen yang saling berkomunikasi. Koneksi antar-komponen dapat bermasalah, dan semakin kritis suatu aplikasi, semakin tinggi tuntutan agar layanan tetap berfungsi. Karena itu, kegagalan perlu diantisipasi untuk menjaga ketersediaan layanan.

## 3. Jenis Fault

| Jenis fault | Penjelasan | Contoh dalam modul |
|---|---|---|
| **Transient Fault** | Terjadi sementara, lalu dapat menghilang. | Petir. |
| **Intermittent Fault** | Muncul dan menghilang berulang kali. | Perangkat keras yang terlalu panas (*overheat*). |
| **Permanent Fault** | Bersifat tetap. | Tidak dicantumkan. |
| **Byzantine Fault** | Komponen memberikan hasil acak atau tidak konsisten. | Tidak dicantumkan. |

### Ilustrasi Byzantine Fault Tolerance

Diagram dalam modul menggunakan gambaran sebuah kastil yang dikepung pasukan. Pada gambar pertama, semua pasukan menyerang bersama sehingga serangan berhasil. Pada gambar kedua, sebagian pasukan adalah pengkhianat dan tidak ikut menyerang.

Ilustrasi ini menunjukkan bahwa beberapa komponen dapat berperilaku tidak jujur atau tidak konsisten. Sistem tetap harus mengambil keputusan yang benar meskipun menghadapi perilaku seperti itu; inilah tantangan Byzantine fault.

## 4. Klasifikasi Fault Berdasarkan Sumber

| Sumber fault | Contoh |
|---|---|
| **Hardware Faults** | Disk crash dan kegagalan daya (*power failure*). |
| **Software Faults** | Bug, memory leak, dan race condition. |
| **Communication Faults** | Packet loss dan network partition. |

## 5. Design Patterns dan Teknik untuk Mencapai Fault Tolerance

Modul menyebut lima teknik (sumber: designgurus.io):

1. Redundancy and Replication.
2. Load Balancing.
3. Checkpointing and Rollback Recovery.
4. Heartbeat and Failure Detection.
5. Consensus and Coordination.

### 5.1 Redundansi dan Replikasi

Sumber daya disediakan dalam beberapa salinan di banyak node. Redundansi ini bertujuan menghindari **single point of failure**, yaitu satu komponen yang jika gagal dapat menyebabkan seluruh sistem berhenti.

Rincian replikasi dibahas pada modul sebelumnya, “Konsistensi dan Replikasi”.

### 5.2 Load Balancing

Load balancing digunakan untuk menjalankan aplikasi dalam beberapa **instance**. Satu instance adalah satu proses aplikasi penuh yang aktif. **Load balancer**, seperti HAProxy atau Nginx, membagi permintaan ke beberapa instance.

Modul merujuk pembahasan lebih rinci ke file terpisah `web-app-py-blacksheep-load-balancing-nginx.pdf`, yang tidak terdapat dalam slide.

### 5.3 Checkpointing dan Rollback Recovery

- **Checkpointing**: menyimpan status sistem secara berkala ke media penyimpanan.
- **Rollback Recovery**: mengembalikan sistem ke checkpoint terakhir setelah kegagalan.

Dengan demikian, pemulihan dapat dimulai dari status yang tersimpan, bukan dari awal. Cara ini membantu mengurangi kehilangan data dan waktu pemrosesan.

### 5.4 Heartbeat dan Failure Detection

- **Heartbeat** adalah sinyal periodik antar-komponen untuk menunjukkan bahwa komponen masih aktif.
- **Failure Detection** menggunakan heartbeat untuk mendeteksi kegagalan. Jika sinyal tidak diterima dalam jangka waktu tertentu, komponen dianggap gagal atau tidak responsif.

Pola ini membantu sistem mengetahui kegagalan dan mengambil tindakan dengan tepat waktu.

#### Contoh pemeriksaan server

Modul menunjukkan program `check-server.py` untuk memeriksa apakah server aktif. Server dijalankan dengan `python dev.py` menggunakan Uvicorn di `localhost:44777`.

- `python check-server.py localhost 44777` menghasilkan `Server up`.
- `python check-server.py localhost 80` menghasilkan `Server down`.

Modul mencatat bahwa diagnostik lebih lanjut memerlukan program yang lebih kompleks.

#### Retry menggunakan tenacity

Library **tenacity** digunakan untuk mengelola operasi yang bergantung pada komponen eksternal, karena koneksi ke layanan luar dapat gagal.

- Jika server aktif, contoh menghasilkan pesan `Attempting to access...` lalu `Successfully accessed`.
- Jika server mati, program mencoba kembali beberapa kali (lima kali pada contoh), lalu berhenti dengan pesan kegagalan setelah beberapa percobaan, misalnya `RetryError[...]ConnectionError`.

#### Circuit Breaker

**Circuit Breaker** adalah pola desain pada microservices yang memblokir sementara panggilan ke layanan yang gagal. Tujuannya mencegah kegagalan berantai dan memberi waktu kepada layanan untuk pulih.

Pola ini bekerja seperti *state machine* dengan tiga status:

| Status | Perilaku |
|---|---|
| **Closed** | Permintaan diizinkan dan kegagalan dipantau. Jika jumlah kegagalan mencapai ambang batas, status berubah menjadi open. |
| **Open** | Permintaan langsung ditolak agar layanan memiliki waktu untuk pulih. |
| **Half-open** | Setelah batas waktu, sejumlah kecil permintaan diizinkan untuk menguji pemulihan. Jika berhasil, status kembali ke closed; jika gagal, kembali ke open. |

Contoh Python menggunakan library `pybreaker`:

```python
import pybreaker
import requests

breaker = pybreaker.CircuitBreaker(fail_max=4, reset_timeout=20)

@breaker
def make_http_request(url):
    print(f"Attempting to access {url}...")
    response = requests.get(url)
    response.raise_for_status()  # exception untuk status 4xx atau 5xx
    print(f"Successfully accessed {url}")
    return response.text

try:
    content = make_http_request("http://localhost:44777")
    # Process the content
except Exception as e:
    print(e)
```

Penjelasan:

- `fail_max=4`: setelah empat kegagalan, Circuit Breaker berpindah ke status open.
- `reset_timeout=20`: setelah 20 detik, status menjadi half-open untuk menguji layanan.
- `@breaker`: decorator yang membungkus fungsi agar kegagalannya dipantau.
- `raise_for_status()`: memunculkan exception jika respons HTTP menunjukkan status buruk (4xx atau 5xx), sehingga kegagalan dapat dihitung.
- Jika server mati, contoh dapat menampilkan `Connection refused (Errno 111)`; jika server hidup, muncul `Successfully accessed`.

### 5.5 Consensus dan Coordination

- **Consensus** adalah proses ketika beberapa node menyepakati satu nilai, status, atau urutan operasi, meskipun terdapat kegagalan atau keterlambatan jaringan.
- **Coordination** adalah praktik yang lebih luas untuk mengelola interaksi antar-node agar dapat bekerja sama secara efektif dan konsisten. Coordination sering menggunakan consensus sebagai salah satu komponennya.

Contoh penerapan dalam modul:

- **Database leader election**: cluster database dengan replikasi memilih leader untuk menangani write. Algoritma consensus seperti Raft dapat digunakan.
- **Cloud storage**, seperti Google Drive: perubahan file di-commit ke mayoritas server. Setelah mayoritas setuju, pembaruan dianggap final dan kemudian direplikasi ke semua server.
- **Cryptocurrency/blockchain**: consensus digunakan untuk validasi dan persetujuan transaksi.

## 6. Actor Model untuk Supervisi Proses

Dalam **Actor Model**, setiap actor memiliki mailbox sendiri. Actor berkomunikasi dengan mengirim pesan ke mailbox actor lain, bukan dengan berbagi data secara langsung.

Diagram hierarki supervisor dalam modul memperlihatkan susunan:

**Top-level supervisor → supervisor → actor (worker)**

Supervisor memeriksa apakah actor masih aktif. Jika actor gagal, supervisor dapat memulai ulang (*restart*) actor tersebut. Dengan pola **Supervisor-Worker**, kegagalan satu actor dapat ditangani oleh supervisornya tanpa menjatuhkan seluruh sistem.

## 7. Implementasi Fault Tolerance pada Berbagai Bahasa dan Peranti

| Bahasa/peranti | Dukungan yang disebutkan dalam modul |
|---|---|
| **Erlang/Elixir** | Dukungan bawaan untuk Actor Model dan Supervisor-Worker. |
| **Pony** | Bahasa yang secara khusus berfokus pada Actor Model (`ponylang.io`). |
| **Python** | Library pihak ketiga, seperti Xoscar (`xoscar.dev`), tenacity, dan Circuit Breaker (`pybreaker`). |
| **Java** | Apache Pekko untuk Actor Model dan Supervisor-Worker, serta Resilience4j untuk fault tolerance. |
| **Bahasa lain** | Umumnya menggunakan library pihak ketiga; contoh tempat mencari library adalah npm untuk JavaScript dan crates.io untuk Rust. |

## 8. Poin Penting untuk UTS/UAS

1. Pahami definisi fault tolerance dan jenis sistem yang membutuhkannya: high availability, mission-critical, dan life-critical.
2. Hafalkan empat jenis fault: Transient, Intermittent, Permanent, dan Byzantine, beserta contoh yang tersedia.
3. Kenali tiga sumber fault: Hardware, Software, dan Communication.
4. Hafalkan lima teknik fault tolerance: Redundancy/Replication, Load Balancing, Checkpointing/Rollback, Heartbeat/Failure Detection, dan Consensus/Coordination.
5. Pahami single point of failure dan cara menguranginya melalui redundansi.
6. Bedakan checkpointing (menyimpan status) dengan rollback recovery (kembali ke status tersimpan).
7. Pahami heartbeat: jika tidak diterima selama jangka waktu tertentu, komponen dapat dianggap gagal.
8. Bedakan **retry** (mencoba ulang permintaan) dan **Circuit Breaker** (memblokir sementara untuk mencegah kegagalan berantai).
9. Hafalkan tiga status Circuit Breaker: closed, open, dan half-open; pahami `fail_max=4` dan `reset_timeout=20`.
10. Pahami consensus dan contoh penggunaannya pada Raft, cloud storage, serta blockchain.
11. Pahami Actor Model: actor berkomunikasi melalui mailbox dan supervisor dapat me-restart actor yang gagal.
12. Kenali dukungan bahasa yang disebutkan: Erlang/Elixir (bawaan), Pony (berfokus pada actor), Python (Xoscar, tenacity, pybreaker), serta Java (Pekko dan Resilience4j).
