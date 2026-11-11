# Rangkuman: Pengenalan Sistem Terdistribusi dan Terdesentralisasi


## 1. Konsep Dasar & Pengertian

### ***Sistem Terdistribusi***

Sekumpulan komputer independen yang terhubung melalui jaringan sebagai satu sistem tunggal yang koheren (Tanenbaum & van Steen, 2007).

* Fokus: Pembagian beban kerja (workload distribution).

* Karakteristik: Transparansi lokasi/akses, skalabilitas, fault-tolerance, kinerja tingga, dan heterogenitas.

### ***Sistem Terdesentralisasi***

Sistem tanpa titik kontrol tunggal (no single point of failure). Pengambilan keputusan dilakukan secara kolektif melalui mekanisme konsensus.
    
* Fokus: Pembagian kontrol dan tata kelola (control distribution).

```
+---------------------------------------------------------------+
| Centralized       | Distributed           | Decentralized     |
| 1 Titik Kontrol   | Pembagian Beban Kerja | Pembagian Kontrol |
+---------------------------------------------------------------+
```


## 2. Visualisasi Arsitektur Sistem

### Arsitektur sistem terdistribusi web

```
+------------+RestAPI+-------------+
| Mobile App |------>|             |
+------------+       | API GATEWAY |RestAPI +-----------------+     +------------+
                     |             |-+----->| Account Service |---->| Account DB |
                     |             | |      +-----------------+     +------------+
                     +-------------+ |                A
                                     |                |RestAPI
                     +-------------+ |                +--------------------------------+
                     |             | |                v                                |
+---------+ HTTP     | STOREFRONT  | |RestAPI +-------------------+   +--------------+ |
| Browser |--------->|   WEBAPP    | +------->| Inventory Service |-->| Inventory DB | |
+---------+          |             | |        +-------------------+   +--------------+ |
                     +-------------+ |                                                 |
                               |     |                                                 |
                               |     |RestAPI                                          |
                               |     +------->+------------------+    +-------------+  |
                               +------------->| Shipping Service |--->| Shipping DB |  |
                               |              +------------------+    +-------------+  |
                               |                                                       |
                               +-------------------------------------------------------+
```

### Arsitektur sistem Dasar

```
+---------+
| Laptop  |----\
+---------+     \     +----------+       +---------------+
+---------+      ---> |          |       |    SERVER     |
| HP/Mob  |---------> | INTERNET |====== | +-----------+ |
+---------+      ---> |          |       | | Database  | |
+---------+     /     +----------+       | +-----------+ |
| Desktop |----/                         +---------------+
+---------+
 (CLIENTS)
```

### Arsitektur Peer-to-Peer (P2P)

```
    [ Node A ]-----------[ Node B ]
        |   \             /   |
        |    \           /    |
        |     [ Node E ]      |
        |    /           \    |
        |   /             \   |
    [ Node D ]-----------[ Node C ]

 (Komunikasi langsung antar-peer tanpa server pusat. Contoh: BitTorrent, Bitcoin)
```

### Arsitektur Hybrid

```
  [ Client 1 ]----\                  /----[ Client 3 ]
                   --> [SUPERNODE 1]<--
  [ Client 2 ]----/         ||        \----[ Client 4 ]
                            || (Inter-Supernode Link)
  [ Client 5 ]----\         ||        /----[ Client 7 ]
                   --> [SUPERNODE 2]<--
  [ Client 6 ]----/                  \----[ Client 8 ]

 (P2P terstruktur menggunakan Supernode sebagai perantara koneksi/layanan)
```

### Topologi Paul Baran

```
   Centralized (A)          Decentralized (B)          Distributed (C)

       \ | /                  o--o   o--o                 o---o---o
      -- O --                /    \ /    \               / \ / \ / \
       / | \                o------O------o             o---o---o---o
                           /      / \      \             \ / \ / \ /
                          o      o   o      o             o---o---o
  (1 Pusat Utama)        (Beberapa Pusat Lokal)     (Jaringan Mesh Setara)
```

## 3. Teorema CAP (Brewer's Theorem)

Pada distribusi data store, sistem tidak mungkin memenuhi ketiga properti secara bersamaan saat terjadi jaringan terputus (partition tolerance):

    * ***Consistency (C)***: Setiap operasi baca mengembalikan data penulisan terbaru atau error.
    * ***Availability (A)***: Setiap request non-failing node mendapat respons non-error (meski bukan data terbaru).
    * ***Partition Tolerance (P)***: Sistem tetap berfungsi meski komunikasi antar-node terputus.

```
                  .
                 / \
                /   \
               / CAP \
              /  Tri  \
             /_________\
            /           \
           / Consistency \
          /_______________\
         /                 \
        /   Availability    \
       /_____________________\
      /  Partition Tolerance  \
     /_________________________\
```

### Trade-off saat terjadi partition (p):
    * ***AP (Availability + Partition Tolerance)***: Mengorbankan konsistensi demi ketersediaan (Data lama mungkin terbaca). Contoh: MongoDB (opsi read preference).
    * ***CP (Consistency + Partition Tolerance)***: Mengorbankan ketersediaan demi konsistensi data (Request ditolak jika data belum konsisten). Contoh: Apache Cassandra.

## 4. Konsistensi Data & Tantangan
1. Model Konsistensi:
    * *Strong Consistency*: Data selalu sama seketika di semua node (Contoh: Sistem Perbankan).
    * *Eventual Consistency*: Data akan konsisten secara bertahap seiring waktu (Contoh: Feeds Media Sosial).

2. Tantangan Sistem:
    * *Latency & Throughput*: Waktu proses vs volume data.
    * *Keamanan*: Serangan seperti *DDoS* dan *Sybil Attack* (pembuatan ribuan identitas palsu untuk mendominasi voting/node).
    * *Kompleksitas & Governance*: Manajemen node dan tata kelola terdesentralisasi.

## 5. Studi Kasus & Tren Masa Depan

1. Studi kasus:

    * *Terdistribusi*: Google File System (GFS), HDFS, Apache Kafka, Netflix, YouTube, TikTok.
    * *Terdesentralisasi*: Blockchain (Bitcoin, Ethereum), DNS, BitTorrent.

2. Tren Masa Depan:
    * Cloud-Native (Docker, Kubernetes)
    * Edge & Fog Computing
    * Web3 & DeFi
    * Federated Learning (AI Terdistribusi)

## 6. Diskusi dan Tugas

### Mengapa perkembangan mengarah ke distribusi dan desentralisasi?

1. **Skalabilitas dan Pertumbuhan Data (Scalability)**

    * Pertumbuhan data dan jumlah pengguna internet berkembang secara eksplosif.
    * Satu server terpusat (monolithic) memiliki keterbatasan fisik (CPU/RAM) dan tidak mampu menangani jutaan atau miliaran permintaan serentak dari seluruh dunia.
    * Membutuhkan penambahan kapasitas secara horizontal (menambah device) yaitu sistem terdistribusi.

2. **Ketahanan terhadap kegagalan (Fault tolerance & High Availability)**

    * Pada sistem terpusat, jika server utama mengalami gangguan (crash, bencana alam, dsb), seluruh layanan mati (single point of failure).

    * Sistem terdistribusi dan terdesentralisasi dirancang agar operasi tetap berjalan normal 14/7 meski beberapa sistem mengalami kegagalan.

3. **Mengeliminasi Single Point of Failure & Pembagian Kontrol**

    * Sistem terdesentralisasi menghilangkan ketergantungan pada satu pihak pusat/otoritas tunggal.
    * Keputusan di kelola secara kolektif melalui mekanisme konsensus, meningkatkan transparansi dan keandalan sistem misalnya pada blockchain dan DNS.

4. **Kinerja dan Pengurangan Latensi (Performance & Latency)**

    * Waktu tanggap (latency) dapat di pangkas secara signifikan untuk layanan real-time seperti streaming service atau media sosial karena membagi beban kerja dan menempatkan node/server lebih dekat dengan lokasi pengguna (seperti pada Cloud-Native, Edge, atau Fog Computing).

### Contoh sehari-hari yang terasa

1. **Layanan Streaming Video (YouTube, Netflix, Twitch)** karena video tidak disimpan di satu server pusat di luar negri, melainkan didistribusikan ke ribuan edge server atau cdn (content delivery network) terdekat di wilayah pengguna sehingga real-time per detik tanpa harus download.

2. **Aplikasi pesan instan & media sosial (X/Twitter, Instragram, TitTok, dsb)** karena menggunakan teknik load balancing dan sharding ke banyak server agar tidak down saat trafik melonjak karena spam status, post, story, dan dsb, dari pengguna.

3. **Domain Name System (DNS) di internet** karena DNS bekerja secara hierarkis dan tersebar di seluruh dunia tanpa ada satu komputer pun yang mengontrol seluruh direktori internet secara tunggal.
