# Java-Socket-Programming
Projek ini berdasarkan mata kuliah Pemrograman jaringan. Pada pertemuan ke-6, saya beserta kelompok 2 sudah berhasil menjalankan program sederhana yang mengimplementasikan penggunakan Socket pada Java. Program yang dijalankan terdiri dari dua jenis program. Berikut adalah overview dari kedua program pertama tersebut:

**HALF DUPLEX**
Program pertama merupakan program yang menghitung luas suatu lingkaran dari jari-jari (r) yang diinput dari Client ke Server. Sesuai dengan namanya, server hanya bisa melakukan komunikasi dengan tidak lebih dari satu Client. Jika ada Client lain yang mencoba untuk melakukan komunikasi, maka Client tersebut harus menuggu Client sebelumnya selesai melakukan komunikasi. 
**FULL DUPLEX**
Program kedua merupakan program yang menginput alamat rumah mahasiswa ke server dan nantinya server akan menyimpannya dalam bentuk file. Sesuai dengan namanya, server bisa menerima input dari multiclient dan langsung menyimpan data alamat rumah mahasiswa dalam bentuk file biner. File biner selanjutnya dikonversi kedalam bentuk file yang lebih human-readable.

Berdasarkan dua program sederhana tersebut, saya terinspirasi untuk membuat project lebih lanjut dengan memanfaatkan Java Socket. Projek ini nantinya akan berbentuk Chat Application yang tidak hanya bisa bertukar Plain text tapi juga bisa bertukar File Audio dan File Image. Berikut adalah gambaran kasar dari arsitektur Project Chat App:
┌─────────────────────────────────────────────────────────┐
│                    SERVER SIDE                          │
│                                                         │
│   ┌─────────────┐      ┌──────────────────────────┐    │
│   │ Connection  │      │      Room Manager         │    │
│   │  Listener   │─────►│  Room A │ Room B │ Room C │    │
│   │(ServerSocket│      └──────────────────────────┘    │
│   │  :8080)     │              │                        │
│   └─────────────┘              ▼                        │
│                       ┌─────────────────┐               │
│   ┌─────────────┐     │  Message Broker │               │
│   │  File       │     │  (route pesan   │               │
│   │  Server     │     │   ke room yg    │               │
│   │  :8081      │     │   tepat)        │               │
│   └─────────────┘     └─────────────────┘               │
│         │                      │                        │
│         └──────────┬───────────┘                        │
│                    ▼                                     │
│          ┌──────────────────┐                           │
│          │   Auth Manager   │                           │
│          │ (username+token) │                           │
│          └──────────────────┘                           │
└─────────────────────────────────────────────────────────┘
           │                    │
    TCP :8080               TCP :8081
    (Chat Stream)          (File Transfer)
           │                    │
┌──────────────────────────────────────────────────────────┐
│                    CLIENT SIDE                           │
│                                                          │
│  ┌──────────────┐    ┌──────────────┐                   │
│  │  Chat Thread │    │  File Thread │                   │
│  │  (Send/Recv) │    │  (Upload /   │                   │
│  │              │    │   Download)  │                   │
│  └──────┬───────┘    └──────┬───────┘                   │
│         └──────────┬────────┘                            │
│                    ▼                                     │
│          ┌──────────────────┐                           │
│          │   JavaFX UI      │                           │
│          │  (Main Thread)   │                           │
│          │                  │                           │
│          │ [Chat Panel]     │                           │
│          │ [File Panel]     │                           │
│          │ [Room List]      │                           │
│          └──────────────────┘                           │
└──────────────────────────────────────────────────────────┘
