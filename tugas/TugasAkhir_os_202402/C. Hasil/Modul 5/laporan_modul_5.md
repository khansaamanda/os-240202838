# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: `<Khansa Amanda Icha Sentana>`
**NIM**: `<240202838>`
**Modul yang Dikerjakan**:
---

## 📌 Deskripsi Singkat Tugas

Modul 5 – Audit dan Keamanan Sistem (xv6-public) : Modul ini menambahkan fitur audit log pada sistem operasi xv6. Setiap pemanggilan system call akan dicatat secara otomatis. Log hanya bisa dibaca oleh proses dengan PID 1 melalui system call get_audit_log(), sehingga akses log lebih aman dan terlindungi dari proses biasa.

## 🛠️ Rincian Implementasi
-syscall.c : Menambahkan struktur audit_entry, array audit_log[MAX_AUDIT], dan variabel audit_index , lalu memodifikasi fungsi syscall() untuk mencatat setiap pemanggilan syscall ke dalam log.
-sysproc.c : Menambahkan definisi struct audit_entry dan deklarasi eksternal variabel audit_log serta audit_index , lalu mengimplementasikan syscall baru sys_get_audit_log() yang hanya dapat dipanggil oleh proses dengan PID 1.
-defs.h : Menambahkan makro #define MAX_AUDIT 128 dan deklarasi fungsi get_audit_log(struct audit_entry*, int) untuk mendukung syscall audit.
-user.h : Menambahkan definisi struct audit_entry dan deklarasi fungsi get_audit_log(void *buf, int max) untuk digunakan pada program user space.
-usys.S : Menambahkan entri SYSCALL(get_audit_log) untuk menghubungkan syscall ke kode user.
-syscall.h : Mendaftarkan syscall get_audit_log dengan nomor 22 melalui penambahan #define (#define SYS_get_audit_log 22).
-audit.c : Membuat program uji audit.c di user space untuk memanggil syscall get_audit_log() dan menampilkan isi log syscall dalam format [i] PID=x SYSCALL=y         TICK=z secara berurutan.
-Makefile : Mendaftarkan program uji audit.c ke Makefile.
-Memodifikasi init.c supaya langsung mengeksekusi program audit setelah inisialisasi selesai, dengan mengganti eksekusi shell (sh) menjadi exec("audit", audit_argv);.

## ✅ Uji Fungsionalitas

audit: untuk melihat isi log system call (jika dijalankan oleh PID 1)

---

## 📷 Hasil Uji
Output audit dengan PID bukan 1 :
$ audit
Access denied or error. 

Output audit PID = 1 :
=== Audit Log ===
[0] PID=1 SYSCALL=7 TICK=7
[1] PID=1 SYSCALL=15 TICK=12
[2] PID=1 SYSCALL=17 TICK=14
[3] PID=1 SYSCALL=15 TICK=18
...
<img width="1920" height="1080" alt="Screenshot 2025-07-31 131945" src="https://github.com/user-attachments/assets/73ca424c-f16d-4a0a-abeb-c450bd777d83" />

## ⚠️ Kendala yang Dihadapi
1. Tidak mendefinisikan ulang struct audit_entry di user.h menyebabkan program user tidak dapat mengenali struktur data log yang dikembalikan oleh syscall.
2. Menggunakan sizeof(struct audit_entry) di sysproc.c tanpa mendefinisikan struktur secara lengkap yang menyebabkan error karena hanya tersedia deklarasi awal di defs.h.
3. Forward declaration struct audit_entry; di defs.h hanya menyatakan keberadaan struct, namun tidak menyediakan informasi ukuran atau isi, sehingga tidak dapat digunakan untuk operasi seperti copyout atau sizeof. Hal ini menyebabkan kompilasi gagal jika definisi lengkap struct tidak tersedia.
4. Tidak menyertakan definisi lengkap struct audit_entry di file sysproc.c menyebabkan fungsi sys_get_audit_log tidak dapat mengakses atau menyalin data log dengan benar.

---

## 📚 Referensi

Tuliskan sumber referensi yang Anda gunakan, misalnya:
-Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
-Repositori xv6-public: https://github.com/mit-pdos/xv6-public
-Stack Overflow, GitHub Issues, diskusi praktikum


---

