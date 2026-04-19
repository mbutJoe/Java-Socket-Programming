**Project Chat Application**

Milestone 1 — Server Foundation
Langkah 1.1 — Basic ServerSocket
├── Buat class Server.java
├── ServerSocket listen di port 8080
├── Cetak log saat server start
└── Test: jalankan server, pastikan tidak crash

Langkah 1.2 — Terima Satu Client
├── server.accept() → return Socket
├── Baca pesan dari client via BufferedReader
├── Cetak pesan di console server
└── Test: konek manual via Telnet/terminal

Langkah 1.3 — Balas Pesan ke Client
├── Tambah PrintWriter ke client socket
├── Server echo balik pesan yang diterima
└── Test: client kirim "hello" → terima "hello" kembali



Milestone 2 — Multi Client
Langkah 2.1 — ClientHandler Thread
├── Buat class ClientHandler implements Runnable
├── Pindahkan logika baca/tulis ke dalam class ini
├── Setiap accept() → new Thread(new ClientHandler(socket)).start()
└── Test: konek 3 client sekaligus, semua bisa kirim pesan

Langkah 2.2 — Broadcast System
├── Buat List<ClientHandler> di Server (static)
├── Saat pesan masuk → loop semua handler → kirim ke semua
├── Tambah synchronized pada list agar thread-safe
└── Test: Client A kirim pesan → Client B dan C menerima

Langkah 2.3 — Auth & Username
├── Saat client konek → minta input username
├── Server simpan username di ClientHandler
├── Validasi username tidak boleh duplikat
├── Format pesan: "[username]: pesan"
└── Test: login dengan username sama → ditolak server



Milestone 3 — Room System
Langkah 3.1 — Room Manager
├── Buat class Room.java
│   ├── String roomName
│   ├── List<ClientHandler> members
│   └── method broadcast(message, sender)
├── Buat class RoomManager.java
│   ├── HashMap<String, Room> rooms
│   ├── method createRoom(name)
│   ├── method joinRoom(name, client)
│   └── method leaveRoom(name, client)
└── Test unit: buat room, tambah member, broadcast

Langkah 3.2 — Command Protocol (Chat Port 8080)
├── Definisikan format command:
│   ├── /join <roomName>   → masuk room
│   ├── /leave             → keluar room
│   ├── /rooms             → list room tersedia
│   ├── /users             → list user di room
│   └── <text>             → kirim pesan ke room
├── Buat class CommandParser.java
└── Test: client ketik /join general → masuk room general

Langkah 3.3 — Routing Pesan per Room
├── Ubah broadcast dari "ke semua" menjadi "ke member room"
├── Client yang belum join room tidak bisa kirim pesan
└── Test: Room A dan Room B terisolasi satu sama lain



Milestone 4 — File Transfer
Langkah 4.1 — File Server Terpisah (Port 8081)
├── Buat class FileServer.java
│   └── ServerSocket di port 8081 (thread terpisah)
├── Buat class FileClientHandler.java
└── Test: FileServer start bersamaan dengan Chat Server

Langkah 4.2 — Upload Protocol
├── Definisikan header file transfer:
│   ├── LINE 1: TYPE=IMAGE atau TYPE=AUDIO
│   ├── LINE 2: FILENAME=foto.jpg
│   ├── LINE 3: SIZE=204800
│   ├── LINE 4: ROOM=general
│   ├── LINE 5: SENDER=alice
│   └── LINE 6: --- (separator)
│             [raw bytes payload]
├── Implementasi di FileClientHandler:
│   ├── Baca header line by line
│   ├── Baca bytes sejumlah SIZE
│   └── Simpan file di folder server/files/
└── Test: upload gambar kecil, cek file tersimpan di server

Langkah 4.3 — Download & Notifikasi
├── Setelah file tersimpan:
│   ├── FileServer notifikasi Chat Server via internal method
│   └── Chat Server broadcast ke room: "[alice mengirim foto.jpg]"
├── Client yang ingin download:
│   ├── Konek ke port 8081
│   ├── Kirim: GET <filename>
│   └── Terima bytes → simpan lokal
└── Test: A upload gambar → B terima notifikasi → B download



Milestone 5 — JavaFX UI
Langkah 5.1 — Setup Project JavaFX
├── Tambah dependency JavaFX di pom.xml / build.gradle
├── Buat class MainApp extends Application
├── Buat file login.fxml → form username + tombol connect
└── Test: window JavaFX muncul saat program dijalankan

Langkah 5.2 — Login Screen
├── Controller: LoginController.java
├── Saat tombol Connect diklik:
│   ├── Ambil input username
│   ├── Panggil ChatClient.connect(host, port, username)
│   └── Jika berhasil → pindah ke chat screen
└── Test: login berhasil → window berganti ke chat screen

Langkah 5.3 — Chat Screen
├── Buat chat.fxml dengan layout:
│   ├── Kiri: ListView room list + ListView user list
│   ├── Tengah: ListView/TextArea untuk pesan
│   └── Bawah: TextField input + tombol Send + tombol File
├── Controller: ChatController.java
└── Test: tampilan muncul dengan benar, belum ada fungsi

Langkah 5.4 — Integrasi Socket ke UI
├── ChatClient jalan di background thread
├── Saat pesan diterima dari server:
│   └── Platform.runLater(() → { tambah ke ListView })
├── Saat tombol Send diklik:
│   └── ChatClient.sendMessage(text)
└── Test: dua instance app bisa saling chat

Langkah 5.5 — Integrasi File Transfer ke UI
├── Tombol File → FileChooser dialog
├── Saat file dipilih:
│   ├── Tampilkan progress bar
│   ├── Upload via FileClient di thread terpisah
│   └── Update progress bar dengan Platform.runLater()
├── Saat notifikasi file masuk:
│   └── Tampilkan bubble chat dengan tombol Download
└── Test: upload MP3 dan gambar, progress bar bergerak

Langkah 5.6 — Render File di UI
├── Gambar: tampilkan ImageView di dalam chat bubble
├── MP3: tampilkan tombol Play → gunakan JavaFX MediaPlayer
└── Test: gambar muncul inline, MP3 bisa diplay langsung



Milestone 6 — Deploy & Testing Jaringan Luar
Langkah 6.1 — Persiapan VPS
├── Daftar Oracle Cloud Free Tier (gratis selamanya)
├── Buat instance Ubuntu 22.04
├── Buka port 8080 dan 8081 di firewall VPS
└── Test: ping IP publik VPS dari laptop

Langkah 6.2 — Deploy Server
├── Build project → jar file (mvn package)
├── Upload jar ke VPS via SCP
├── Jalankan: java -jar chatserver.jar
└── Test: konek dari laptop ke IP publik VPS

Langkah 6.3 — Config Client
├── Ganti hardcode "localhost" dengan input field di login screen
├── Default value: IP VPS kamu
└── Test: dua laptop berbeda jaringan bisa saling chat
