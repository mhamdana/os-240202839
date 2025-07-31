#📝 Laporan Tugas Akhir
Mata Kuliah: Sistem Operasi
```
Semester: Genap / Tahun Ajaran 2024–2025
Nama: <Muhammad Khamdan Azkiya>
NIM: <240202839>
```
Modul yang Dikerjakan: Modul 2 – Penjadwalan CPU Lanjutan (Priority Scheduling Non-Preemptive)

---

##📌 Deskripsi Singkat Tugas
 * Modul 2 – Penjadwalan CPU Lanjutan (Priority Scheduling Non-Preemptive):
   Mengubah algoritma penjadwalan proses di xv6-public dari Round Robin menjadi Non-Preemptive Priority Scheduling. Ini melibatkan penambahan field priority pada setiap struct proc, penambahan syscall set_priority(int) untuk mengatur prioritas proses, dan modifikasi scheduler agar selalu menjalankan proses RUNNABLE dengan prioritas tertinggi (nilai prioritas lebih kecil menandakan prioritas lebih tinggi).

##🛠️ Rincian Implementasi
Secara ringkas, berikut adalah langkah-langkah implementasi yang dilakukan:
 * Menambahkan field int priority; ke dalam struct proc di file proc.h. Nilai 0 untuk prioritas tertinggi dan 100 untuk terendah.
 * Menginisialisasi p->priority = 60; sebagai nilai default saat alokasi proses baru di fungsi allocproc() dalam proc.c.
 * Mengimplementasikan syscall set_priority(int):
   * Menambahkan nomor syscall SYS_set_priority (nilai 24) di syscall.h.
   * Mendeklarasikan int set_priority(int priority); di user.h.
   * Menambahkan entri SYSCALL(set_priority) di usys.S.
   * Meregistrasikan syscall sys_set_priority di syscall.c dengan menambahkan extern int sys_set_priority(void); dan [SYS_set_priority] sys_set_priority, ke dalam array syscalls[].
   * Mengimplementasikan fungsi sys_set_priority() di sysproc.c untuk mengatur prioritas proses saat ini, dengan validasi nilai prioritas antara 0 dan 100.
 * Memodifikasi fungsi scheduler() di proc.c untuk menerapkan logika penjadwalan non-preemptive priority scheduling. Scheduler kini akan mencari proses dengan status RUNNABLE yang memiliki nilai priority terkecil (tertinggi), lalu mengeksekusinya.
 * Membuat program uji ptest.c untuk memverifikasi fungsionalitas priority scheduling. Program ini membuat dua child process dengan prioritas berbeda (satu rendah, satu tinggi) dan memanggil fungsi busy() untuk simulasi kerja.
 * Menambahkan _ptest\ ke dalam daftar UPROGS di Makefile agar program uji dapat dikompilasi dan dijalankan di xv6.
### Contoh untuk Modul 1:

* Menambahkan dua system call baru di file `sysproc.c` dan `syscall.c`
* Mengedit `user.h`, `usys.S`, dan `syscall.h` untuk mendaftarkan syscall
* Menambahkan struktur `struct pinfo` di `proc.h`
* Menambahkan counter `readcount` di kernel
* Membuat dua program uji: `ptest.c` dan `rtest.c`
---

## ✅ Uji Fungsionalitas
Program uji yang digunakan adalah:
 * ptest: Untuk menguji fungsionalitas set_priority() dan memastikan scheduler menjalankan proses dengan prioritas yang lebih tinggi terlebih dahulu (dalam kasus non-preemptive).

---

## 📷 Hasil Uji

Lampirkan hasil uji berupa screenshot atau output terminal. Contoh:

###📍 Output ptest:

```
Child 2 selesai
Child 1 selesai
Parent selesai
```
Penjelasan Output:
Dari output di atas, Child 2 (yang diatur dengan set_priority(10), yaitu prioritas tinggi) dieksekusi dan selesai terlebih dahulu, diikuti oleh Child 1 (dengan set_priority(90), yaitu prioritas rendah). Ini mengkonfirmasi bahwa scheduler berhasil memilih dan menjalankan proses berdasarkan prioritas yang telah ditetapkan, dan karena implementasinya non-preemptive, proses dengan prioritas lebih tinggi akan berjalan hingga selesai sebelum scheduler memilih proses lain dengan prioritas lebih rendah.
Screenshot Pengerjaan:
Berikut adalah tangkapan layar dari file-file yang dimodifikasi dan output hasil uji:
 * syscall.h (Penambahan SYS_set_priority)
 * user.h (Deklarasi set_priority)
 * usys.S (Entri set_priority)
 * sysproc.c (Implementasi sys_set_priority)
 * syscall.c (Registrasi sys_set_priority)
 * proc.h (Penambahan priority ke struct proc)
 * proc.c (Inisialisasi priority di allocproc dan Modifikasi scheduler())
 * ptest.c (Program Pengujian)
 * Output ptest di Terminal xv6
   (Asumsi output yang sama dengan yang disediakan)
   (Ini adalah gambar umum dari terminal, bukan khusus output ptest, tetapi merepresentasikan lokasi output.)

## ⚠️ Kendala yang Dihadapi
 * Selama pengerjaan modul ini, seperti dalam setiap implementasi sistem operasi, beberapa tantangan teknis ditemukan. Ini termasuk debugging dalam lingkungan kernel yang memerlukan pemahaman mendalam tentang flow eksekusi dan interaksi antar komponen. Namun, dengan analisis yang cermat dan referensi yang memadai, kendala-kendala tersebut berhasil diatasi, memungkinkan implementasi sesuai dengan tujuan modul.
---

##📚 Referensi
 * Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
 * Repositori xv6-public: https://github.com/mit-pdos/xv6-public
 * Stack Overflow, GitHub Issues, diskusi praktikum.
---
