# 📝 Laporan Tugas Akhir

**Mata Kuliah**: Sistem Operasi
**Semester**: Genap / Tahun Ajaran 2024–2025
**Nama**: `<Khansa Amanda Icha Sentana>`
**NIM**: `<240202838>`
**Modul yang Dikerjakan**:

## 📌 Deskripsi Singkat Tugas
Memodifikasi algoritma penjadwalan proses di sistem operasi xv6-public dari yang awalnya menggunakan metode Round Robin menjadi Priority Scheduling Non-Preemptive. Dalam perubahannya, ditambahkan field prioritypada setiap proses untuk menyimpan nilai prioritas, serta pembuatan system call baru bernama set_priority(int)yang memungkinkan pengguna mengatur proses prioritas secara langsung. Selain itu, fungsinya schedulerdimodifikasi agar menjalankan proses RUNNABLEdengan prioritas tertinggi (yaitu proses dengan nilai prioritas paling kecil).


**Uji Fungsionalitas**
⦁ ptest.cuntuk menguji apakah proses dengan prioritas lebih tinggi dieksekusi lebih dulu. Anak 2 diberi prioritas 0, Anak 1 diberi prioritas 10.


---

## 🛠️ Rincian Implementasi
Modifikasi pada fungsi scheduler()di proc.cuntuk mengganti algoritma Round Robin menjadi Non-Preemptive Priority Scheduling
⦁ Menambahkan Field prioritypada Struct procdi file proc.h
⦁ Menambahkan fungsi sys_set_priority()di sysproc.cuntuk mengambil argumen prioritas dari pengguna dan memasukkan ke field priority
⦁ Menambahkan definisi syscall SYS_set_prioritydengan nomor 22 di syscall.h
⦁ Mencantumkan deklarasi eksternal dan entri syscall set_prioritydi syscall.c
⦁ Menambah kembali int set_priority(int priority); di user.h
⦁ Ditambahkan SYSCALL(set_priority)di usys.Suntuk mendefinisikan syscall
⦁ Menambahkan implementasi fungsi sys_set_priority()di sysproc.cuntuk mengatur nilai proses melalui system call
⦁ Membuat program uji ptest.cuntuk memverifikasi proses dengan prioritas lebih tinggi agar dijalankan lebih dulu oleh scheduler
⦁ tambahkan _ptestke MakefilebagianUPROGS=\

**Hasil Uji**
📍 Keluaran ptest

$ ptest
Child 2 selesai
Child 1 selesai
Parent selesai
$ 
<img width="1920" height="1080" alt="Screenshot 2025-07-30 182820" src="https://github.com/user-attachments/assets/ec788555-297a-4fc9-bfe1-8bdaffb365a0" />

## ⚠️ Kendala yang Dihadapi
⦁ Output dari ptestsempat tidak sesuai harapan karena proses cetak (printf)dari beberapa proses tumpang tindih akibat eksekusi paralel tanpa output sinkronisasi
⦁ Awalnya output menunjukkan urutan proses tidak sesuai prioritas karena penundaan sleep()belum diatur dengan benar untuk menghindari tabrakan antar proses.
---
## 📚 Referensi
⦁ Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
⦁ Repositori xv6-public: https://github.com/mit-pdos/xv6-public
---

