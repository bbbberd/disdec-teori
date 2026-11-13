# Rangkuman MATERI-6: Distributed File System

## Pengertian File System

File system merupakan metode sistem operasi untuk mengelola dan mengatur file pada media penyimpanan.

Contoh FS:

* *Linux*: ext4, XFS, btrfs
* *Windows*: Fat32, NTFS
* *MacOs*: APFS

Alur akses: Aplikasi (User Space) -> System call interface -> virtual file system (VFS) -> Kernel driver -> Hardware (Media penyimpanan).

Hierarki FS: Pengaturan tata letak direktori dan berkas pada OS (mengacu pada *FileSystem Hierarchy Standard* / FHS seperti `/bin`, `/etc`, `/home`, `/var`, dll.).

## Distributed File System

***Definisi konseptual***: Sistem abstraksi terkoordinasi dari sekumpulan media penyimpanan yang menutupi heterogenitas, kegagalan (failures), dan konkurensi sehingga menghasilkan jaringan penyimpanan yang terintegrasi, andal, dan berkinerja tinggi.

***Karakteristik utama***: Terdiri dari banyak media penyimpanan fisik, tetapi dilihat oleh pengguna sebagai satu media penyimpanan tunggal.

## Perbandingan Arsitektur Penyimpanan

* **DAS (Direct-Attached Storage)**: Penyimpanan yang terhubung langsung ke satu komputer/server.

* **NAS (Network-Attached Storage)**: Penyimpanan bersama (*shared storage*) melalui jaringan standar (TCP/IP), berbasis level berkas (*file system*), dan lebih mudah dikelola.

* **SAN (Storage Area Network)**: Penyimpanan bersama melalui jaringan khusus (*dedicated network*), berbasis blok (*block storage*), berkinerja sangat tinggi namun mahal.

* **DFS**: Lebih mengarah pada pendekatan DAS di setiap *node* yang diabstraksi secara software menjadi namespace global.

## Contoh DFS yang Umum Digunakan

* **Google File System (GFS) & Colossus**: Digunakan di infrastruktur Google (YouTube, Gmail, Google Docs).

* **HDFS (Hadoop Distributed File System)**: Bagian utama dari ekosistem Apache Hadoop.

* **CephFS & GlusterFS**: Sistem penyimpanan terdistribusi open-source yang populer.

* **Lustre**: Sistem berkas paralel berskala besar untuk komputasi performa tinggi (HPC).

## HDFS (Hadoop Distributed File System)

*Open-source*, *scalable*, terdistribusi, dan *fault-tolerant* yang dirancang untuk mengelola dataset berukuran raksasa di atas *commodity hardware* (perangkat keras murah dan diproduksi massal).

Komponen Utama Arsitektur HDFS:

1. **NameNode (Master)**: Mengelola metadata sistem berkas (nama file, lokasi blok, status replika).

2. **DataNodes (Slaves)**: Menyimpan dan mengambil blok data aktual sesuai instruksi *NameNode*.

Mekanisme Kerja: File dipecah menjadi blok-blok besar, didistribusikan ke berbagai *node* di dalam *rack*, lalu direplikasikan untuk mencapai *high-availability*.

## Cloud Storage dan Hubungannya dengan DFS

**Pengertian Cloud Storage**: Layanan penyimpanan daring yang memungkinkan pengguna menyimpan serta mengakses data di server jarak jauh melalui internet. Contoh: Amazon S3, Azure Blob Storage, Google Cloud Storage (GCS).

**Relasi dengan DFS**: *Cloud storage* bertindak sebagai penyedia media penyimpanan fisik/mentah, sementara DFS (seperti HDFS) mengatur pengorganisasian, namespace, dan manajemen datanya (misalnya integrasi HDFS dengan penyimpanan berbasis S3 melalui konfigurasi `core-site.xml`).

Tipe Cloud Storage:

**Block Storage**: Latensi rendah, cocok untuk data terstruktur (transport: FC/iSCSI).

**File Storage**: Berbasis direktori/berkas (protokol NFS/SMB) untuk file sharing*.

**Object Storage**: Skalabilitas tanpa batas, diakses via HTTP/REST (contoh: Amazon S3, MinIO/Silo, Ceph, CubeFS).
