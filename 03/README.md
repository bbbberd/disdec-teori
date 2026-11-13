# Rangkuman MATERI-3: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

## 1. Konsep Dasar Sinkronisasi
* **Definisi**: Koordinasi proses, data, dan sumber daya di berbagai *nodes* untuk memastikan operasi yang konsisten dan andal, serta mencegah konflik data (*data race*).

* **Mengapa sinkronisasi penting**:

    * Menjaga **integritas data** (mencegah *data race & race conditions*).

    * Sinkronisasi *state* aplikasi di semua *nodes* (contoh: *multiplayer game*, keranjang belanja *e-commerce*).

    * Pengelolaan sumber daya, *load balancing*, mencegah *deadlock*, dan mendukung *scalable operation*.

## 2. Arsitektur

### Distribusi Deadlock pada Jaringan

```
( Process 1 ) <--- Waiting for --- [ R1 (Assigned to Process 1) ]
         |       \
         |        \--- Waiting for ---> [ R2 ] <--- Assigned to --- ( Process 2 )
         |                                                          |
         \--- Waiting for ------------------------------------------/---> [ R3 ]         
```

### Arsitektur GPS & NTP Time Server

```
[ Satelit GPS ] ---> ( /dev/ttyACM0 ) ---> [ GPSD ] ---> [ RAM (Shared Memory) ]
                                                                |
[ LAN Jaringan ] <--- NTP / PTP Server <------------------------+
```

## 3. Data Race vs Race Condition

* **Data Race**: Konflik akses memori bersama secara konkuren di tingkat low-level (CPU/memori fisik) di mana minimal ada satu operasi *write* tanpa mekanisme *lock*.

* **Race Condition**: Masalah logika bisnis (*high-level*) di mana hasil akhir program bergantung pada urutan eksekusi (*timing*) yang tidak dapat diprediksi (contoh kasus *double spending* atau saldo bank minus).

## 4. Sinkronisasi Waktu fisik

* **Tujuan**: Menyelaraskan *real-time clock* antar-komputer dalam jaringan.

* **Standar Waktu Global**:

    * **UTC** (*Universal Coordinated Time*): Standar universal yang didefinisikan oleh ITU.
    
    * **TAI** (*Temps Atomique International*): Waktu atom international berbasis jam atom.

* **Sumber & Protokol**:

    * Sumber waktu utama berasal dar ijam atom satelit GPS (Medium Earth Orbit / ISS / Ground Control).

    * **NTP** (*Network Time Protocol* - RFC 5905): Protokol standar internet untuk sinkronisasi waktu lewat jaringan. Implementasi populer di Linux meliputi `chrony`.

    * **PTP** (*precision Time Protocol* - IEEE 1588): Protokol dengan presisi sangat tinggi (sub-mikrodetik) yang membutuhkan dukungan perangkat keras *hardware timestamping* pada NIC.

## 5. Sinkronisasi Waktu Logis

**Vector Clocks**: Metode pelacakan urutan sebab-akibat (*casual order*) menggunakan array ukuran tetap berdasarkan jumlah proses.

* *Aturan Update*: Setiap proses menambah nilai counternya saat terjadi *event*, lalu mencatat nilai maksimum elemen-wise saat menerima pesan dari proses lain.

* *Aturan Perbandingan*: Event ***A*** terjadi sebelum ***B*** jika semau komponen clock ***A <= B*** dan ada minimal satu komponen yang lebih kecil.

## 6. Tantangan Mutual Exclusion & Critical Section

**Critical Section (CS)**: Bagian kode yang mengakses sumber daya bersama. Prinsip utamanya: *Mutual Exclusion, Progress, Bounded Waiting* dan *Fairness*.

**Pendekatan solusi**:

* **Centralized Algorithm**: Menggunakan satu node koordinator (Sederhana, tetapi rentan *Single Point of Failure*).

* **Ricart-Agrawala**: Algoritma terdistribusi penuh berbasis *timestamp* dan *reply* pesan antar semua proses (*fault-tolerant*, namun *overhead* pesan tinggi).

* **Token-Based Algorithm**: Berbasis peredaran token unik (hanya 1 pesan per entri CS, namun sulit mendeteksi token hilang).

## 7. Pemilihan Koordinator (*Coordinator Election*)

* Dibutuhkan ketika koordinator lama mengalami kegagalan atau node baru bergabung untuk mengelola keputusan global.

* **Algoritma**: *Bully Algorithm*, *Ring Algorithm*, serta algoritma berbasis konsensus seperti **Raft** atau **Paxos**.

* **Contoh Industri**: Apache ZooKeeper (menggunakan Zab) dan Kubernetes (menggunakan Raft via *etcd*).
