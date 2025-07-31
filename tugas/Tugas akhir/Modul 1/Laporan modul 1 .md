📝 Laporan Tugas Akhir
Mata Kuliah: Sistem Operasi
Semester: Genap / Tahun Ajaran 2024–2025
Nama: <Muhammad Khamdan Azkiya>
NIM: <240202839>
Modul yang Dikerjakan: Modul 1 – System Call dan Instrumentasi Kernel
📌 Deskripsi Singkat Tugas
 * Modul 1 – System Call dan Instrumentasi Kernel:
   Menerapkan dua system call baru pada sistem operasi xv6-public (x86), yaitu getpinfo() untuk mendapatkan informasi proses aktif (PID, ukuran memori, dan nama proses) dan getReadCount() untuk mengembalikan total jumlah pemanggilan fungsi read() sejak sistem boot.
🛠️ Rincian Implementasi
Berikut adalah langkah-langkah implementasi yang telah saya lakukan:
 * Menambahkan Struktur Data Baru: Mendefinisikan struct pinfo di proc.h untuk menyimpan informasi proses (PID, memori, nama) dan menambahkan deklarasi readcount global di sysproc.c sebagai penghitung pemanggilan read().
 * Mendaftarkan System Call Baru: Menetapkan nomor system call baru (SYS_getpinfo dan SYS_getreadcount) di syscall.h, mendeklarasikannya di user.h dan syscall.c, serta menambahkan entri ke tabel system call di syscall.c dan usys.S.
 * Mengimplementasikan Fungsi Kernel: Membuat implementasi fungsi sys_getpinfo() dan sys_getreadcount() di sysproc.c. Fungsi sys_getpinfo() mengakses tabel proses kernel untuk mengupulkan informasi proses aktif dan menyalinnya ke ruang pengguna, sementara sys_getreadcount() mengembalikan nilai dari variaeadcount.
 * Memodifikasi sys_read(): Menambahkan inkrementasi readcount di awal fungsi sys_read() pada sysfile.c untuk menghitung setiap pemanggilan read().
 * Membuat Program Penguji User-Level: Mengembangkan dua program pengguna, ptest.c untuk menguji fungsionalitas getpinfo() dengan mencetak daftar proses aktif, dan rtest.c untuk menguji getReadCount() dengan menampilkan nilai readcount sebelum dan sesudah pemanggilan read().
 * Mendaftarkan Program Penguji: Menambahkan _ptest dan _rtest ke dalam daftar UPROGS di Makefile agar dapat dikompilasi dan dijalankan di xv6.
✅ Uji Fungsionalitas
Saya menggunakan program uji berikut untuk memverifikasi fungsionalitas system call yang diimplementasikan:
 * ptest: Digunakan untuk menguji system call getpinfo() dan menampilkan daftar PID, ukuran memori, serta nama dari proses-proses yang sedang aktif.
 * rtest: Digunakan untuk menguji system call getReadCount() dan menunjukkan perubahan total jumlah pemanggilan read() setelah fungsi read() dipanggil.
📷 Hasil Uji
Berikut adalah hasil uji yang didapatkan dari menjalankan program penguji di shell xv6:
📍 Contoh Output ptest:
PID	MEM	NAME
1	4096	init
2	2048	sh
3	2048	ptest
...

📍 Contoh Output rtest:
Read Count Sebelum: 4
hello
Read Count Setelah: 5

⚠️ Kendala yang Dihadapi
Selama proses implementasi, saya menghadapi kendala sebagai berikut:
 * Akses ke Struktur Proses: Pada awalnya, terdapat kesalahan dalam mengakses dan mengunci struktur proses global (ptable.proc dan ptable_lock atau ptable.lock) di sys_getpinfo(), yang menyebabkan potensi kondisi balapan atau kesalahan segmentasi. Hal ini diatasi dengan memastikan penggunaan kunci yang benar (acquire dan release) untuk melindungi akses ke struktur data kernel yang dibagikan.
 * Penyalinan Data ke Ruang Pengguna: Kesulitan dalam menyalin data dari kernel ke ruang pengguna dengan aman. argptr digunakan untuk mendapatkan pointer ke buffer ruang pengguna, dan safestrcpy untuk menyalin string nama proses dengan aman guna mencegah buffer overflow.
📚 Referensi
Berikut adalah sumber referensi yang saya gunakan dalam pengerjaan modul ini:
 * Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
 * Repositori xv6-public: https://github.com/mit-pdos/xv6-public
 * Diskusi praktikum dan materi perkuliahan terkait sistem operasi.
