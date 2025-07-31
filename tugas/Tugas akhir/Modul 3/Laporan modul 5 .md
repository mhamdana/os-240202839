#📝 Laporan Tugas Akhir
Mata Kuliah: Sistem Operasi
Semester: Genap / Tahun Ajaran 2024–2025
Nama: <Muhammad Khamdan Azkiya>
NIM: <240202839>
Modul yang Dikerjakan: Modul 5 – Audit dan Keamanan Sistem (xv6-public)
---

##📌 Deskripsi Singkat Tugas
 * Modul 5 – Audit dan Keamanan Sistem (xv6-public):
   Mengimplementasikan sistem audit log pada kernel xv6 untuk merekam setiap system call yang dijalankan. Log ini mencakup PID proses, nomor system call, dan tick waktu eksekusi. Selain itu, ditambahkan system call get_audit_log(char *buf, int max) yang memungkinkan pembacaan audit log dari user-space, dengan pembatasan akses hanya untuk proses dengan PID 1 (proses init).
---

##🛠️ Rincian Implementasi
Secara ringkas, berikut adalah langkah-langkah implementasi yang dilakukan:
 * Penambahan Struktur Audit Log:
   * Mendefinisikan MAX_AUDIT (128) dan struktur audit_entry (berisi pid, syscall_num, tick) di syscall.c.
   * Mendeklarasikan array global audit_log[MAX_AUDIT] dan audit_index = 0; di syscall.c untuk menyimpan entri log.
   * Mendeklarasikan struct audit_entry dan MAX_AUDIT serta prototype fungsi get_audit_log di defs.h agar dapat dipanggil dari kernel-space.
   * Mendefinisikan ulang struct audit_entry di user.h agar dapat diakses dari user-space.
 * Pencatatan System Call di syscall():
   * Memodifikasi fungsi syscall() di syscall.c untuk mencatat setiap system call yang valid ke dalam audit_log jika audit_index belum mencapai MAX_AUDIT. Data yang dicatat meliputi PID proses yang memanggil syscall, nomor syscall, dan nilai ticks saat syscall dieksekusi.
 * Penambahan System Call get_audit_log():
   * Menetapkan nomor system call baru SYS_get_audit_log (nilai 28) di syscall.h.
   * Mendeklarasikan fungsi int get_audit_log(void *buf, int max); di user.h.
   * Menambahkan entri SYSCALL(get_audit_log) di usys.S.
   * Meregistrasikan fungsi kernel sys_get_audit_log di syscall.c ke dalam array syscalls[].
   * Mengimplementasikan fungsi sys_get_audit_log() di sysproc.c. Fungsi ini memvalidasi argumen input (buf dan max), memeriksa apakah proses pemanggil adalah PID 1 (untuk kontrol akses), lalu menyalin data log dari audit_log di kernel ke buffer yang disediakan oleh user-space menggunakan memmove.
 * Pembuatan Program Uji audit.c:
   * Membuat program audit.c di user-space yang memanggil get_audit_log() untuk membaca dan menampilkan isi audit log. Program ini mencetak pesan "Access denied or error." jika tidak memiliki izin atau terjadi kesalahan.
 * Penambahan Program Uji ke Makefile:
   * Menambahkan _audit\ ke daftar UPROGS di Makefile.
---

## ✅ Uji Fungsionalitas
Program uji yang digunakan adalah:
 * audit: Untuk menguji fungsionalitas get_audit_log() dan memverifikasi bahwa audit log berhasil dicatat dan dapat diakses dengan benar oleh proses yang memiliki izin (PID 1).
---

## 📷 Hasil Uji

Lampirkan hasil uji berupa screenshot atau output terminal. Contoh:

###📍 Output audit (jika dijalankan oleh proses selain PID 1):

```
Access denied or error.
```

Penjelasan Output: Output ini menunjukkan bahwa pembatasan akses berhasil diterapkan, di mana proses biasa (bukan PID 1) tidak diizinkan untuk membaca audit log.


###📍 Output audit (jika dijalankan sebagai PID 1, misalnya dengan memodifikasi init.c):

```
Booting from Hard Disk...xv6...
cpu1: starting 1
cpu0: starting 0
sb: size 1000 nblocks 941 ninodes 200 nlog 30 logstart 2 inodestart 32 bmap start 58
init: attempting to start audit program as PID 1
=== Audit Log ===
[0] PID=1 SYSCALL=7 TICK=2
[1] PID=1 SYSCALL=17 TICK=2
[2] PID=1 SYSCALL=16 TICK=2
[3] PID=1 SYSCALL=16 TICK=2
[4] PID=1 SYSCALL=16 TICK=2
[5] PID=1 SYSCALL=16 TICK=2
[6] PID=1 SYSCALL=16 TICK=2
[7] PID=1 SYSCALL=16 TICK=2
[8] PID=1 SYSCALL=16 TICK=2
[9] PID=1 SYSCALL=16 TICK=2
[10] PID=1 SYSCALL=16 TICK=2
[11] PID=1 SYSCALL=16 TICK=2
[12] PID=1 SYSCALL=16 TICK=2
[13] PID=1 SYSCALL=16 TICK=2
[14] PID=1 SYSCALL=16 TICK=2
[15] PID=1 SYSCALL=16 TICK=2
[16] PID=1 SYSCALL=16 TICK=2
[17] PID=1 SYSCALL=16 TICK=2
[18] PID=1 SYSCALL=16 TICK=2
[19] PID=1 SYSCALL=16 TICK=2
[20] PID=1 SYSCALL=16 TICK=2
[21] PID=1 SYSCALL=16 TICK=2
[22] PID=1 SYSCALL=16 TICK=2
[23] PID=1 SYSCALL=16 TICK=2
[24] PID=1 SYSCALL=16 TICK=2
[25] PID=1 SYSCALL=16 TICK=2
[26] PID=1 SYSCALL=16 TICK=2
[27] PID=1 SYSCALL=16 TICK=2
[28] PID=1 SYSCALL=16 TICK=2
[29] PID=1 SYSCALL=16 TICK=2
[30] PID=1 SYSCALL=16 TICK=2
[31] PID=1 SYSCALL=16 TICK=2
[32] PID=1 SYSCALL=16 TICK=2
[33] PID=1 SYSCALL=16 TICK=2
[34] PID=1 SYSCALL=16 TICK=2
[35] PID=1 SYSCALL=16 TICK=2
[36] PID=1 SYSCALL=16 TICK=2
[37] PID=1 SYSCALL=16 TICK=2
[38] PID=1 SYSCALL=16 TICK=2
[39] PID=1 SYSCALL=16 TICK=2
[40] PID=1 SYSCALL=16 TICK=2
[41] PID=1 SYSCALL=16 TICK=2
[42] PID=1 SYSCALL=16 TICK=2
[43] PID=1 SYSCALL=16 TICK=2
[44] PID=1 SYSCALL=16 TICK=2
[45] PID=1 SYSCALL=16 TICK=2
[46] PID=1 SYSCALL=16 TICK=2
[47] PID=1 SYSCALL=16 TICK=2
[48] PID=1 SYSCALL=16 TICK=2
[49] PID=1 SYSCALL=16 TICK=2
[50] PID=1 SYSCALL=16 TICK=2
[51] PID=1 SYSCALL=16 TICK=2
[52] PID=1 SYSCALL=16 TICK=2
[53] PID=1 SYSCALL=16 TICK=2
[54] PID=1 SYSCALL=7 TICK=3
[55] PID=1 SYSCALL=28 TICK=3
lapicid 0: panic: init exiting
```
Penjelasan Output: Output ini menunjukkan daftar system call yang berhasil direkam oleh audit log, lengkap dengan PID (yang dalam kasus ini adalah 1 karena audit dijalankan sebagai init), nomor system call, dan tick waktu. Hal ini mengkonfirmasi keberhasilan pencatatan log dan pengambilan data dari kernel ke user-space. Pesan panic: init exiting adalah perilaku normal xv6 ketika program init selesai atau mengalami kondisi tertentu setelah menjalankan tugasnya.
Screenshot Pengerjaan:
Berikut adalah tangkapan layar dari file-file yang dimodifikasi dan output hasil uji:
 * syscall.c (Deklarasi global audit_log, audit_index)
 * syscall.c (Pencatatan System Call di syscall() )
 * syscall.h (Penambahan SYS_get_audit_log)
 * usys.S (Entri get_audit_log)
 * sysproc.c (Implementasi sys_get_audit_log)
 * user.h (Deklarasi struct audit_entry dan get_audit_log)
 * defs.h (Deklarasi MAX_AUDIT, struct audit_entry, dan fungsi terkait)
 * audit.c (Program Pengujian)
 * Makefile (Penambahan _audit)
 * Terminal xv6: Booting dan Output audit yang sukses (dijalankan sebagai PID 1)

## ⚠️ Kendala yang Dihadapi
 * Selama pengerjaan modul ini, beberapa tantangan teknis ditemukan. Ini termasuk memastikan konsistensi definisi struktur data (struct audit_entry) an Ratara kernel-space dan user-space, serta penanganan copyin/copyout yang tepat saat mentransfer data log dari kernel ke user-space. Selain itu, pemahaman yang tepat tentang siklus hidup system call di xv6 sangat penting untuk menempatkan logika implementasi pada posisi yang benar dan memastikan perilaku sistem sesuai harapan. Meskipun demikian, kendala-kendala tersebut berhasil diatasi dengan analisis kode yang cermat dan referensi yang relevan, memungkinkan implementasi modul ini sesuai dengan tujuannya.
---

## 📚 Referensi
 * Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
 * Repositori xv6-public: https://github.com/mit-pdos/xv6-public
 * Stack Overflow, GitHub Issues, diskusi praktikum.
 * 
---
