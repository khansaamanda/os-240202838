# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: `<Khansa Amanda Icha Sentana>`
**NIM**: `<240202838>`
**Modul yang Dikerjakan**:
---

## 📌 Deskripsi Singkat Tugas

Modul 3 – Manajemen Memori Tingkat Lanjut (xv6-public x86) :
Modul ini fokus pada optimalisasi manajemen memori di sistem operasi xv6, dengan mengimplementasikan dua fitur penting:

Copy-on-Write (CoW) Fork Meningkatkan efisiensi fork() dengan menunda penyalinan halaman memori hingga proses melakukan penulisan. Halaman memori awalnya ditayangkan dalam mode read-only, dan hanya kompresor saat terjadi kesalahan halaman akibat operasi tulis. Manajemen memori fisik dikendalikan melalui penghitungan referensi sistem.
Memori Bersama ala System V Menyediakan mekanisme berbagi memori antar proses menggunakan sistem panggilan:
void* shmget(int key)Untuk mengambil atau membuat shared memory berdasarkan key tertentu.
int shmrelease(int key)untuk melepaskan memori bersama. Implementasi ini memungkinkan beberapa proses mengakses satu halaman memori bersama dengan pengelolaan akses melalui jumlah referensi.
---

## 🛠️ Rincian Implementasi
1. Fork Copy-on-Write (CoW)
-vm.c: Menambahkan array refcount[], fungsi incref()dan decref()untuk mengelola jumlah referensi halaman fisik, dan memodifikasi copyuvm()menjadi cowuvm()untuk mendukung mekanisme Copy-on-Write.
-proc.c: Mengubah fungsi fork()agar menggunakan cowuvm()sebagai pengganti copyuvm()saat duplikasi proses tabel halaman.
-trap.c: Menambah penanganan page error untuk melakukan salinan halaman (copy) saat proses melakukan penulisan pada halaman bertanda PTE_COW.
-mmu.h: Menambah resolusi flag PTE_COWuntuk menandai tabel halaman entri yang menggunakan mekanisme Copy-on-Write.
-defs.h: Menambahkan deklarasi fungsi incref()dan decref()untuk manajemen refcountpada halaman memori fisik.
-cowtest.c: Membuat program uji cowtest.
-Mendaftarkan program uji _cowtestke Makefile.

2. Memori Bersama ala Sistem V
-sysproc.c: Menambahkan syscall sys_shmget()untuk memperoleh satu halaman shared memory berdasarkan key, dan sys_shmrelease()untuk mengurangi refcountdan Menghapus halaman jika tidak ada proses yang menggunakannya.
-vm.c: Membuat array shmtab[]yang menyimpan informasi key, alamat halaman memori (frame), dan refcountuntuk mencatat jumlah proses yang mengakses shared memory.
-syscall.h: Menambahkan nomor syscall SYS_shmgetdan SYS_shmreleasesupaya fungsinya bisa dikenal sistem.
-user.h: Menambahkan deklarasi fungsi shmget(int)dan shmrelease(int)supaya dapat digunakan oleh pengguna program.
-usys.S: Menambahkan entri syscall SYSCALL(shmget)dan SYSCALL(shmrelease)untuk menjembatani pemanggilan dari user space ke kernel.
-syscall.c: Mendaftarkan sys_shmgetdan sys_shmreleaseke dalam tabel syscall untuk menghubungkan nomor syscall dengan fungsi yang dijalankan.
-shmtest: Membuat program uji shmtest.
-Mendaftarkan program uji ke Makefile(_ shmtest).
---

## ✅ Uji Fungsionalitas

-cowtest: untuk menguji apakah proses fork berjalan dengan mekanisme Copy-on-Write (CoW) tanpa langsung menggandakan seluruh memori.
-shmtest: untuk menguji apakah shmget()dapat membagi memori antar proses, dan shmrelease()berhasil melepasnya saat tidak digunakan lagi.
---

## 📷 Hasil Uji

### 📍 Contoh Output `cowtest`:

```
Child sees: Y
Parent sees: X
```

### 📍 Contoh Output `shmtest`:

```
Child reads: A
Parent reads: B

## ⚠️ Kendala yang Dihadapi
1. Tidak menambahkan penanganan khusus untuk kesalahan halaman bertipe PTE_COWdi trap()yang menyebabkan proses langsung dihentikan (panik) saat terjadi penulisan ke halaman hasil fork().
2. Fungsi fork()tidak diarahkan ke cowuvm()benar, atau gagal menangani error return dari cowuvm(), menyebabkan proses anak crash saat dijalankan.
3. Tidak menghapus modifier staticpada fungsi mappages()menyebabkan fungsi ini tidak bisa digunakan dari file lain sepertivm.c
4. Awalnya menggabungkan uji praktikum yang A dengan B jadi satu sehingga banyak error terus menerus karena tabrakan , kemudian mencoba untuk modifikasi satu dulu baru berhasil,tapi kemudian lupa tidak backup yang modifikasi A lalu isi file modifikasinya hilang, tapi output sudah screenshot.
5. Karena sudah berhasil mengeluarkan output program uji pertama, dilanjutkan untuk memodifikasi fitur ke dua tapi malah jadi berantakan semua isi filenya dan error fatal.
6. Kemudian menghapus semua modifikasi,dan mencoba fokus ke program modifikasi uji ke dua saja, tidak jadi satu dengan yang pertama(A) dan akhirnya berhasil.
---

## 📚 Referensi
Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
Gudang xv6-public: https://github.com/mit-pdos/xv6-public
Stack Overflow, Masalah GitHub, diskusi praktikum


---

