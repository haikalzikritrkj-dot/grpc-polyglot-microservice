# gRPC Polyglot Microservice (Go, Python, Node.js)

Proyek ini merupakan implementasi sistem komunikasi *inter-service* berbasis **gRPC** dan **Protocol Buffers (proto3)** pada arsitektur terdistribusi *polyglot* di lingkungan sistem operasi Windows. Layanan *server* dibangun menggunakan **Go (Golang)**, sedangkan *client* dibangun menggunakan **Python** dan **Node.js**.

---

## 📂 Struktur Folder Proyek Lengkap

```text
GRPC/
├── .gitignore
├── README.md
├── user.proto
├── go-server/
│   ├── go.mod
│   ├── go.sum
│   ├── main.go
│   └── pb/
│       ├── user.pb.go
│       └── user_grpc.pb.go
├── python-client/
│   ├── client.py
│   ├── user_pb2.py
│   └── user_pb2_grpc.py
└── node-client/
    ├── client.js
    ├── package.json
    └── package-lock.json
```

---

##  Prasyarat & Lingkungan Kerja (Windows)

1. **Protocol Buffer Compiler (`protoc`):** Versi `v3.21.9` (Terinstal di `E:\protoc\bin`).
2. **Go Programming Language:** Versi `v1.27.x` (Terinstal di `E:\Go\bin`).
3. **Python Environment:** Versi `3.x` (Pustaka: `grpcio`, `grpcio-tools`).
4. **Node.js Runtime:** Versi `24.x` (Pustaka: `@grpc/grpc-js`, `@grpc/proto-loader`).

###  Pengaturan Environment Variables (System PATH)
Pastikan 3 jalur berikut telah didaftarkan pada **User Variables -> Path** di Windows:
- `E:\protoc\bin`
- `E:\Go\bin`
- `%USERPROFILE%\go\bin`

---

##  Kontrak API (`user.proto`)

Kontrak antarmuka data didefinisikan secara tunggal pada file `user.proto` yang berada di direktori utama `GRPC/`:

```protobuf
syntax = "proto3";

package user;

option go_package = "./pb";

// Skema permintaan dari client
message UserRequest {
  int32 user_id = 1;
}

// Skema tanggapan dari server (termasuk field phone)
message UserResponse {
  string name = 1;
  string email = 2;
  string phone = 3;
}

// Layanan RPC utama
service UserService {
  rpc GetUserProfile (UserRequest) returns (UserResponse);
}
```

---

##  Perintah Kompilasi Protobuf

### 1. Go Server (Static Code Generation)
```cmd
cd /d "E:\Politeknik Lhokseumawe\A Semester 5\Pak Reza\Tugas\GRPC\go-server"
protoc --proto_path=../ --go_out=. --go-grpc_out=. ../user.proto
```

### 2. Python Client (Static Code Generation)
```cmd
cd /d "E:\Politeknik Lhokseumawe\A Semester 5\Pak Reza\Tugas\GRPC\python-client"
python -m grpc_tools.protoc --proto_path=../ --python_out=. --grpc_python_out=. ../user.proto
```

### 3. Node.js Client (Dynamic Runtime Loader)
Node.js membaca file `../user.proto` secara langsung saat aplikasi dijalankan (*runtime*) menggunakan `@grpc/proto-loader`, sehingga **tidak memerlukan** kompilasi `protoc` manual.

---

## 🚀 Cara Menjalankan Aplikasi

Jalankan pengujian dengan membuka **3 jendela Command Prompt (CMD)** terpisah secara bersamaan:

###  Terminal 1: Jalankan Go Server
```cmd
cd /d "E:\Politeknik Lhokseumawe\A Semester 5\Pak Reza\Tugas\GRPC\go-server"
go run main.go
```
*Tampilan Terminal yang Berhasil:*
```text
gRPC Server (Go) berjalan di port :50051...
2026/10/25 22:17:48 Menerima request untuk User ID: 1
```

###  Terminal 2: Jalankan Python Client
```cmd
cd /d "E:\Politeknik Lhokseumawe\A Semester 5\Pak Reza\Tugas\GRPC\python-client"
python client.py
```
*Tampilan Terminal yang Berhasil:*
```text
Mengirim request ke Go Server dari Python...
Respon dari Server -> Nama: Haikal Zikri, Email: Haikal@universitas.ac.id, No Telp: 081234567890
```

###  Terminal 3: Jalankan Node.js Client
```cmd
cd /d "E:\Politeknik Lhokseumawe\A Semester 5\Pak Reza\Tugas\GRPC\node-client"
node client.js
```
*Tampilan Terminal yang Berhasil:*
```text
Mengirim request ke Go Server dari Node.js...
Respon dari Server -> Nama: Haikal Zikri, Email: Haikal@universitas.ac.id, No Telp: 081234567890
```

---

##  Analisis Pendekatan Kompilasi

1. **Static Code Generation (Go & Python):**
   Memerlukan proses kompilasi manual menggunakan `protoc` sebelum aplikasi dieksekusi untuk menghasilkan berkas kode sumber fisik (*stubs*). Keuntungannya adalah ketersediaan pemeriksaan tipe data (*type-checking*) yang ketat pada saat pembentukan kode (*compile-time*) serta *auto-complete* pada editor kode.

2. **Dynamic Runtime Generation (Node.js):**
   Membaca dan menganalisis (*parsing*) berkas skema `.proto` secara langsung pada saat aplikasi dijalankan (*runtime*) memanfaatkan pustaka `@grpc/proto-loader`. Keuntungannya adalah alur kerja pengembangan menjadi lebih fleksibel tanpa memerlukan kompilasi ulang manual setiap terjadi perubahan skema.
