#📝 Laporan Tugas Akhir
Mata Kuliah: Sistem Operasi
Semester: Genap / Tahun Ajaran 2024–2025
Nama: <Nama Lengkap>
NIM: <Nomor Induk Mahasiswa>
Modul yang Dikerjakan:
Modul 4 – Subsistem Kernel Alternatif (xv6-public)
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

### 📍 Contoh Output `cowtest`:

```
Child sees: Y
Parent sees: X
```

### 📍 Contoh Output `shmtest`:

```
Child reads: A
Parent reads: B
```

### 📍 Contoh Output `chmodtest`:

```
Write blocked as expected
```

Jika ada screenshot:

```
![hasil cowtest](./screenshots/cowtest_output.png)
```

---

## ⚠️ Kendala yang Dihadapi

Tuliskan kendala (jika ada), misalnya:

* Salah implementasi `page fault` menyebabkan panic
* Salah memetakan alamat shared memory ke USERTOP
* Proses biasa bisa akses audit log (belum ada validasi PID)

---

## 📚 Referensi

Tuliskan sumber referensi yang Anda gunakan, misalnya:

* Buku xv6 MIT: [https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf)
* Repositori xv6-public: [https://github.com/mit-pdos/xv6-public](https://github.com/mit-pdos/xv6-public)
* Stack Overflow, GitHub Issues, diskusi praktikum

---
