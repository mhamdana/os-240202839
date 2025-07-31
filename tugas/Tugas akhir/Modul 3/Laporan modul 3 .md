📝 Laporan Tugas Akhir
Mata Kuliah: Sistem Operasi
Semester: Genap / Tahun Ajaran 2024–2025
```
Nama: < Muhammad Khamdan Azkiya >
NIM: <240202839>
Modul yang Dikerjakan: Modul 3 – Manajemen Memori Tingkat Lanjut (xv6-public x86)
```
---

📌 Deskripsi Singkat Tugas
Modul 3 ini berfokus pada implementasi fitur manajemen memori tingkat lanjut dalam sistem operasi xv6, yaitu:
 * Copy-on-Write (CoW) Fork: Mengubah perilaku fork() agar tidak langsung menyalin seluruh memori proses induk ke proses anak. Sebaliknya, halaman memori akan dibagi secara read-only dan penyalinan (copy) hanya akan terjadi saat salah satu proses mencoba menulis ke halaman tersebut (melalui page fault). Implementasi ini memanfaatkan mekanisme reference count untuk melacak jumlah referensi ke setiap halaman fisik.
 * Shared Memory ala System V: Menambahkan dua system call baru, yaitu shmget(int key) untuk mendapatkan atau membuat segmen shared memory dan shmrelease(int key) untuk melepaskan segmen shared memory. Fitur ini memungkinkan antar proses untuk berbagi satu halaman memori fisik dengan sistem reference count sendiri.
---
   
🛠️ Rincian Implementasi
Implementasi modul ini melibatkan modifikasi pada beberapa file kernel xv6 dan penambahan system call baru. Berikut adalah rinciannya:
1. Implementasi Copy-on-Write Fork (CoW)
 * Penambahan ref_count[] dan fungsi incref/decref di vm.c:
   * Mendefinisikan ref_count[NPAGE] sebagai array global untuk melacak jumlah referensi setiap halaman fisik.
   * Mengimplementasikan fungsi incref(char *pa) untuk menambah reference count halaman fisik yang ditunjuk oleh pa.
   * Mengimplementasikan fungsi decref(char *pa) untuk mengurangi reference count halaman fisik. Jika reference count mencapai 0, halaman tersebut akan dikosongkan menggunakan kfree(pa).
   * Perubahan penting: Memastikan setiap panggilan kalloc() diikuti dengan incref() dan setiap kfree() diatur agar terintegrasi dengan decref(). Ini dilakukan di kalloc.c dan vm.c pada fungsi deallocuvm dan freevm.
 * Penambahan PTE_COW di mmu.h:
   * Mendefinisikan flag baru PTE_COW (0x200) dalam mmu.h untuk menandai halaman yang menggunakan mekanisme Copy-on-Write.
 * Modifikasi copyuvm() menjadi cowuvm() di vm.c:
   * Mengganti logika penyalinan penuh dengan mekanisme CoW.
   * Dalam cowuvm(), ketika memetakan halaman dari proses induk ke proses anak:
     * Flag PTE_W (writable) pada PTE induk dihapus.
     * Flag PTE_COW ditambahkan pada PTE induk dan anak.
     * Reference count untuk halaman fisik yang dibagikan (P2V(pa)) ditingkatkan menggunakan incref().
 * Perubahan pada fork() di proc.c:
   * Baris np->pgdir = copyuvm(curproc->pgdir, curproc->sz); diganti menjadi np->pgdir = cowuvm(curproc->pgdir, curproc->sz); untuk memanfaatkan implementasi CoW.
 * Penanganan Page Fault T_PGFLT di trap.c:
   * Menambahkan blok penanganan page fault dalam fungsi trap().
   * Ketika T_PGFLT terjadi:
     * Mendapatkan alamat virtual (va) yang menyebabkan page fault.
     * Mencari PTE yang terkait dengan va.
     * Jika PTE tidak valid, tidak ada flag PTE_P, atau tidak ada flag PTE_COW, maka ini adalah page fault yang tidak diharapkan, dan proses akan dihentikan.
     * Jika page fault terjadi pada halaman yang seharusnya read-only (tidak ada PTE_W tapi PTE_COW aktif), ini menandakan percobaan tulis pertama.
     * Alokasikan halaman memori fisik baru (kalloc()).
     * Salin data dari halaman fisik lama ke halaman fisik baru.
     * Kurangi reference count halaman lama (decref((char*)P2V(pa))).
     * Perbarui PTE yang menyebabkan page fault agar menunjuk ke halaman fisik baru, dengan menambahkan flag PTE_W, PTE_U, dan PTE_P, serta menghapus flag PTE_COW.
     * Memanggil lcr3(V2P(proc->pgdir)) untuk me-flush TLB dan memastikan perubahan PTE berlaku.
2. Implementasi Shared Memory ala System V
 * Penambahan Struktur shmtab di vm.c:
   * Mendefinisikan MAX_SHM 16 untuk jumlah maksimum segmen shared memory.
   * Mendefinisikan struktur shmtab array global yang berisi key (identifikasi segmen), frame (pointer ke halaman fisik), dan refcount (jumlah proses yang menggunakan segmen tersebut).
 * Implementasi shmget() di sysproc.c:
   * Menerima key sebagai argumen.
   * Mencari segmen shared memory yang sudah ada dengan key yang sama. Jika ditemukan, refcount akan ditingkatkan dan halaman akan dipetakan ke ruang alamat pengguna pada alamat USERTOP - (i+1)*PGSIZE.
   * Jika tidak ditemukan, akan mencari slot kosong di shmtab.
   * Mengalokasikan halaman fisik baru (kalloc()), menginisialisasi refcount menjadi 1, dan membersihkan memori dengan memset.
   * Memetakan halaman fisik ke ruang alamat virtual proses pada USERTOP - (i+1)*PGSIZE dengan flag PTE_W|PTE_U.
   * Mengembalikan alamat virtual yang dipetakan atau -1 jika gagal.
 * Implementasi shmrelease() di sysproc.c:
   * Menerima key sebagai argumen.
   * Mencari segmen shared memory dengan key yang cocok.
   * Mengurangi refcount. Jika refcount mencapai 0, halaman fisik akan dikosongkan (kfree()) dan key diatur ulang menjadi 0.
   * Mengembalikan 0 jika berhasil atau -1 jika tidak ditemukan.
 * Registrasi System Call:
   * syscall.h: Menambahkan definisi SYS_shmget (25) dan SYS_shmrelease (26).
   * user.h: Menambahkan deklarasi fungsi void* shmget(int); dan int shmrelease(int);.
   * usys.S: Menambahkan SYSCALL(shmget) dan SYSCALL(shmrelease).
   * syscall.c: Menambahkan entri sys_shmget dan sys_shmrelease ke dalam tabel syscalls[].
   * Tambahan di sysproc.c: Menambahkan deklarasi eksternal untuk fungsi-fungsi kernel seperti mappages, kfree, kalloc, dan allocuvm yang diperlukan oleh implementasi shared memory.
  ---
✅ Uji Fungsionalitas
Program uji yang digunakan untuk memverifikasi fungsionalitas:
 * cowtest: Untuk menguji implementasi Copy-on-Write pada fork(). Program ini mengalokasikan memori, menulis nilai awal, lalu melakukan fork(). Proses anak mengubah nilai tersebut, dan kedua proses kemudian mencetak nilai yang mereka lihat.
 * shmtest: Untuk menguji fungsionalitas shmget() dan shmrelease(). Program ini membuat segmen shared memory, menulis nilai, kemudian fork. Proses anak mengakses dan memodifikasi shared memory tersebut, lalu keduanya mencetak nilai yang mereka lihat setelah modifikasi.
---

📷 Hasil Uji
---

📍 Output shmtest:
```
Child reads: A
Parent reads: B
```

Penjelasan: Output menunjukkan bahwa proses anak berhasil membaca nilai 'A' yang ditulis oleh proses induk sebelum fork. Kemudian, proses anak menulis 'B' ke shared memory, dan proses induk setelah wait() berhasil membaca nilai 'B' tersebut. Ini mengkonfirmasi bahwa shared memory bekerja dengan benar, memungkinkan komunikasi antarproses melalui memori yang sama.

📍 Output cowtest:
```
Starting cowtest...
Original value: X
Child process created.
Child sees: Y
Parent sees: X
cowtest finished.
```

Penjelasan: Output ini membuktikan bahwa implementasi Copy-on-Write berhasil. Proses induk awalnya memiliki nilai 'X' pada halaman memori. Ketika fork() dipanggil, halaman tersebut dibagi secara read-only. Saat proses anak mencoba menulis 'Y', page fault terjadi, halaman disalin, dan proses anak kini memiliki salinan sendiri dengan nilai 'Y'. Namun, proses induk masih melihat nilai aslinya 'X', menunjukkan bahwa halaman aslinya tidak terpengaruh oleh penulisan oleh anak.
---

⚠️ Kendala yang Dihadapi
 * Integrasi incref/decref dengan kalloc/kfree: Awalnya terdapat kesalahan dalam memastikan setiap alokasi halaman fisik (misalnya di kalloc.c) langsung diikuti oleh pemanggilan incref() dan setiap pelepasan halaman fisik (misalnya di deallocuvm, freevm) memanggil decref(). Ini menyebabkan reference count tidak akurat dan berpotensi menyebabkan memory leak atau use-after-free jika halaman dibebaskan terlalu cepat. Solusinya adalah meninjau kembali semua titik di mana kalloc dan kfree dipanggil dan menyesuaikan reference count secara manual.
 * Penanganan Page Fault yang Tepat: Menentukan kondisi yang tepat untuk memicu penyalinan pada page fault dan bagaimana memperbarui PTE (termasuk flag PTE_COW dan PTE_W) membutuhkan pemahaman mendalam tentang arsitektur memori xv6. Kesalahan dalam logika ini dapat menyebabkan panic kernel atau perilaku yang tidak terduga.
 * Pemetaan Alamat Shared Memory: Memastikan shared memory dipetakan ke alamat yang konsisten dan aman (misalnya di USERTOP atau di bawahnya) untuk menghindari konflik dengan area memori proses lainnya.
 * Output Terkadang Kosong: Terkadang, setelah menjalankan program uji (cowtest atau shmtest), output pada terminal tidak muncul atau kosong. Hal ini menyebabkan kebingungan apakah program berjalan dengan benar atau terjadi masalah internal.
📚 Referensi
 * Buku xv6 MIT: https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf
 * Repositori xv6-public: https://github.com/mit-pdos/xv6-public
 * Diskusi dan panduan dari Gemini AI.
