# Penjelasan Kode LawakFS++ (Bagian A & B)
Kode ini mengimplementasikan sebuah filesystem sederhana menggunakan FUSE (Filesystem in Userspace) di C. Fokus utama dari implementasi ini adalah untuk memenuhi dua persyaratan dari soal "LawakFS++":

Bagian A: Ekstensi File Tersembunyi - Menyembunyikan ekstensi file saat direktori ditampilkan. 
Bagian B: Akses Berbasis Waktu - Membatasi akses ke file bernama "secret" hanya pada jam kerja (08:00 - 18:00). 

### Header dan Definisi Global :
```
#define FUSE_USE_VERSION 28 = memberitahu FUSE versi API mana yang kita gunakan
#include <fuse.h> Library utama untuk FUSE
#include <stdio.h>
#include <string.h>
#include <unistd.h> = system call tingkat rendah seperti access dan open
#include <fcntl.h> = system call tingkat rendah seperti access dan open.
#include <dirent.h> = operasi direktori seperti opendir dan readdir
#include <errno.h> = mengakses kode error sistem
#include <sys/stat.h> = mendapatkan informasi file (atribut) dengan lstat
#include <time.h> = mendapatkan dan memanipulasi waktu sistem
#include <stdlib.h>

static char *source_dir; =  menyimpan path ke direktori sumber yang akan "dicerminkan" oleh FUSE
#define SECRET_NAME "secret" = nama dasar file yang aksesnya akan dibatasi
#define ACCES_START 8 = jam mulai akses (8 pagi)
#define ACCES_END 18 = jam berakhir akses (6 sore)
```

### Fungsi `get_fullPath` :
```
static void get_fullPath(char *full_path, const char *path) {
    sprintf(full_path, "%s%s", source_dir, path);
}
```
- menggabungkan `source_dir` (misalnya, `/home/user/dokumen`) dengan path relatif dari FUSE (misalnya, `/file.txt`) untuk membentuk path absolut yang dapat dikenali oleh sistem operasi (hasilnya : `/home/user/dokumen/file.txt`)

### Fungsi `secret_access` (akses berbatas waktu) :
```
static int secret_access(const char *path) {
    const char *basename = strrchr(path, '/');
    if (basename) basename++;
    else basename = path;

    if (strcmp(basename, SECRET_NAME) == 0) {
        time_t now = time(NULL);
        struct tm *local_time = localtime(&now);
        int current_hour = local_time->tm_hour;

        if (current_hour < ACCES_START || current_hour >= ACCES_END) return 1;
    }
    return 0;
}
```
- `strrchr(path, '/')` mencari karakter / terakhir untuk mengisolasi nama file (misalnya, dari `/folder/secret` menjadi secret
- `strcmp(basename, SECRET_NAME)` membandingkan apakah nama file tersebut adalah file "secret"
- pengecekan Waktu = Jika namanya cocok, fungsi ini akan :
  - ambil waktu sistem saat ini menggunakan `time(NULL)`
  - konversinya ke waktu lokal dengan `localtime()`
  - ekstrak hanya jam `(tm_hour)`
  - meriksa apakah jam saat ini berada di luar rentang `ACCES_START (8)` dan `ACCES_END (18)`
- Return Value :
  - kembalikan 1 (berarti "tolak akses") jika nama file adalah "secret" & diluar jam
  - kembalikan 0 (berarti "izinkan") untuk semua kasus lainnya

### Fungsi `lawak_getattr` (mendapatkan atribut file) :
```
static int lawak_getattr(const char *path, struct stat *stbuf) { 
    if (secret_access(path)) {
        return -ENOENT;
    }
    char full_path[PATH_MAX];
    get_fullPath(full_path, path);
    int res = lstat(full_path, stbuf);
    if (res == -1) return -errno;
    return 0;
}
```
- dipanggil ketika sistem perlu mengetahui metadata sebuah file (ukuran, izin, dll.), misalnya saat menjalankan `ls -l`
- pengecekan akses : hal pertama yang dilakukannya adalah memanggil `secret_access(path)`
- menolak akses : jika `secret_access` mengembalikan `1`, fungsi ini langsung mengembalikan -`ENOENT (Error: No Such File or Directory)`
- akses diizinkan : jika akses diizinkan, fungsi ini akan melanjutkan untuk mendapatkan atribut file yang sebenarnya dari direktori sumber menggunakan `lstat`

### Fungsi `lawak_readdir` (membaca isi direktori) :
```
static int lawak_readdir(const char *path, void *buf, fuse_fill_dir_t filler, ...) {
    (void) offset;
    (void) fi;
    char full_path[PATH_MAX];
    get_fullPath(full_path, path);

    DIR *dp = opendir(full_path);
    if (dp == NULL) return -errno;

    struct dirent *de;
    while ((de = readdir(dp)) != NULL) {
        if (strcmp(de->d_name, ".") == 0 || strcmp(de->d_name, "..") == 0) {
            filler(buf, de->d_name, NULL, 0);
            continue;
        }
        char name_to_show[NAME_MAX];
        strcpy(name_to_show, de->d_name);
        char *last_dot = strrchr(name_to_show, '.');
        if (last_dot && last_dot != name_to_show) *last_dot = '\0';

        if filler(buf, name_to_show, NULL, 0) break;
    }
    closedir(dp);
    return 0;
}
```
- mengimplementasikan fitur Ekstensi File Tersembunyi dan dipanggil saat pengguna menjalankan perintah seperti `ls`
- buka direktori : membuka direktori yang sesuai di `source_dir`
- iterasi entri: melakukan looping untuk setiap entri (file/direktori) di dalamnya
- menyembunyikan ekstensi: bagian inti dari fitur A :
  - nama file asli (`de->d_name`) disalin ke `name_to_show`
  - `strrchr(name_to_show, '.'`) mencari posisi titik (.) terakhir pada nama file
  - jika ditemukan, karakter tersebut diganti dengan `'\0'` (karakter null terminator), yang secara efektif "memotong" string di titik tersebut dan menyembunyikan ekstensinya
  - `filler(...)` : Fungsi ini memasukkan nama file yang sudah dimodifikasi (tanpa ekstensi) ke dalam buffer yang akan ditampilkan kepada pengguna
 
### Fungsi `lawak_access`, `lawak_open`, dan `lawak_read` :
```
static int lawak_access(const char *path, int mask) {
    if (secret_access(path)) return -ENOENT;

    char full_path[PATH_MAX];
    get_fullPath(full_path, path);
    int res = access(full_path, mask);
    if (res == -1) return -errno;
    return 0;
}

static int lawak_open(const char *path, struct fuse_file_info *fi) {
    if (secret_access(path)) return -ENOENT;
    if ((fi->flags & O_ACCMODE) != O_RDONLY) return -EACCES; // read only

    char full_path[PATH_MAX];
    get_fullPath(full_path, path);
    int res = open(full_path, fi->flags);
    if (res == -1) return -errno;
    fi->fh = res;
    return 0;
}

static int lawak_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi) {
    int res = pread(fi->fh, buf, size, offset);
    if (res == -1) return -errno;
    return res;
}
```
- lawak_access : memeriksa izin akses sebelum operasi lain. Sama seperti `getattr,` ia menggunakan `secret_access` untuk menolak akses ke file "secret" di luar jam yang ditentukan dengan mengembalikan `-ENOENT`
- lawak_open : file akan dibuka, melakukan pengecekan secret_access. selain itu, kode ini menambahkan pengecekan untuk memastikan filesystem bersifat read-only dengan menolak mode akses selain O_RDONLY
- lawak_read : setelah itu, fungsi ini menangani pembacaan data dari file tersebut menggunakan `pread` pada file handle yang sudah ada

 ## `fuse operations` dan `main` :
 ```
 static struct fuse_operations lawak_oper = {
    .getattr = lawak_getattr,
    .readdir = lawak_readdir,
    .access  = lawak_access,
    .open    = lawak_open,
    .read    = lawak_read,
};

int main (int argc, char *argv[]) {
    source_dir = realpath(argv[1], NULL);
    if (!source_dir) {
        perror("realpath");
        return 1;
    }
    argv[1] = argv[2];
    argc--;
    int fuse_stat = fuse(argc, argv, &lawak_oper, NULL);
    free(source_dir);
    return fuse_stat;
}
```
- `struct fuse_operations` : struct ini adalah pusat kendali FUSE. Kita "mendaftarkan" fungsi-fungsi yang telah kita buat ke operasi FUSE yang sesuai. Misalnya, ketika sistem ingin melakukan `readdir`, FUSE akan memanggil fungsi `lawak_readdir` kita
- `main` : fungsi utama dari program, Tugasnya adalah =
  - Mem-parsing argumen baris perintah untuk mendapatkan direktori sumber dan mount point
  - Menggunakan `realpath` untuk mendapatkan path absolut dari direktori sumber
  - Memanggil `fuse_main` (di sini `fuse`) yang akan memulai event loop FUSE. Fungsi ini akan berjalan terus, menangani permintaan filesystem dari kernel dan meneruskannya ke fungsi-fungsi `lawak_*` kita, hingga filesystem di-unmount
