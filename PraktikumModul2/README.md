[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/9LcL5VTQ)
|    NRP     |      Name      |
| :--------: | :------------: |
| 5025241190 | Afsal Murtaza |
| 5025241204 | Fathiya Nayla Husna Wibowo |
| 5025241210 | Muhammad Cholaif Al Ghifari Harianto |

# Praktikum Modul 2 _(Module 2 Lab Work)_

</div>

## Daftar Soal _(Task List)_

- [Task 1 - Trabowo & Peddy Movie Night](/task-1/)

- [Task 2 - Organize and Analyze Anthony's Favorite Films](/task-2/)

- [Task 3 - Cella’s Manhwa](/task-3/)

- [Task 4 - Pipip's Load Balancer](/task-4/)

## Laporan Resmi Praktikum Task 3 _(Task 3 Cella's Manhwa)_

Pertama kita Kunjungi situs MyAnimeList dan cari manhwa yang diinginkan, daftar Manhwa :
1. Mistaken as the Monster Duke's Wife  = ID : 168827
2. The Villainess Lives Again           = ID : 147205
3. No, I Only Charmed the Princess!     = ID : 169731
4. Darling, Why Can't We Divorce?       = ID : 175521

Sehingga endpoint API akan menjadi seperti berikut: https://api.jikan.moe/v4/manga/ (ID Manhwa)

### A. Summoning the Manhwa Stats
Search dulu data Manhwa dengan endpoint API Jikan sesuai ID Manhwa yang ingin dicari, ambil data Judul, Status, 
Tanggal rilis, Genre, Tema, dan Author.

Setelah berhasl, data disimpan ke file txt, dengan nama file disesuaikan dengan judul versi bahasa Inggris (tanpa karakter khusus dan spasi diganti dengan underscore). Semua file txt disimpan dalam folder Manhwa.

#### Buat Folder dulu : Manhwa, Archive, Heroines
```
#include <stdio.h>
#include <stdlib.h>

int main () {
    system("mkdir Manhwa");
    system("mkdir Archive");
    system("mkdir Heroines");
    return 0;
}
```

#### Koding dalam bahasa C :
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

#define MAX_BUFFER 3000

void create(const char *filename, const char *data) {
    FILE *file = fopen(filename, "w");
    if (file == NULL) {
        perror ("error");
        exit(EXIT_FAILURE);
    }
    fprintf (file, "%s", data);
    fclose(file);
}
```
#### Fungsi untuk nama txt file sesuai ketentuan :
```
void namaFile(const char *title, char *format) {
    int j = 0;
    for (int i = 0; title[i] != '\0'; i++) {
        if (title[i] == ' ') format[j++] = '_';
        else if ((title[i] >= 'A' && title[i] <= 'Z') || (title[i] >= 'a' && title[i] <= 'z') || 
                 (title[i] >= '0' && title[i] <= '9') || title[i] == '_') {
            format[j++] = title[i];
        }
    }
    format[j] = '\0';
}
```
#### Fungsi untuk nama zip file sesuai ketentuan :
```
void namaZip(const char *txt_filename, char *zip_filename) {
    int j = 0;
    for (int i = 1; txt_filename[i] != '\0'; i++) {
        if (txt_filename[i] >= 'A' && txt_filename[i] <= 'Z') {
            zip_filename[j++] = txt_filename[i];
        }
    }
    zip_filename[j] = '\0';
    strcat(zip_filename, ".zip");
}
```
#### Fungsi untuk zip file :
```
void zip(const char *filename) {
    char zip_command[MAX_BUFFER], zip_filename[MAX_BUFFER];
    namaZip(filename, zip_filename);
    snprintf (zip_command, sizeof(zip_command), "zip -j Archive/%s %s", zip_filename, filename);

    pid_t pid = fork();
    if (pid == 0) {
        execlp("sh", "sh", "-c", zip_command, (char *)NULL);
        perror ("execlp fail");
        exit(EXIT_FAILURE);
    } else if (pid > 0) {
        int status;
        waitpid(pid, &status, 0);
    } else {
        perror ("fork fail");
        exit(EXIT_FAILURE);
    }
}
```
#### Fungsi main nya, aku disini pakai input ID Manhwa secara manual :
```
int main () {
    char manhwa_id[10];
    const char *api_url = "https://api.jikan.moe/v4/manga/";
    char command[MAX_BUFFER], filename[MAX_BUFFER], data[MAX_BUFFER];

    printf ("Enter Manhwa ID: ");
    scanf ("%s", manhwa_id);

    pid_t pid = fork();
    if (pid == 0) {
        snprintf (command, sizeof(command), "curl -s %s%s", api_url, manhwa_id);

        FILE *fp = popen(command, "r");
        if (fp == NULL) {
            perror ("popen fail");
            exit(EXIT_FAILURE);
        }
        char jsonOutput[MAX_BUFFER];
        fread(jsonOutput, sizeof(char), MAX_BUFFER, fp);
        pclose(fp);

        char title[256], status[256], release[256], genres[256], authors[256], themes[256];
        char *title_ptr = strstr(jsonOutput, "\"title\":\"");
        char *status_ptr = strstr(jsonOutput, "\"status\":\"");
        char *published_ptr = strstr(jsonOutput, "\"published\":{");
        char *genres_ptr = strstr(jsonOutput, "\"genres\":[");
        char *authors_ptr = strstr(jsonOutput, "\"authors\":[");
        char *themes_ptr = strstr(jsonOutput, "\"themes\":[");
```
#### Bagian scrapping data di API Jikan pakai json :
```
        // extract judul
        if (title_ptr) sscanf (title_ptr, "\"title\":\"%[^\"]\"", title);
        else {
            fprintf (stderr, "Title not found\n");
            exit(EXIT_FAILURE);
        }
        // extract status
        if (status_ptr) sscanf (status_ptr, "\"status\":\"%[^\"]\"", status);
        else {
            fprintf (stderr, "Status not found\n");
            exit(EXIT_FAILURE);
        }
        // extract rilis kapan
        if (published_ptr) {
            sscanf (published_ptr, "\"published\":{\"from\":\"%[^\"]\"", release);
            char *date_end = strchr(release, 'T');
            if (date_end) *date_end = '\0';
        } else {
            fprintf (stderr, "Published not found\n");
            exit(EXIT_FAILURE);
        }

        // extract genre
        if (genres_ptr) {
            char *genre_ = genres_ptr;
            int index = 0;
            while (sscanf(genre_, "\"name\":\"%[^\"]\"", genres) == 1) {
                genre_ = strchr(genre_, '}') + 1;
                index++;
            }
        } else {
            strcpy(genres, "N/A");
        }

        // extract penulis
        if (authors_ptr) {
            char *author_ = authors_ptr;
            int index = 0;
            while (sscanf(author_, "\"name\":\"%[^\"]\"", authors) == 1) {
                author_ = strchr(author_, '}') + 1;
                index++;
            }
        } else {
            strcpy(authors, "N/A");
        }

        // extract tema
        if (themes_ptr) {
            char *theme_ = themes_ptr;
            int index = 0;
            while (sscanf(theme_, "\"name\":\"%[^\"]\"", themes) == 1) {
                theme_ = strchr(theme_, '}') + 1;
                index++;
            }
        } else {
            strcpy(themes, "N/A");
        }
```
#### Bagian cetak output di txt nya :
```
        snprintf (data, sizeof(data),
                 "Title: %s\n"
                 "Status: %s\n"
                 "Release: %s\n"
                 "Genre: %s\n"
                 "Theme: %s\n"
                 "Author: %s\n",
                 title, status, release, genres, themes, authors);

        char format[256];
        namaFile(title, format);
        snprintf (filename, sizeof(filename), "Manhwa/%s.txt", format);
        create(filename, data);
        printf ("data '%s' saved\n", title);

        zip(filename);
        exit(EXIT_SUCCESS);
    } else if (pid > 0) {
        int status;
        waitpid(pid, &status, 0);
    } else {
        perror ("fork fail");
        exit(EXIT_FAILURE);
    }
    return 0;
}
```

#### Hasil nya :
- Setelah run kode untuk membuat Folder :
![folder](https://github.com/user-attachments/assets/ed6e418d-ea21-4fba-9dac-3fe53a17f237)
- Setelah run kode untuk masing-masing Manhwa, dan data diambil pakai ID Manhwa :
![run + txt](https://github.com/user-attachments/assets/0def0c61-5c2d-451d-a7ca-8e956fb1798b)
![txt](https://github.com/user-attachments/assets/80608ba7-4e9f-4935-ad1c-36ca88647aad)
- Isi dari tiap File txt yang ada :

![txt mistaken](https://github.com/user-attachments/assets/e6e9ee3f-f234-4aa2-8fe7-678b7f2959cb)
![txt the villain](https://github.com/user-attachments/assets/c4ffff1a-f526-4c74-b6f8-c1261242f43a)
![txt no i](https://github.com/user-attachments/assets/cea8d7be-fe41-448a-85d6-d2e501e71e54)
![txt darling](https://github.com/user-attachments/assets/66ab8954-44ea-464e-bfd6-6cc7b86648f0)

#### Kendala :
- Belum berhasil mencetak data terkait Genre, Theme, dan Author

### B. Seal the Scrolls
Lanjut dari soal yang A, kita mau membuat Zip File dengan ketentuan nama file diambil dari Huruf kapital pada
judul Manhwa yang dipilih.

#### Bagian kode dalam bahasa C :
#### Fungsi untuk nama zip file sesuai ketentuan :
```
void namaZip(const char *txt_filename, char *zip_filename) {
    int j = 0;
    for (int i = 1; txt_filename[i] != '\0'; i++) {
        if (txt_filename[i] >= 'A' && txt_filename[i] <= 'Z') {
            zip_filename[j++] = txt_filename[i];
        }
    }
    zip_filename[j] = '\0';
    strcat(zip_filename, ".zip");
}
```
#### Fungsi untuk zip file :
```
void zip(const char *filename) {
    char zip_command[MAX_BUFFER], zip_filename[MAX_BUFFER];
    namaZip(filename, zip_filename);
    snprintf (zip_command, sizeof(zip_command), "zip -j Archive/%s %s", zip_filename, filename);

    pid_t pid = fork();
    if (pid == 0) {
        execlp("sh", "sh", "-c", zip_command, (char *)NULL);
        perror ("execlp fail");
        exit(EXIT_FAILURE);
    } else if (pid > 0) {
        int status;
        waitpid(pid, &status, 0);
    } else {
        perror ("fork fail");
        exit(EXIT_FAILURE);
    }
}
```
#### Memanggil Fungsi Zip di Int Main :
```
zip(filename);
exit(EXIT_SUCCESS);
```

#### Hasil nya :
- Setelah run kode untuk membuat Zip File :
![run + zip](https://github.com/user-attachments/assets/bea7b71b-8527-45ab-b791-85e017b418f2)
![zip](https://github.com/user-attachments/assets/a911007d-c717-4ae7-9c5a-8b53a13d2d23)
- Isi dari tiap Zip File yang ada :

![zip mistaken](https://github.com/user-attachments/assets/3d68da46-0800-4fc7-8760-c71cfbc1f3fb)
![zip the villain](https://github.com/user-attachments/assets/e2a30779-e824-4e5f-b180-d077918991af)
![zip no i](https://github.com/user-attachments/assets/bb6bafae-8bc0-403c-a99e-57b5e5b47c3e)
![zip darling](https://github.com/user-attachments/assets/d5fb7a81-43b9-4415-bc08-cbf60b5b97e4)

### C. Making the Waifu Gallery
#### Belum dikerjakan, dikarenakan masih menyempurnakan soal A

### D. Zip. Save. Goodbye
#### Belum dikerjakan, dikarenakan masih menyempurnakan soal A
