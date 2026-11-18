# Rangkuman 06: Distributed File System (DFS)

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

## 1. Pengertian File System (FS)

**File system (FS)** adalah metode yang digunakan sistem operasi untuk mengatur file pada media penyimpanan, seperti hard disk, flash drive, dan SSD. Istilahnya dapat ditulis *file system*, *filesystem*, atau disingkat FS/fs.

Contoh file system pada beberapa sistem operasi:

| Sistem operasi | Contoh file system |
|---|---|
| Linux | ext4, XFS, btrfs, ReiserFS |
| DOS/Windows | FAT16, FAT32, NTFS |
| Mac | APFS (*Apple File System*) |

### File system dan hirarki file system

File system diakses melalui **file system driver** yang dikelola kernel. Kernel menyediakan **virtual file system** agar aplikasi di *user space* dapat mengakses file melalui antarmuka yang seragam.

File system berbeda dari **hirarki file system**. Hirarki file system adalah susunan file dan direktori pada sistem operasi. Pengguna melihat susunan tersebut tanpa perlu mengetahui jenis file system yang berada di bawahnya.

### Alur kerja file system

Diagram arsitektur kernel Linux dalam modul menunjukkan lapisan berikut:

1. **User space/applications**: utilitas sistem, shell/CLI, dan aplikasi GUI.
2. **System call interface**: antarmuka panggilan sistem, seperti `read()` dan `write()`.
3. **Kernel space**: mencakup manajemen proses dan memori (MMU), IPC, file system (misalnya ext4 dan XFS), network stack (TCP/IP), dan device driver.
4. **Hardware**: CPU, RAM, media penyimpanan, dan antarmuka jaringan.

Alur sederhananya:

**Aplikasi → System Call Interface → virtual file system → driver kernel → hardware/media penyimpanan.**

Modul juga merujuk diagram yang lebih lengkap berdasarkan Linux kernel 6 dari Linux Storage Stack Diagram (thomas-krenn.com).

### Hirarki file system di Linux

Modul menampilkan hasil `tree -D -L 1 /` dan merujuk **Filesystem Hierarchy Standard (FHS)**. FHS adalah standar penempatan file dan direktori pada sistem UNIX-like agar aplikasi dan perangkat bantu dapat saling kompatibel.

Direktori yang terlihat dalam slide antara lain `/bin`, `/boot`, `/dev`, `/etc`, `/home`, `/lib`, `/lib64`, `/lost+found`, `/mnt`, `/opt`, `/proc`, `/root`, `/run`, `/sbin`, `/srv`, `/sys`, `/tmp`, `/usr`, dan `/var`.

| Direktori | Fungsi menurut modul |
|---|---|
| `/bin` | Program perintah penting untuk semua pengguna. |
| `/boot` | File statis untuk boot loader. |
| `/dev` | File perangkat. |
| `/etc` | Konfigurasi sistem khusus host. |
| `/home` | Direktori home pengguna (opsional). |
| `/lib` | Shared libraries dan modul kernel yang penting. |

## 2. Pengertian Distributed File System (DFS)

**Distributed File System (DFS)** bukan sekadar file system yang tersebar di beberapa mesin. DFS adalah abstraksi terkoordinasi atas sekumpulan media penyimpanan. DFS menyembunyikan perbedaan perangkat, lokasi, kegagalan, dan akses bersamaan sehingga menyediakan penyimpanan terintegrasi yang andal dan berkinerja tinggi.

Media penyimpanan DFS berada di beberapa node, tetapi bagi pengguna terlihat seperti satu media penyimpanan.

### DFS, DAS, NAS, dan SAN

| Jenis | Penjelasan |
|---|---|
| **DAS (Direct-Attached Storage)** | Penyimpanan yang terhubung langsung ke mesin. Contoh pada modul adalah partisi seperti `/dev/nvme0n1p2` yang di-*mount* ke `/`, `/home`, atau `/boot/efi`. |
| **NAS (Network-Attached Storage)** | Penyimpanan bersama melalui jaringan umum dan berbasis file system; pengelolaannya relatif mudah. |
| **SAN (Storage Area Network)** | Penyimpanan bersama melalui jaringan khusus (*dedicated*), berbasis blok, cepat tetapi mahal, dan menggunakan SAN switch. |
| **DFS** | Abstraksi perangkat lunak yang mengelola data sebagai file dan direktori yang dapat didistribusikan ke banyak node. |

Poin tambahan dari modul:

- SAN menyediakan sekumpulan perangkat blok yang dapat dihubungkan langsung ke server dan diformat menggunakan file system sendiri.
- SAN secara konsep dapat digunakan sebagai penyimpanan untuk DFS, tetapi modul menyatakan hal tersebut tidak disarankan.
- SAN umum digunakan sebagai backend database skala besar, penyuntingan video/audio, dan virtualisasi skala besar.
- DFS lebih mengarah pada penggunaan DAS di setiap node.

## 3. Berbagai DFS yang Umum

| DFS | Keterangan dalam modul |
|---|---|
| **Google File System (GFS)** | — |
| **Colossus** | Pengganti Google File System; digunakan untuk layanan seperti YouTube, Gmail, dan Google Docs. |
| **CephFS** | `ceph.io` |
| **Gluster** | `gluster.org` |
| **HDFS (Hadoop Distributed File System)** | `hadoop.apache.org` |
| **Lustre** | `lustre.org` |

## 4. Cara Kerja DFS

DFS dapat menyediakan **Global Namespace**, yaitu satu ruang nama terpadu yang digunakan pengguna dan aplikasi untuk mengakses data.

Diagram modul menggabungkan beberapa namespace dari lokasi berbeda:

- **Data center**: Namespace 1 dan 2, misalnya NAS 1 dan NAS 2.
- **Lokasi remote**: Namespace 3 dan 4, misalnya Linux NFS dan Windows SMB.
- **Public cloud**: Namespace 5.

Dengan Global Namespace, media penyimpanan yang berbeda jenis dan lokasi ditampilkan sebagai satu ruang penyimpanan terpadu.

## 5. Studi Kasus: HDFS (Hadoop Distributed File System)

### 5.1 Pengertian

**HDFS** adalah file system open source yang skalabel, terdistribusi, dan fault-tolerant. HDFS dirancang untuk mengelola serta menyimpan dataset besar pada cluster **commodity hardware**.

**Commodity hardware** adalah perangkat keras yang diproduksi massal, murah, mudah diganti, dan tersedia dari banyak vendor.

HDFS merupakan komponen utama Apache Hadoop untuk pemrosesan data skala besar. HDFS membagi file menjadi blok, mendistribusikan blok ke beberapa server, dan mereplikasinya untuk mendukung ketersediaan tinggi.

Persyaratan versi yang disebutkan modul:

- Apache Hadoop 3.5.0 dikompilasi dengan JDK 17.
- Server berjalan menggunakan Java 17.
- Client dapat menggunakan Java 17 atau 21.

### 5.2 Arsitektur

#### Diagram Master-Slave

- **Master**: NameNode dan Resource Manager.
- **Slave**: beberapa node yang masing-masing memiliki DataNode, Node Manager, serta Map dan Reduce.

#### Komponen arsitektur HDFS

| Komponen | Peran |
|---|---|
| **NameNode** | Menyimpan metadata, seperti nama file dan jumlah replika. Contoh pada diagram `/home/foo/data, 3, ...` menunjukkan file memiliki tiga replika. |
| **DataNodes** | Menyimpan blok data yang tersebar di beberapa node dan rack. |
| **Client** | Menghubungi NameNode untuk operasi metadata, tetapi membaca dan menulis data secara langsung ke DataNode. |
| **Replication** | Menyalin blok antar-DataNode, termasuk antar-rack. |

Alur sederhananya: client meminta metadata kepada NameNode; NameNode mengatur operasi blok pada DataNode; client membaca atau menulis data ke DataNode; lalu blok direplikasi.

### 5.3 Praktik

Modul meminta mahasiswa mempraktikkan HDFS menggunakan dokumen “Mulai Menggunakan Apache Hadoop - HDFS” di EL-UTDI.

Persyaratan perangkat lunak yang ditampilkan:

- JDK 17 dan/atau 21: Java 17 untuk server, client dapat menggunakan 17 atau 21.
- Apache Hadoop 3.5.0 (rilis 2 April 2026).
- `pdsh` dan `ssh`.
- `sshd` harus berjalan. Contoh perintah:

```sh
sudo dinitctl start sshd
```

Untuk sistem dengan systemd, modul mencantumkan:

```sh
sudo systemctl start ssh
```

Hadoop dapat diunduh dari `hadoop.apache.org/releases.html`.

## 6. Pengertian Cloud Storage

**Cloud storage** adalah layanan online untuk menyimpan dan mengakses data pada server jarak jauh melalui jaringan, biasanya Internet, bukan hanya pada hard drive lokal.

Data dikelola secara remote dalam suatu pool logis. Secara fisik, penyimpanan dapat terdiri atas beberapa server di satu atau beberapa lokasi. Infrastruktur fisiknya dimiliki dan dikelola oleh penyedia layanan cloud.

Contoh:

- Amazon S3.
- Azure Blob Storage.
- Google Cloud Storage (GCS).

## 7. Relasi Cloud Storage dengan DFS

Perbedaan ringkasnya:

- **Cloud storage** menyediakan tempat atau media untuk menyimpan data.
- **DFS** mengorganisasi dan menyediakan operasi file system untuk data.

Contohnya, S3 dapat digunakan oleh HDFS: S3 menyimpan data, sedangkan HDFS menyediakan operasi file system. Modul menunjukkan konfigurasi S3A pada `core-site.xml`:

```xml
<property>
    <name>fs.s3a.access.key</name>
    <value>YOUR_AWS_ACCESS_KEY_ID</value>
</property>
<property>
    <name>fs.s3a.secret.key</name>
    <value>YOUR_AWS_SECRET_ACCESS_KEY</value>
</property>
<property>
    <name>fs.s3a.endpoint</name>
    <value>s3.amazonaws.com</value> <!-- Or specific region endpoint -->
</property>
```

Keterangan:

- `fs.s3a.access.key`: ID kunci akses AWS.
- `fs.s3a.secret.key`: kunci rahasia yang menjadi pasangan access key.
- `fs.s3a.endpoint`: alamat endpoint layanan S3; dapat menggunakan endpoint untuk region tertentu.

Nilai `YOUR_AWS_ACCESS_KEY_ID` dan `YOUR_AWS_SECRET_ACCESS_KEY` adalah placeholder, bukan kredensial yang dapat digunakan.

## 8. Tipe Cloud Storage

Modul membagi cloud storage menjadi tiga tipe:

| Aspek | Block Storage | File Storage | Object Storage |
|---|---|---|---|
| **Transport** | FC atau iSCSI. | TCP/IP. | TCP/IP. |
| **Interface** | Direct Attached atau SAN. | NFS atau SMB. | HTTP atau REST. |
| **Use case** | Latensi rendah; sesuai untuk data terstruktur. | Kinerja baik, berbagi file, dan global file locking. | Mudah diskalakan; dapat diakses melalui LAN dan WAN. |

## 9. Studi Kasus Cloud Storage: Object Storage dan Storage Open Source

Modul menampilkan beberapa contoh berdasarkan tangkapan layar situs resmi:

| Nama | Keterangan dalam modul |
|---|---|
| **Ceph** (`ceph.com`) | Sistem penyimpanan terdistribusi open source yang menyediakan storage andal dan skalabel untuk berbagai organisasi. |
| **CubeFS** (`cubefs.io`) | Storage cloud-native open source yang mendukung akses S3, HDFS, dan POSIX. Modul menyebut penggunaannya untuk big data, AI/LLM, container, dan pemisahan storage dan compute; proyek ini berada di bawah CNCF. |
| **RustFS** (`rustfs.com`) | Menyebut dirinya sebagai pengganti MinIO dan S3 untuk multi-cloud storage. |
| **SILO / PGSTY** (`silo.pgsty.com`) | Object storage open source yang kompatibel dengan S3/MinIO dan merupakan fork MinIO yang dikelola PGSTY. S3 API menggunakan port 9000 dan console menggunakan port 9001. |
| **Rook** (`rook.io`) | Storage cloud-native open source untuk Kubernetes, dengan pengelolaan File, Block, dan Object storage. |

### Arsitektur CubeFS

| Komponen | Peran |
|---|---|
| **Master** | Pusat koordinasi. |
| **Metadata Subsystem** | Memuat Meta Node dengan Meta Partition di memori. |
| **Data Subsystem** | Memuat Data Node (Data Partition) dan Blob Node (Erasure Code Stripe). |
| **Object Subsystem** | Memuat Object Node yang diakses aplikasi melalui AWS S3 SDK. |
| **Client** | Container, VM, dan server yang menggunakan antarmuka POSIX/HDFS. |
| **Volume** | Ruang penyimpanan logis yang digunakan client. |

## 10. Poin Penting untuk UTS/UAS

1. Pahami definisi file system, contoh jenisnya pada tiap OS, serta perbedaan FS dengan hirarki FS.
2. Hafalkan alur akses file: aplikasi → system call interface → virtual file system → driver kernel → hardware.
3. Pahami DFS sebagai abstraksi terkoordinasi yang menutupi heterogenitas, kegagalan, dan konkurensi, sehingga beberapa media tampak sebagai satu storage.
4. Bedakan DAS, NAS, SAN, dan DFS. NAS menggunakan file storage melalui jaringan bersama; SAN menyediakan block storage melalui jaringan khusus; DFS adalah abstraksi perangkat lunak untuk penyimpanan di banyak node.
5. Kenali contoh DFS: GFS, Colossus, CephFS, Gluster, HDFS, dan Lustre.
6. Pahami Global Namespace dan bagaimana DFS menyatukan namespace dari beragam lokasi dan jenis penyimpanan.
7. Pahami HDFS, commodity hardware, pembagian file menjadi blok, dan replikasi.
8. Bedakan peran NameNode (metadata) dan DataNode (blok data), termasuk interaksi client dan replikasi antar-rack.
9. Pahami cloud storage dan contoh Amazon S3, Azure Blob Storage, serta GCS.
10. Bedakan peran cloud storage sebagai tempat penyimpanan dan DFS sebagai pengelola operasi file system; kenali konfigurasi S3A di `core-site.xml`.
11. Hafalkan tiga tipe cloud storage—block, file, dan object—beserta transport, interface, dan use case.
12. Kenali Ceph, CubeFS, RustFS, SILO/PGSTY, dan Rook beserta fungsi utama yang disebutkan dalam modul.
