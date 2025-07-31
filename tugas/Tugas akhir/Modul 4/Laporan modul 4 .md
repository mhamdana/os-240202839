#📝 Laporan Tugas Akhir
Mata Kuliah: Sistem Operasi
Semester: Genap / Tahun Ajaran 2024–2025
```
Nama: < Muhammad Khamdan Azkiya >
NIM: <240202839>
Modul yang Dikerjakan:
Modul 4 – Subsistem Kernel Alternatif (xv6-public)
```
---

##📌 Deskripsi Singkat Tugas
 * Modul 4 – Subsistem Kernel Alternatif (xv6-public):
   Mengimplementasikan system call chmod(path, mode) untuk mengatur mode akses file (read-only atau read-write) dan menambahkan driver pseudo-device /dev/random untuk menghasilkan byte acak.
---

##🛠️ Rincian Implementasi
 * Bagian A – System Call chmod():
   * Menambahkan field short mode; pada struct inode di file file.h.
   * Menambahkan system call chmod() dengan nomor SYS_chmod (27) di syscall.h, mendeklarasikannya di user.h, menambahkan entri di usys.S, dan mendaftarkannya di syscall.c.
   * Mengimplementasikan fungsi sys_chmod() di sysfile.c untuk mengubah nilai ip->mode pada inode file yang dituju, disertai iupdate(ip) untuk persistensi perubahan.
   * Memodifikasi fungsi filewrite() di file.c untuk mencegah penulisan jika f->ip->mode adalah 1 (read-only).
   * Membuat program uji chmodtest.c untuk memverifikasi fungsionalitas chmod() dan pencegahan penulisan.
 * Bagian B – Device Pseudo /dev/random:
   * Membuat file baru random.c yang berisi fungsi randomread() untuk menghasilkan byte acak.
   * Mendaftarkan randomread sebagai handler read untuk major device 3 di devsw[] dalam file.c.
   * Menambahkan panggilan mknod("/dev/random", 1, 3); di init.c untuk membuat device node /dev/random.
   * Membuat program uji randomtest.c untuk membaca dan menampilkan byte dari /dev/random.
 * Integrasi Makefile:
   * Menambahkan _chmodtest dan _randomtest ke daftar UPROGS di Makefile agar program uji dapat dikompilasi.

##✅ Uji Fungsionalitas
 * chmodtest: untuk menguji chmod() dan memastikan file read-only tidak bisa ditulis.
 * randomtest: untuk menguji driver pseudo-device /dev/random dan memverifikasi output byte acak.
---

## 📷 Hasil Uji

Lampirkan hasil uji berupa screenshot atau output terminal. Contoh:

###📍 Output Konsol XV6:

```
Booting from Hard Disk...xv6...
cpu1: starting 1
cpu0: starting 0
sb: size 1000 nblocks 941 ninodes 200 nlog 30 logstart 2 inodestart 32 bmap start 58
init: starting sh
$ chmodtest
Write blocked as expected
$ randomtest
159 114 41 116 67 198 109 232
$
```
---

##⚠️ Kendala yang Dihadapi
 * Ketidaksesuaian Lokasi Deklarasi struct inode pada Instruksi Modul:
   Instruksi modul awal menyarankan penambahan field mode untuk struct inode di fs.h. Namun, pada lingkungan xv6 yang digunakan, struct inode tidak ditemukan di fs.h (yang justru berisi definisi d_inode atau struktur terkait disk lainnya). Sebaliknya, struct inode yang relevan untuk manipulasi file ditemukan dan didefinisikan di file.h. Kendala ini mengharuskan analisis dan penyesuaian lokasi penambahan field mode secara manual ke file.h agar kompilasi dapat berhasil dan fungsionalitas chmod dapat diimplementasikan dengan benar sesuai struktur kode yang ada di lingkungan spesifik ini. Hal ini menunjukkan pentingnya memahami organisasi header file di xv6 yang digunakan dibandingkan hanya mengikuti instruksi secara langsung.
   
---

##📚 Referensi
 * Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
 * Repositori xv6-public: https://github.com/mit-pdos/xv6-public
 * Stack Overflow, GitHub Issues, diskusi praktikum 
---
