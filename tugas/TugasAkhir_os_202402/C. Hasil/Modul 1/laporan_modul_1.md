# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: `<Khansa Amanda Icha Sentana>`
**NIM**: `<240202838>`
**Modul yang Dikerjakan**:
`(Modul 1 – System Call dan Instrumentasi Kernel)`

---

## 📌 Deskripsi Singkat Tugas
* **Modul 1 – System Call dan Instrumentasi Kernel**:
System Call
System call adalah antarmuka antara program pengguna (user space) dan kernel sistem operasi. Melalui system call, program dapat meminta layanan dari kernel, seperti mengakses file, membuat proses, atau menggunakan perangkat keras. Contoh system call meliputi read(), write(), fork(), dan exec(). System call penting karena membatasi akses langsung ke sumber daya sistem, menjaga keamanan dan stabilitas.

Instrumentasi Kernel
Instrumentasi kernel adalah proses menambahkan mekanisme pemantauan atau pelacakan di dalam kernel untuk mengumpulkan data tentang aktivitas sistem, seperti kinerja, penggunaan sumber daya, atau debugging. Teknik ini digunakan untuk menganalisis perilaku sistem dan memperbaiki bug atau meningkatkan efisiensi. Contoh alat instrumentasi kernel adalah ftrace, perf, dan SystemTap.

## 🛠️ Rincian Implementasi
Tambahkan Struktur pinfodan Counterreadcount
1. Diproc.h
#define MAX_PROC 64

struct pinfo {
  int pid[MAX_PROC];
  int mem[MAX_PROC];
  char name[MAX_PROC][16];
};
2. Di sysproc.c(global)
int readcount = 0; // untuk menghitung read()
Tambahkan Nomor System Call Baru
Di syscall.h(paling bawah)
#define SYS_getpinfo     22
#define SYS_getreadcount 23
Registrasikan syscall
Disyscall.c
Tambah deklarasi di atas:
extern int sys_getpinfo(void);
extern int sys_getreadcount(void);
Tambah ke array syscall:
[SYS_getpinfo]     sys_getpinfo,
[SYS_getreadcount] sys_getreadcount,
Tambahkan ke Tingkat Pengguna
1. Diuser.h
struct pinfo; // forward declaration
int getpinfo(struct pinfo *);
int getreadcount(void);
2. Diusys.S
SYSCALL(getpinfo)
SYSCALL(getreadcount)
 Implementasi Fungsi Kernel
1. Di sysproc.ctambahkan:
int sys_getreadcount(void) {
  return readcount;
}
#include "proc.h"

int sys_getpinfo(void) {
  struct pinfo *ptable;
  if (argptr(0, (char**)&ptable, sizeof(*ptable)) < 0)
    return -1;

  struct proc *p;
  int i = 0;
  acquire(&ptable_lock);
  for(p = ptable.proc; p < &ptable.proc[NPROC]; p++){
    if(p->state != UNUSED){
      ptable->pid[i] = p->pid;
      ptable->mem[i] = p->sz;
      safestrcpy(ptable->name[i], p->name, sizeof(p->name));
      i++;
    }
  }
  release(&ptable_lock);
  return 0;
}
💡 Catatan: ptable_lock mungkin belum ada di xv6-public default. Sebagai pengganti aman:

acquire(&ptable.lock);
...
release(&ptable.lock);
Modifikasi read()untuk Tambah Counter
Di sysfile.c, fungsisys_read()
Tambahkan di bagian atas fungsi:

readcount++;
Buat Program Penguji Tingkat Pengguna
1. File: ptest.c(untuk getpinfo)
#include "types.h"
#include "stat.h"
#include "user.h"

struct pinfo {
  int pid[64];
  int mem[64];
  char name[64][16];
};

int main() {
  struct pinfo p;
  getpinfo(&p);

  printf(1, "PID\tMEM\tNAME\n");
  for(int i = 0; i < 64 && p.pid[i]; i++) {
    printf(1, "%d\t%d\t%s\n", p.pid[i], p.mem[i], p.name[i]);
  }
  exit();
}
2. File: rtest.c(untuk getreadcount)
#include "types.h"
#include "stat.h"
#include "user.h"

int main() {
  char buf[10];
  printf(1, "Read Count Sebelum: %d\n", getreadcount());
  read(0, buf, 5); // baca stdin
  printf(1, "Read Count Setelah: %d\n", getreadcount());
  exit();
}
3. Daftarkan keMakefile
Cari bagian:

UPROGS=\
  _cat\
  _echo\
  ...
Tambahkan:

  _ptest\
  _rtest\
  Build dan Jalankan
make clean
make qemu-nox
Lalu di shell xv6:

$ ptest
PID	MEM	NAME
1	4096	init
2	2048	sh
3	2048	ptest
...

$ rtest
Read Count Sebelum: 4
hello
Read Count Setelah: 5
Kesimpulan
Dengan langkah-langkah di atas, Anda berhasil:

Menambahkan 2 syscall baru di xv6-public
Mengakses info proses aktif melaluigetpinfo()
Memonitor aktivitas read()melaluigetreadcount()


### Contoh untuk Modul 1:

* Menambahkan dua system call baru di file `sysproc.c` dan `syscall.c`
* Mengedit `user.h`, `usys.S`, dan `syscall.h` untuk mendaftarkan syscall
* Menambahkan struktur `struct pinfo` di `proc.h`
* Menambahkan counter `readcount` di kernel
* Membuat dua program uji: `ptest.c` dan `rtest.c`
---

## ✅ Uji Fungsionalitas

Tuliskan program uji apa saja yang Anda gunakan, misalnya:

* `ptest`: untuk menguji `getpinfo()`
* `rtest`: untuk menguji `getReadCount()`

---

## 📷 Hasil Uji

$ ptest  
PID     MEM     NAME  
1       12288   init  
2       16384   sh  
3       12288   ptest

$ rtest  
Read Count Sebelum: 12  
hello  
Read Count Setelah: 13  
$ $   

<img width="1920" height="1080" alt="Screenshot 2025-07-31 001411" src="https://github.com/user-attachments/assets/5ca6e63c-7e2f-4008-aeba-36732e274a8b" />

## ⚠️ Kendala yang Dihadapi

1. readcounttidak diketahui yang menyebabkan kesalahan referensi tidak terdefinisi
2. Salah menambahkan field prioritypada struct procsehingga menyebabkan gagal kompilasi
3. Makefile error karena penggunaan spasi bukan tab
4. Output ptest tidak rapi karena proses pencetakan secara bersamaan tanpa sinkronisasi

---

## 📚 Referensi

Tuliskan sumber referensi yang Anda gunakan, misalnya:

* Buku xv6 MIT: [https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)
* Repositori xv6-public: [https://github.com/mit-pdos/xv6-public](https://github.com/mit-pdos/xv6-public)
* Stack Overflow, GitHub Issues, diskusi praktikum

---

