# Praktikum Modul 4 _(Module 4 Lab Work)_

</div>

### Daftar Soal _(Task List)_

- [Task 2 - LawakFS++](/task-2/)

### Laporan Resmi Praktikum Modul 4 _(Module 4 Lab Work Report)_

## Task 2 (LawakFS++ - A Cursed Filesystem with Censorship and Strict Access Policies)

### Pertama, kita install FUSE :
```
sudo apt-get update
sudo apt-get install fuse libfuse-dev gcc -y
```

### Buat source dir & mount point di home :
```
mkdir -p ~/praktikum_sisop/source_dir ~/praktikum_sisop/mount_point
```

### Buat beberapa file contoh untuk di `source dir` :
```
echo "Ini adalah dokumen RAHASIA" > secret.txt
echo "Ini file biasa" > document.pdf
echo "halloo" > readme
mkdir folder_biasa
dll
```

Isi `source_dir` :

![isi source_dir](https://github.com/user-attachments/assets/d5326876-44bc-4298-acaf-8daa2312ee18)

Isi `mount_point` :

![isi mount_point](https://github.com/user-attachments/assets/2743872e-b977-4666-9d41-d48b29630997)

### Buat direktori `lawakfs` & di isi file `lawak.c` :
```
kode lawak.c
```

### Buat File Konfigurasi `lawak.conf` di direktori `lawakfs` :
```
sudo tee /home/lawakfs/lawak.conf > /dev/null <<'EOF'
```
- isi nya :
```
FILTER_WORDS=ducati,ferrari,mu,chelsea,prx,onic,sisop
SECRET_FILE_BASENAME=secret
ACCESS_START=08:00
ACCESS_END=18:00
EOF
```

Hasil : 

![buat lawak conf](https://github.com/user-attachments/assets/459a0406-e006-41df-b124-498aa757a2c0)

Isi `lawakfs` :

![isi lawakfs](https://github.com/user-attachments/assets/f6125502-8a4d-4b2c-a7c3-3d2957722fd2)

### Buat File Log dan Atur Izin :
```
sudo touch /var/log/lawakfs.log
sudo chmod a+w /var/log/lawakfs.log
```

Hasil :

![Screenshot from 2025-06-22 17-15-08](https://github.com/user-attachments/assets/8353bc9e-220f-488b-9de8-770423fef1c5)

### Buat File Contoh di `source_dir` :
```
echo "tim sepak bola mu dan chelsea sering diejek. di dunia balap, ducati dan ferrari adalah rival. sementara di e-sport, tim onic dan prx sangat kuat. praktikum sisop sangat tidak menyenangkan" > /home/praktikum_sisop/source_dir/artikel.txt

echo "ini file rahasia yang hanya bisa dibuka pada jam kerja yaa." > /home/praktikum_sisop/source_dir/secret.log

wget -O /home/praktikum_sisop/source_dir/gambar.png https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png
```
Hasil :

![Screenshot from 2025-06-22 17-21-12](https://github.com/user-attachments/assets/191258d4-df31-4e54-a694-4372aa41c593)

### Compile lawak.c :
```
gcc -Wall lawak.c -o lawakfs `pkg-config fuse --cflags --libs`
```

Hasil :

![compile](https://github.com/user-attachments/assets/8df7f68a-4df6-4ee6-a2b1-9849e593b82b)

### Run FUSE, hubungkan source dir ke mount point :
```
./lawakfs ~/praktikum_sisop/source_dir ~/praktikum_sisop/mount_point
```

Hasil : 

![Screenshot from 2025-06-22 17-25-38](https://github.com/user-attachments/assets/4591e5fe-dd81-48b4-b6b0-55decc65f31d)

### Test soal A :
```
ls -l ~/praktikum_sisop/mount_point
```
- Output seharusnya : melihat `artikel`, `document`, `secret`, dll

```
cat ~/praktikum_sisop/mount_point/document
```
- Output seharusnya : menunjukkan isi file bisa dibaca meskipun ekstensinya disembunyikan

Hasil :

![ekstensi file](https://github.com/user-attachments/assets/38eb6f3a-287a-4cc9-a7f8-3809f9d32cd0)

### Test soal B :
```
ls -l ~/praktikum_sisop/mount_point
```
- Jika di dalam jam 8 - 18 : Output seharusnya = secret akan terlihat di daftar file
- Jika di luar jam 8 - 18 : Output seharusnya = secret tidak akan terlihat di daftar file

```
cat ~/praktikum_sisop/mount_point/secret
```
- Jika di dalam jam 8 - 18 : Output seharusnya isi dari file secret
- Jika di luar jam 8 - 18 : Output seharusnya = `cat: /home/user/praktikum_sisop/mount_point/secret: No such file or directory`

Hasil :

![buka file secret di rentang 8 - 18 ](https://github.com/user-attachments/assets/0026b5f7-d7fb-456e-9699-577b8f033a34)

### Test soal C (Filler konten) :
```
cat /home/praktikum_sisop/mount_point/artikel
```
- Output seharusnya : semua kata terlarang (`mu`, `chelsea`, `ducati`, `ferrari`, `onic`, `prx`, `sisop`) akan berubah menjadi `lawak`

Hasil :

![filter kata](https://github.com/user-attachments/assets/0b94411e-27cf-4dff-b7ec-1fae505e17c5)

### Test soal D (Logging) :
```
tail -n 5 /var/log/lawakfs.log
```
- Output seharusnya : melihat entri log baru untuk setiap operasi `READ` dan `ACCESS` yang berhasil, dengan format `[YYYY-MM-DD HH:MM:SS] [UID] [ACTION] [PATH]`

Hasil :

![Screenshot from 2025-06-22 17-32-02](https://github.com/user-attachments/assets/52fb5715-8564-48f3-b6db-cee5555b657d)

### Test Operasi Tulis (Read-Only) :
```
touch /home/praktikum_sisop/mount_point/testfile
```
- Output seharusnya : `touch: cannot touch '.../testfile': Read-only file system`

### Unmount Filesystem :
```
sudo umount /home/praktikum_sisop/mount_point
```

## Penjelasan Kode :
Kode ini mengimplementasikan sebuah filesystem virtual menggunakan `FUSE` (Filesystem in Userspace) yang disebut `LawakFS++`. Filesystem ini bersifat `read-only `dan memiliki beberapa fitur unik sesuai dengan spesifikasi soal A hingga E, yaitu menyembunyikan ekstensi file, membatasi akses file berdasarkan waktu, memfilter konten file, mencatat aktivitas, dan menggunakan konfigurasi eksternal.

### Bagian 1: Inklusi Header, Definisi Global, dan Struktur Data
```
#define FUSE_USE_VERSION 28
#include <fuse.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <dirent.h>
#include <errno.h>
#include <sys/stat.h>
#include <time.h>
#include <stdlib.h>
#include <ctype.h>

static char *source_dir;
#define MAX_FILTER_WORDS 50
#define MAX_WORD_LEN 50

typedef struct {
    char secret_basename[NAME_MAX];
    int access_start_hour;
    int access_end_hour;
    char filter_words[MAX_FILTER_WORDS][MAX_WORD_LEN];
    int filter_word_count;
} AppConfig;

static AppConfig config;
```

### Bagian 2: Fungsi-Fungsi Pembantu (Helper Functions) :
1. `log_action` (Jawaban Soal D)
```
static void log_action(const char *action, const char *path) {
    // ... implementasi ...
}
```
- Fungsi ini bertanggung jawab untuk mencatat aktivitas ke file /var/log/lawakfs.log.

Membuka File Log: Membuka file log dalam mode a (append), yang berarti setiap log baru akan ditambahkan di akhir file tanpa menghapus konten lama.
Timestamp: Mengambil waktu saat ini menggunakan time() dan memformatnya menjadi YYYY-MM-DD HH:MM:SS menggunakan strftime.
Menulis Log: Menggunakan fprintf untuk menulis entri log dalam format yang ditentukan: [TIMESTAMP] [UID] [ACTION] [PATH]. UID didapatkan dari getuid().

2. `parse_config` (Jawaban Soal E)
```
static void parse_config() {
    // ... implementasi ...
}
```
- Fungsi ini membaca dan mem-parsing file konfigurasi lawak.conf saat filesystem diinisialisasi.

Nilai Default: Sebelum membaca file, fungsi ini mengisi struct config dengan nilai default. Ini adalah praktik yang baik untuk memastikan program tetap berjalan meskipun file konfigurasi tidak ada.
Membaca File: Membuka file /home/lawakfs/lawak.conf dalam mode baca.
Parsing per Baris: Membaca file baris per baris. sscanf digunakan untuk memisahkan kunci dan nilai berdasarkan karakter =.
Mengisi Konfigurasi: Berdasarkan key yang ditemukan, nilai yang sesuai di dalam struct config akan diperbarui.
Untuk FILTER_WORDS, strtok digunakan untuk memecah string yang dipisahkan koma menjadi kata-kata individual dan menyimpannya dalam array.

3. `is_secret_access_denied` (Jawaban Soal B)
```
static int is_secret_access_denied(const char *path) {
    // ... implementasi ...
}
```
- Fungsi ini adalah inti dari logika pembatasan akses berbasis waktu.

Ekstrak Basename: strrchr digunakan untuk mendapatkan nama file dari path lengkap.
Pengecekan Nama: Nama file dibandingkan dengan config.secret_basename yang didapat dari file konfigurasi.
Pengecekan Waktu: Jika namanya cocok, fungsi ini mendapatkan jam saat ini dari sistem dan membandingkannya dengan config.access_start_hour dan config.access_end_hour.
Return Value: Mengembalikan 1 (benar, akses ditolak) jika waktu saat ini di luar rentang yang diizinkan, dan 0 (salah, akses diizinkan) jika sebaliknya.

4. resolve_path (Jawaban Soal A - Mekanisme Pemetaan)
```
static int resolve_path(const char *fuse_path, char *real_path) {
    // ... implementasi ...
}
```
Ini adalah fungsi krusial untuk menangani file yang ekstensinya disembunyikan. Saat FUSE meminta akses ke file seperti /dokumen, fungsi ini bertugas menemukan file aslinya di direktori sumber (misalnya, /path/sumber/dokumen.pdf).

- Pengecekan Langsung: Pertama, fungsi ini mencoba apakah path yang diminta ada secara langsung (misalnya, sebuah direktori).

- Pencarian Manual: Jika tidak ditemukan, fungsi ini akan:
Membuka direktori induk dari path yang diminta.

- Melakukan iterasi pada setiap entri di dalam direktori tersebut.
Untuk setiap entri, ia akan "menyembunyikan" ekstensinya secara virtual (dengan mengganti . terakhir dengan \0).

- Membandingkan nama yang sudah disembunyikan ini dengan nama file yang diminta oleh FUSE.
Jika cocok, ia akan menyalin path asli (dengan ekstensi) ke real_path dan mengembalikan sukses.
Return Value: Mengembalikan 0 jika path asli ditemukan, atau -ENOENT jika tidak.

5. `base64_encode` dan `is_binary` (Jawaban Soal C)
```
char *base64_encode(...) { /*...*/ }
int is_binary(...) { /*...*/ }
```
- `is_binary` : Fungsi heuristik sederhana untuk mendeteksi file biner. Ia mengasumsikan file adalah biner jika menemukan karakter NULL (\0) di dalamnya, karena ini jarang terjadi pada file teks biasa.

- `base64_encode` : Implementasi standar dari algoritma encoding Base64. Fungsi ini mengambil data biner mentah dan mengubahnya menjadi representasi teks yang aman untuk ditampilkan.

### Bagian 3: Implementasi Operasi FUSE
Ini adalah fungsi-fungsi yang dipetakan langsung ke operasi system call pada filesystem.

1. `lawak_getattr`
Fungsi ini dipanggil oleh perintah seperti ls -l untuk mendapatkan atribut file.
Cek Akses Secret: Pertama, panggil is_secret_access_denied(). Jika akses ditolak, kembalikan -ENOENT agar file tampak tidak ada.
Resolve Path: Panggil resolve_path() untuk mendapatkan path asli dari file yang mungkin disembunyikan ekstensinya.
Panggil lstat: Panggil lstat pada real_path untuk mendapatkan atribut file dan mengisinya ke stbuf.

2. `lawak_readdir` (Jawaban Soal A & B)
Fungsi ini dipanggil oleh ls untuk menampilkan isi direktori.
Buka Direktori: Buka direktori sumber yang sesuai.
Iterasi Entri: Lakukan iterasi pada setiap entri di dalam direktori.
Cek File Secret (Soal B): Sebelum menampilkan entri, cek apakah entri tersebut adalah file rahasia yang aksesnya sedang dibatasi. Jika ya, continue untuk melewatinya.
Sembunyikan Ekstensi (Soal A): Jika entri adalah file reguler (S_ISREG), cari titik terakhir pada namanya dan ganti dengan \0 untuk menyembunyikan ekstensinya.
Isi Buffer: Gunakan filler() untuk menambahkan nama file (yang mungkin sudah dimodifikasi) ke daftar yang akan ditampilkan.

3. `lawak_access`, `lawak_open`, `lawak_access`: Memeriksa izin akses.
Cek akses file secret.
Gunakan resolve_path untuk menemukan path asli.
Panggil access() pada path asli.
Jika berhasil, panggil log_action("ACCESS", ...) untuk logging (Soal D).
lawak_open: Membuka file.
Cek akses file secret.
Paksa Read-Only: Cek fi->flags. Jika flag bukan O_RDONLY, kembalikan -EROFS (Read-only file system).
Gunakan resolve_path untuk menemukan path asli dan panggil open() pada path tersebut.

4. `lawak_read` (Jawaban Soal C)
Ini adalah fungsi paling kompleks yang mengimplementasikan penyaringan konten dinamis.
Baca Seluruh Konten: Seluruh isi file asli dibaca dari file handle (fi->fh) ke dalam memori (original_content). Ini diperlukan agar filtering bisa dilakukan pada seluruh konteks file.

Deteksi dan Proses:
Panggil is_binary() pada konten tersebut.
Jika Biner: Panggil base64_encode() untuk mengubah konten menjadi string Base64.
Jika Teks: Lakukan proses pencarian dan penggantian kata:
Buat buffer baru (processed_content).
Lakukan loop untuk mencari kemunculan kata-kata terfilter (FILTER_WORDS) secara case-insensitive menggunakan strcasestr.
Salin bagian teks sebelum kata yang ditemukan, lalu salin kata "lawak", dan lanjutkan pencarian dari posisi setelah kata yang diganti.
Jika tidak ada lagi kata terlarang, salin sisa teks.
Salin ke Buffer FUSE: Setelah konten diproses (processed_content), salin bagian data yang diminta (berdasarkan size dan offset) ke buf yang disediakan FUSE.
Logging: Jika pembacaan berhasil, panggil log_action("READ", ...) (Soal D).
Bebaskan Memori: free() semua memori yang dialokasikan.

### Bagian 4: Struktur Operasi dan Fungsi main
Bagian terakhir ini mengikat semuanya menjadi satu dan menjalankan FUSE.
```
static struct fuse_operations lawak_oper = {
    // ... pemetaan fungsi ...
};

int main (int argc, char *argv[]) {
    // ... implementasi ...
}
```

`lawak_oper`: struct fuse_operations ini berfungsi sebagai "peta" yang memberitahu FUSE fungsi mana yang harus dipanggil untuk setiap operasi filesystem. Misalnya, saat ls dijalankan, FUSE akan melihat peta ini dan memanggil fungsi lawak_readdir kita. Operasi tulis seperti write, mkdir, unlink, dll., tidak didefinisikan di sini. Akibatnya, FUSE akan menolaknya secara default, yang secara efektif membuat filesystem ini read-only.

`main` :

`Validasi Argumen`: Memastikan pengguna memberikan dua argumen: direktori sumber dan titik kait (mount point).

`Panggil parse_config()` : Memuat konfigurasi dari file sebelum melakukan hal lain.

`Setup Path` : Menggunakan realpath() untuk mendapatkan path absolut dari direktori sumber, yang menghindari masalah dengan path relatif.

`Panggil fuse_main()` : Fungsi ini adalah titik masuk ke loop utama FUSE. Ia mengambil alih program, me-mount filesystem pada titik kait yang ditentukan, dan menggunakan lawak_oper untuk menangani semua permintaan operasi file hingga program dihentikan.
