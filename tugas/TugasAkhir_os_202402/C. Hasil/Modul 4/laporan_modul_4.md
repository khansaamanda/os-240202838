# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: `<Khansa Amanda Icha Sentana>`
**NIM**: `<240202838>`
**Modul yang Dikerjakan**:
---
## 📌 Deskripsi Singkat Tugas

Memodifikasi kernel xv6 dengan menambahkan dua fitur baru yaitu :

1. system call chmod(path, mode), yaitu fungsi yang bisa dipanggil dari pengguna program untuk mengatur apakah sebuah file hanya bisa dibaca (read-only) atau bisa dibaca dan ditulis (read-write) untuk mengenalkan konsep pengaturan izin akses file di level kernel.
2. Membuat perangkat khusus /dev/randomyaitu file khusus yang bisa dibaca untuk mendapatkan data acak. File ini berfungsi seperti generator angka acak sederhana yang dibuat langsung dari dalam kernel.

---

## 🛠️ Rincian Implementasi
1. system call chmod(path, mode) Untuk menguji kemampuan membatasi akses tulis pada file, dilakukan modifikasi agar file bisa dibuat tidak bisa ditulis dengan syscall chmod(). 🔧 File Modifikasi:
-sysfile.c: Menambahkan syscall sys_chmod()untuk mengatur writeable flag pada inode.
-fs.c: Fungsi writei()diperbarui supaya menolak penulisan jika inode tidak dapat ditulis.
-file.c: penolakan filewrite()menggunakan logika writeblocking dari writei().
-user.h: Mendeklarasikan prototipe chmod()agar dapat dipanggil dari user-space.
-usys.S: Menambahkan entri syscall chmodagar dapat dipanggil dari pengguna program.
-syscall.c: Menambahkan entri syscall SYS_chmodke dalam array syscalls[].
-syscall.h: Menambahkan #define SYS_chmodke syscall nomor baru.
-defs.h: Menambahkan deklarasi fungsi sys_chmod()agar dapat dipanggil kernel internal.
-fs.h: Menambahkan field baru pada struktur inode untuk menyimpan status writable.
-sleeplock.hdan spinlock.hdimodifikasi dengan penambahan header guard untuk mencegah duplikasi saat kompilasi dan menjaga kestabilan sistem saat file digunakan di berbagai bagian kernel.
-chmodtest.c: Program pengujian untuk membuat file, mengubah statusnya menjadi read-only, dan mencoba menulis ke file tersebut.
-Pada chmodtest.c, ditambahkan baris printf(1, "hallo\n");sebagai indikator awal bahwa program berhasil dieksekusi sebelum melakukan pengujian menggunakan syscall chmod().
-Mendaftarkan program uji keMakefile

2. Device Acak (/dev/random) Fitur ini menambahkan perangkat khusus bernama /dev/random yang menghasilkan byte acak saat dibaca,yang menyerupai fungsi random device pada sistem Linux. 🔧 File Modifikasi:
-random.c: Menyediakan fungsi randomread()untuk menghasilkan angka acak.
-fs.cdan file.c: menerjemahkan perangkat /dev/random lewat array devsw[]agar saat dibaca, sistem tahu harus menjalankan fungsi randomread()untuk menghasilkan angka acak.
-sysfile.c: Mendukung pembuatan perangkat /dev/random melalui mknod().
-defs.h: Menambahkan deklarasi fungsi diskwrite, diskread, dan randomreaduntuk mendukung implementasi perangkat /dev/random
-init.c: Menambahkan perintah mknod("/dev/random", 1, 3);agar perangkat dibuat otomatis ketika boot.
-randomtest.c: Program pengujian yang membaca angka acak dari /dev/randomdan menampilkannya.
-Mendaftarkan program uji keMakefile

## ✅ Uji Fungsionalitas

1. chmodtest: untuk memastikan file read-onlytidak bisa ditulis.
2. randomtest: untuk menguji apakah pembacaan dari perangkat /dev/randommenghasilkan angka acak seperti yang diharapkan.
---

## 📷 Hasil Uji
<img width="1920" height="1080" alt="Screenshot 2025-07-31 123126" src="https://github.com/user-attachments/assets/125d0ce0-5512-4796-a0e3-c38b1a2a7381" />

Keluaran chmodtest:
$ chmodtest
Write blocked as expected
📍 Keluaran randomtest:
$ randomtest
Silakan ketik sesuatu: hello
Kamu mengetik: hello

104 101 108 108 111 10 0 0 
$ 
## ⚠️ Kendala yang Dihadapi

1. Salah menambahkan validasi proteksi penulisan di writei()sehingga file read-only masih bisa ditulisi.
2. Tidak melakukan pengecekan return value dari write()di chmodtest.csehingga error tidak terdeteksi saat penulisan ditolak.
3. Lupa mendaftarkan system call chmoddi syscall.c, syscall.h, dan usys.Ssehingga system call tidak diketahui saat runtime.
4. Tidak menambahkan perintah mknod("/dev/random", 1, 3);yang init.cmenyebabkan perangkat /dev/randomtidak tersedia saat booting.
5. Tidak mendaftarkan randomreadke devsw[]pada file.cmenyebabkan pembacaan /dev/randomtidak memanggil fungsi yang benar.
6. Lupa mendeklarasikan randomread()di fs.hdan defs.hsehingga menyebabkan error saat linking.
---

## 📚 Referensi
Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
Gudang xv6-public: https://github.com/mit-pdos/xv6-public
Stack Overflow, Masalah GitHub, diskusi praktikum

---

