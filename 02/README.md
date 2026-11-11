# Rangkuman MATERI-2: Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

## 1. Konsep Dasar Proses & Manajemen Proses

**Proses**: Abstraksi program yang sedang dieksekusi oleh CPU, mencakup alokasi memori, instruksi, dan thread. Manajemen proses meliputi 3 aktivitas utama:

* ***Process Creation***: Membuat proses baru dan mengalokasikan sumber daya (PCB, register, stack).

* ***Process Migration***: Memindahkan proses yang sedang berjalan antar-node/CPU untuk menyeimbangkan beban (load balancing).

* ***Process Monitoring***: Melacak status, penggunaan CPU, memori, dan performa proses.

```
  +------------------+     +-------------------+     +-------------------+
  | Create Process   | --> | Allocate Resources| --> | Initialize Process|
  +------------------+     +-------------------+     +-------------------+
                                                               |
                                                               v
                                                     +--------------------+
                                                     | New Process Ready  |
                                                     +--------------------+
```

## 2. Arsitektur dan Konsep

### Komunikasi antar node pada sistem terdistribusi

```
       +-------------------+                  +-------------------+
       |      Node A       | -- Request ----->|      Node B       |
       |  (App/Comm/DB)    | <-- Response --- |  (App/Comm/DB)    |
       +-------------------+                  +-------------------+
                 ^                              ^
                 |         +---------+          |
                 +-------->| NETWORK |<---------+
                           +---------+
                 ^                              ^
                 |                              |
       +-------------------+                  +-------------------+
       |      Node C       |                  |      Node D       |
       |  (Publish Event)  |----------------->|  (Subscribe Notif)|
       +-------------------+                  +-------------------+
```

### Model komunikasi pesan pub/sub (Publish-Subscribe)

```
  [ Publisher ] ----> ( msg ) ----> [ TOPIC / MQ ] ----> ( msg ) ----> [ Subscriber 1 ]
                                     ^          |
                                     |          +------> ( msg ) ----> [ Subscriber 2 ]
  [ Publisher ] ----> ( msg ) -------+
```

### IPC (Inter-Process Communication)

```
  +--------------------+
  |     Process A      | ---> Menulis data ke memori bersama
  +--------------------+
  |   Shared Memory    | 
  +--------------------+
  |     Process B      | ---> Membaca data dari memori bersama
  +--------------------+
  |       Kernel       |
  +--------------------+
```

### Message Passing Interface (MPI)

```
    ( Process 0 )        ( Process 1 )        ( Process 2 )        ( Process 3 )
           \                                                           ^
            \---------------------------------------------------------/
                    Message Passing Interface (e.g., OpenMPI)
             ---------------------------------------------------------
                     High Speed Network (e.g., Intel Omni-Path)
```

### Remote Procedure Call (RPC) 

```
    +--------+   Pemanggilan    +--------+
    | Klien  |   Prosedur       | Server |
    +--------+                  +--------+
        |                           |
        v                           v
    +--------+     Hasil        +--------+
    |  Stub  | ---------------> |  Stub  |
    +--------+                  +--------+
```

### Message-Oriented Middleware (MOM)

```
  +--------+        +-----------------------------------+        +--------+
  | Client |----->  |           Messaging Provider      |  ----->| Client |
  | (Send) |  Msg1  | [ Destination (Queue / Topic) ]   |  Msg1  | (Recv) |
  +--------+        +-----------------------------------+        +--------+
```

### Studi Kasus Arsitektur Microservices

```
                       +------------------+
                       |  User Interface  |
                       +------------------+
                          /    |     |    \
                         v     v     v     v
                  [Microservice] ... [Microservice]
                        |                  |
                   [Database]         [Database]
```

## 3. Komunikasi Antar Proses (IPC) di Sistem Terdistribusi

Karena tidka ada shared memory antar-node yang terpisah secara fisik, IPC khusus jaringan mmutlak diperlukan.

1. **Message Passing**: Mengirim dan menerima pesan eksplisit antar-proses (Contoh standar: MPI / Message Passing Interface)

2. **RPC (Remote Procedure Call)**: Memungkinkan suatu program memanggil prosedur/fungsi di komputer lain seolah-olah memanggil fungsi lokal (Contoh: gRPC).

3. **MOM (Message Oriented Middleware)**: Pertukaran pesan secara asinkron melalui perantara *message queue* atau *broker* (Contoh protokol: AMQP, MQTT).

## 4. Sinkronisasi & Logika Clock

1. **Masalah waktu lokal**: Setiap node memiliki *clock* fisik sendiri yang rentan mengalami pergeseran (*clock drift*) akibat latensi jaringan.

2. **Lamport Logical Clock**: Menyelesaikan masalah pengurutan kejadian (*event ordering*) tanpa bergantung pada jam fisik:

    * Setiap ada kejadian lokal, nilai *logical clock* bertambah 1.

    * Pesan yang dikirim menyertakan nilai *timestamp clock* tersebut.

    * Node penerima membandingkan *clock* lokalnya dengan *clock* dari pesan, mengambil nilai terbesar, lalu menambahkan 1.

3. **Alternatif Clock Lainnya**: Vector Clocks, Plausible Clocks, Chain Clocks, Interval Tree Clocks, dll.

## 5. Jawaban Diskusi

**Materi Minggu-2**: Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

**Jenis AI**: Menggunakan Google Search saja tapi yang saya pakai jawaban AI mode.

**Prompt**: Apakah GraphQL merupakan contoh dari RPC?

**Ringkasan saya untuk penyesuaian**:

### 1. GraphQL tidak secara langsung bisa disebut RPC, karena pada dasarnya kueri dengan spesifikasi runtime untuk API berbasis HTTP. Meskipun client bisa memicu mutasi, arsitektur *GraphQL* berfokus pada manipulasi sumber daya dan skema data berbasis grafik (graph based data fecthing), bukan mengeksekusi prosedur terdistribusi seperti *gRPC* atau *XML-RPC* contohnya.

**Materi Hasil AI**:
Tidak, GraphQL bukan contoh dari RPC (Remote Procedure Call). GraphQL dan RPC adalah dua gaya arsitektur API yang berbeda.
Berikut perbedaan utamanya: [https://www.indowebsite.co.id/blog/rpc-adalah](https://www.indowebsite.co.id/blog/rpc-adalah)

- GraphQL adalah sebuah bahasa kueri (query language) dan runtime API yang berfokus pada data (menggunakan grafik data). Klien bebas menentukan bidang data (fields) apa saja yang ingin mereka ambil dalam satu kali permintaan, tanpa terpaku pada struktur prosedur di server.
[https://www.youtube.com/watch?v=GsGopNsOKEU&t=517](https://www.youtube.com/watch?v=GsGopNsOKEU&t=517)

- RPC adalah gaya arsitektur API di mana klien memanggil prosedur, fungsi, atau aksi tertentu di server jarak jauh secara langsung (seolah-olah menjalankan fungsi lokal), seperti pada JSON-RPC, XML-RPC, atau gRPC. [https://apisyouwonthate.com/blog/understanding-rpc-rest-and-graphql/](https://apisyouwonthate.com/blog/understanding-rpc-rest-and-graphql/)

Meskipun keduanya sering digunakan sebagai alternatif dari REST, GraphQL berpusat pada data graph, sedangkan RPC berpusat pada eksekusi aksi/fungsi. [https://www.youtube.com/watch?v=Wet__R9-6jQ&t=12](https://www.youtube.com/watch?v=Wet__R9-6jQ&t=12) 

Apakah Anda ingin tahu lebih detail mengenai perbandingan performa antara GraphQL dan gRPC (RPC), atau kapan waktu terbaik menggunakan masing-masing arsitektur tersebut?

### 2. REST dan RPC adalah arsitektur yang berlawanan, REST (CRUD klasik, dimana URI merepresentasikan objek data, contoh: http:<website>/user=1) *bukan* merupakan contoh RPC (yg fokus pada prosedur/aksi contoh: getUser(id))

### 3. Telemetri bisa etis jika implementasi, transparansi dan jenis data yang dikumpulkan etis. Karena penting juga pada sistem terdistribusi untuk melacak error, performa, menjaga keandalan sistem supaya layanan optimal, mematuhi regulasi negara (GDPR), sistem anonimasi, dsb. Telemetri menjadi sebuah *masalah* adalah ketika pengumpulan data dilakukan tanpa izin pengguna (consent), tidak memberi opsi untuk menolak (opt-out), dan juga untuk tujuan yang merugikan.
