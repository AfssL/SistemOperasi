[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Eu-CByJh)
|    NRP     |      Name      |
| :--------: | :------------: |
| 5025241190 | Afsal Murtaza |
| 5025241204 | Fathiya Nayla Husna Wibowo |
| 5025241210 | Muhammad Cholaif Al Ghifari Harianto |

# Praktikum Modul 3 _(Module 3 Lab Work)_

### Laporan Resmi Praktikum Modul 3 _(Module 3 Lab Work Report)_

Di suatu pagi hari yang cerah, Budiman salah satu mahasiswa Informatika ditugaskan oleh dosennya untuk membuat suatu sistem operasi sederhana. Akan tetapi karena Budiman memiliki keterbatasan, Ia meminta tolong kepadamu untuk membantunya dalam mengerjakan tugasnya. Bantulah Budiman untuk membuat sistem operasi sederhana!

_One sunny morning, Budiman, an Informatics student, was assigned by his lecturer to create a simple operating system. However, due to Budiman's limitations, he asks for your help to assist him in completing his assignment. Help Budiman create a simple operating system!_

### Soal 1

> Sebelum membuat sistem operasi, Budiman diberitahu dosennya bahwa Ia harus melakukan beberapa tahap terlebih dahulu. Tahap-tahapan yang dimaksud adalah untuk **mempersiapkan seluruh prasyarat** dan **melakukan instalasi-instalasi** sebelum membuat sistem operasi. Lakukan seluruh tahapan prasyarat hingga [perintah ini](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul3/README-ID.md#:~:text=sudo%20apt%20install%20%2Dy%20busybox%2Dstatic) pada modul!

> _Before creating the OS, Budiman was informed by his lecturer that he must complete several steps first. The steps include **preparing all prerequisites** and **installing** before creating the OS. Complete all the prerequisite steps up to [this command](https://github.com/arsitektur-jaringan-komputer/Modul-Sisop/blob/master/Modul3/README-ID.md#:~:text=sudo%20apt%20install%20%2Dy%20busybox%2Dstatic) in the module!_

**Answer:**

- **Code:**

  `put your answer here`

- **Explanation:**

  `put your answer here`

- **Screenshot:**

  `put your answer here`

### Soal 2

> Setelah seluruh prasyarat siap, Budiman siap untuk membuat sistem operasinya. Dosen meminta untuk sistem operasi Budiman harus memiliki directory **bin, dev, proc, sys, tmp,** dan **sisop**. Lagi-lagi Budiman meminta bantuanmu. Bantulah Ia dalam membuat directory tersebut!

> _Once all prerequisites are ready, Budiman is ready to create his OS. The lecturer asks that the OS should contain the directories **bin, dev, proc, sys, tmp,** and **sisop**. Help Budiman create these directories!_

**Answer:**

- **Code:**

  ```
  sudo mkdir -p ramdiskfs/{bin,dev,proc,sys,tmp,sisop,home}
  ```

- **Explanation:**

  - Membuat suatu struktur direktori dasar untuk suatu sistem operasi yang sederhana dan berada di dalam sebuah direktori bernama `ramdiskfs`

  - Direktori `ramdiskfs` nantinya akan digunakan untuk membuat initial RAM filesystem

  `bin`    : Menyimpan binary esensial

  `dev`    : Berisi file - file devices

  `proc`   : Sebuah filesystem virtual yang menyediakan informasi proses dan kernel

  `sys`    : Sebuah filesystem virtual yang menyediakan informasi tentang perangkat dan driver

  `tmp`    : Tempat file - file sementara

  `sisop`  : Direktori sesuai permintaan

  `home`   : Direktori home untuk user


- **Screenshot:**

  - Hasil Command :
  
  ![nomor 2 (mkdirectori)](https://github.com/user-attachments/assets/0bbdb709-352c-473d-bd84-27fa01ceeaba)

***Setup tambahan (Setelah ini kode di run pada directory `./osboot/linux-6.1.1/ramdiskfs`)***
- **Code:**
  - Copy file - file device penting
  
  ```
  cd ramdiskfs
  sudo cp -a /dev/{null,tty*,zero,console} dev/
  ```
  
  - Install busybox pada initial RAM filesystem

  ```
  sudo cp /usr/bin/busybox bin
  cd bin
  sudo ./busybox --install .
  cd ..
  ```

  - Buat file init

  ```
  sudo touch init
  sudo chmod +x init
  sudo nano init
  ```

- **Explanation:**

  - Pindah ke dalam direktori `ramdiskfs` dan copy device `null, tty*, zero, console` ke dalam direktori `/dev` (merepresentasikan perangkat suatu sistem)

  - Copy `busybox` (executable yang menyediakan banyak utilitas Unix) ke `/bin` dan kemudian di Install

  - Buat file init dan jadikan executable, yang dimana scrip ini adalah proses pertama yang dijalankan kernel

  - Scrip me-mount filesystem `/proc, /sys, dan /dev (melalui devtmpfs)`, mengatur hostname sistem menjadi "sisop", 
  membersihkan layar, mencetak pesan selamat datang, dan kemudian memulai sebuah shell `/bin/sh` untuk memungkinkan interaksi dasar

- **Screenshot:**

  - Command :
  
  ![nomor 2 set up tambahan](https://github.com/user-attachments/assets/8b83b7ca-7c05-4541-b18c-b74fa0c0dd89)

  - Hasil command diatas tadi :
  
  ![nomor 2 ls bin](https://github.com/user-attachments/assets/f402131e-e136-4db1-bb4a-e3f6a3d14bde)
  ![nomor 2 ls dev](https://github.com/user-attachments/assets/2d25d539-aeb6-4d56-a9fb-2e4a119ec7a8)

  - Isi dari file init :
  
  ![nomor 2 isi file init](https://github.com/user-attachments/assets/e8a0e820-b3ff-4a8f-8666-b54acb7c5450)
  
  

### Soal 3

> Budiman lupa, Ia harus membuat sistem operasi ini dengan sistem **Multi User** sesuai permintaan Dosennya. Ia meminta kembali kepadamu untuk membantunya membuat beberapa user beserta directory tiap usernya dibawah directory `home`. Buat pula password tiap user-usernya dan aplikasikan dalam sistem operasi tersebut!

> _Budiman forgot that he needs to create a **Multi User** system as requested by the lecturer. He asks your help again to create several users and their corresponding home directories under the `home` directory. Also set each user's password and apply them in the OS!_

**Format:** `user:pass`

```
root:Iniroot
Budiman:PassBudi
guest:guest
praktikan1:praktikan1
praktikan2:praktikan2
```

**Answer:**

- **Code:**

  - Buat direktori :

   ```
   sudo mkdir -p home/{Budiman,guest,praktikan1,praktikan2}
   sudo mkdir -p etc
   ```

  - Buat file untuk menyimpan User dan Passwword nya :

   ```
   sudo nano etc/passwd
   sudo nano etc/group
   ```
  

- **Explanation:**

  - Buat direktori `/home` untuk semua user

  - Buat direktori `etc` untuk menyimpan file - file config

  - File `etc/passwd` berisi informasi akun user (nama user, password terenkripsi, UID, GID, info user, direktori home, login shell)

  - File `etc/group` berisi definisi dari grup user (nama grup, placeholder untuk passwd grup, GID, daftar user) UID (User ID) dan GID (Group ID) ditetapkan, dan user ditambahkan ke grup yang relevan
  

- **Screenshot:**

  - Hasil Command :
 
  ![nomor 3 a](https://github.com/user-attachments/assets/7b5d1c73-2a23-4904-a8cf-e6038f635595)
  ![nomor 3 b](https://github.com/user-attachments/assets/b0911d54-ed43-4ec4-9550-1963cda8f85f)

  - Isi dari file `etc/passwd` dan `etc/group` :

  ![nomor 3 c](https://github.com/user-attachments/assets/d881aa20-cf31-48a7-b7c3-580dfc445e3b)



### Soal 4

> Dosen meminta Budiman membuat sistem operasi ini memilki **superuser** layaknya sistem operasi pada umumnya. User root yang sudah kamu buat sebelumnya akan digunakan sebagai superuser dalam sistem operasi milik Budiman. Superuser yang dimaksud adalah user dengan otoritas penuh yang dapat mengakses seluruhnya. Akan tetapi user lain tidak boleh memiliki otoritas yang sama. Dengan begitu user-user selain root tidak boleh mengakses `./root`. Buatlah sehingga tiap user selain superuser tidak dapat mengakses `./root`!

> _The lecturer requests that the OS must have a **superuser** just like other operating systems. The root user created earlier will serve as the superuser in Budiman's OS. The superuser should have full authority to access everything. However, other users should not have the same authority. Therefore, users other than root should not be able to access `./root`. Implement this so that non-superuser accounts cannot access `./root`!_

**Answer:**

- **Code:**

  ```
  sudo mkdir -p root
  sudo chmod 700 root
  ```

- **Explanation:**

  - Mengunci direktori home user root `/root`, sehingga hanya dapat diakses oleh user root
 
  - `sudo mkdir -p root` membuat directori bernama root

  - `sudo chmod 700 root` mengatur hak akses untuk direktori `/root`
 
  - Angka 700 berarti :
    - User root : read, write, execute (rwx yaitu 4 + 2 + 1 = 7)
    - Grup : Tidak ada hak akses (--yaitu 0)
    - Lainnya : Tidak ada hak akses (--yaitu 0)
    - Soo, hanya user root yang memiliki akses ke direktori `/root`


- **Screenshot:**

  - Hasil Command berupa informasi :
  
  ![nomor 4](https://github.com/user-attachments/assets/e05cfe2c-b230-447c-8641-e748ac3faa51)


### Soal 5

> Setiap user rencananya akan digunakan oleh satu orang tertentu. **Privasi dan otoritas tiap user** merupakan hal penting. Oleh karena itu, Budiman ingin membuat setiap user hanya bisa mengakses dirinya sendiri dan tidak bisa mengakses user lain. Buatlah sehingga sistem operasi Budiman dalam melakukan hal tersebut!

> _Each user is intended for an individual. **Privacy and authority** for each user are important. Therefore, Budiman wants to ensure that each user can only access their own files and not those of others. Implement this in Budiman's OS!_

**Answer:**

- **Code:**

  - Ganti owner dari tiap direktori `/home` yang ada untuk setiap user :

  ```
  sudo chown 1001:2000 home/Budiman
  sudo chown 1002:2000 home/guest
  sudo chown 1003:2000 home/praktikan1
  sudo chown 1004:2000 home/praktikan2
  ```

  - Ganti hak execute untuk tiap direktori `/home` :

  ```
  sudo chmod 700 home/Budiman
  sudo chmod 700 home/guest
  sudo chmod 700 home/praktikan1
  sudo chmod 700 home/praktikan2
  ```

- **Explanation:**

  - `sudo chown <UID>:<GID> home/<user>` mengubah owner direktori home untuk setiap user
    - Contoh, `home/praktikan1` ditetapkan ke UID 1003 dan GID 2000 (sesuai dengan grup user dan grup di `/etc/passwd`)

  - `sudo chmod 700 home/<user>` mengatur hak execute untuk tiap direktori home dari user menjadi `rwx-----`
    - Sehingga seluruh execute hanya untuk owner saja dan tidak ada hak execute untuk grup atau user lainnya
    - Hal ini dapat mencegah user lain mengakses / memodifikasi isi dari direktori home user lain

- **Screenshot:**

  - Hasil Command :
    
  ![nomor 5](https://github.com/user-attachments/assets/c0a6605f-9df3-48a6-90f1-cab00932e052)


### Soal 6

> Dosen Budiman menginginkan sistem operasi yang **stylish**. Budiman memiliki ide untuk membuat sistem operasinya menjadi stylish. Ia meminta kamu untuk menambahkan tampilan sebuah banner yang ditampilkan setelah suatu user login ke dalam sistem operasi Budiman. Banner yang diinginkan Budiman adalah tulisan `"Welcome to OS'25"` dalam bentuk **ASCII Art**. Buatkanlah banner tersebut supaya Budiman senang! (Hint: gunakan text to ASCII Art Generator)

> _Budiman wants a **stylish** operating system. Budiman has an idea to make his OS stylish. He asks you to add a banner that appears after a user logs in. The banner should say `"Welcome to OS'25"` in **ASCII Art**. Use a text to ASCII Art generator to make Budiman happy!_ (Hint: use a text to ASCII Art generator)

**Answer:**

- **Code:**

  - Install `figlet` terlebih dahulu :

  ```
  sudo apt install figlet
  ```
  
  - Buat tulisan menggunakan `figlet` :
 
  ```
  sudo bash
  figlet "Welcome to OS'25" > etc/issue
  exit
  ```

- **Explanation:**

  - Membuat ASCII art dengan `figlet`
  - Output : "Welcome to OS'25" diubah jadi karakter ASCII berukuran besar, kemudian disimpan ke dalam file `etc/issue` di dalam `ramdiskfs`
  - File ini akan ditampilkan saat `/bin/getty` dirun

- **Screenshot:**

  - Hasil Command :
  
  ![nomor 6](https://github.com/user-attachments/assets/6e75739c-b936-4999-a1ad-216e075dcfd5)


### Soal 7

> Melihat perkembangan sistem operasi milik Budiman, Dosen kagum dengan adanya banner yang telah kamu buat sebelumnya. Kemudian Dosen juga menginginkan sistem operasi Budiman untuk dapat menampilkan **kata sambutan** dengan menyebut nama user yang login. Sambutan yang dimaksud berupa kalimat `"Helloo %USER"` dengan `%USER` merupakan nama user yang sedang menggunakan sistem operasi. Kalimat sambutan ini ditampilkan setelah user login dan setelah banner. Budiman kembali lagi meminta bantuanmu dalam menambahkan fitur ini.

> _Seeing the progress of Budiman's OS, the lecturer is impressed with the banner you created. The lecturer also wants the OS to display a **greeting message** that includes the name of the user who logs in. The greeting should say `"Helloo %USER"` where `%USER` is the name of the user currently using the OS. This greeting should be displayed after user login and after the banner. Budiman asks for your help again to add this feature._

**Answer:**

- **Code:**

  - Buat file untuk menyimpan scrip command :
  
  ```
  sudo nano etc/profile
  ```

  - Isi dari file `etc/profile` :
  
  ```
  #!/bin/sh
  echo "Helloo $(whoami)"
  ```

- **Explanation:**

  - Scrip `/etc/profile` dieksekusi setiap kali user login menggunakan shell yang kompatibel dengan bourne (`/bin/sh`, yang umum untuk busybox)
    - `#!/bin/sh` menentukan interpreter untuk scrip
    - `echo "Helloo $(whoami)"` mencetak pesan sambutan, `whoami` command yang mencetak nama user dari user saat ini

- **Screenshot:**

  - Hasil Command :
 
  ![nomor 7](https://github.com/user-attachments/assets/f7f53467-26f7-4277-a3c0-03d979f7eb22)


### Soal 8

> Dosen Budiman sudah tua sekali, sehingga beliau memiliki kesulitan untuk melihat tampilan terminal default. Budiman menginisiatif untuk membuat tampilan sistem operasi menjadi seperti terminal milikmu. Modifikasilah sistem operasi Budiman menjadi menggunakan tampilan terminal kalian.

> _Budiman's lecturer is quite old and has difficulty seeing the default terminal display. Budiman takes the initiative to make the OS look like your terminal. Modify Budiman's OS to use your terminal appearance!_

**Answer:**

- **Code:**

  - Ganti isi file `init` dari tty1 ke ttyS0 : `sudo nano init `
    - Isi file `init` nya jadi :

      ```
      #!/bin/sh
      mount -t proc none /proc
      mount -t sysfs none /sys

      hostname sisop
      clear
      while true
      do
          /bin/getty -L ttyS0 115200 vt100
          sleep 1
      done
      ```

  - Modifikasi file `etc/profile` dengan menambahkan export PS1 : `sudo nano etc/profile`
    - Isi file `etc/profile` nya jadi :
      ```
      #!/bin/sh
      PS1='\[\e[1;32m\]\u@\h:\[\e[1;34m\]\w\[\e[0m\]\$ '
      export PS1
      echo "Helloo $(whoami)"
      ```

  - Buat grup config untuk nomor 10 nanti sekalian :

  ```
  cd ..
  sudo nano grub.cfg
  ```

  - Isi file dari `grub.cfg` :

  ````
  set timeout=5
  set default=0

  menuentry "Budiman OS'25 (Sisop)" {
      linux /boot/bzImage quiet loglevel=3 console=ttyS0
      initrd /boot/ramdiskfs.gz
  }
  ````
   

- **Explanation:**

  - Mengubah target terminal sogin pada skrip init :
    - File `init` adalah scrip pertama yang dijalankan oleh kernel setelah proses booting (menjalankan program getty)
    - `getty` adalah program yang membuka jalur komunikasi TTY, menampilkan pesan login, dan menjalankan program login untuk mengautentikasi user
    - Mengubah command dari `getty -L tty1 ...` menjadi `getty -L ttyS0 115200 vt100`, kita mengarahkan `getty`
      untuk menyediakan layanan login melalui port serial pertama (`ttyS0`) dengan kecepatan `115200` dan emulasi terminal
      `vt100`. Sebelumnya, `tty1` merujuk pada konsol virtual pertama yang biasanya ditampilkan di layar monitor
    - Perubahan ini memungkinkan interaksi dengan OS melalui koneksi serial
    
  - Menyesuaikan tampilan prompt shell pada `/etc/profile` :
    - File `/etc/profile` adalah skrip yang dieksekusi secara otomatis setiap kali seorang user berhasil login dan sesi shell baru dimulai
    - Variabel lingkungan `PS1` (Prompt String 1) mengontrol bagaimana tampilan prompt pada command line interface (CLI) shell
    - Menambahkan `PS1='\[\e[1;32m\]\u@\h:\[\e[1;34m\]\w\[\e[0m\]\$ '` dan `export PS1` :
      - `\u` : Nama user (misalnya, root, Budiman)
      - `\h` : Nama host (hostname) sistem
      - `\w` : Direktori kerja saat ini (misalnya, `/home/Budiman`)
      - `\[\e[1;32m\]` : Mengatur warna teks menjadi hijau terang (untuk `\u@\h`)
      - `\[\e[1;34m\]` : Mengatur warna teks menjadi biru terang (untuk `\w`)
      - `\[\e[0m\]` : Mengembalikan warna teks ke default
      - `\$` : Menampilkan `$` untuk pengguna biasa atau `#` untuk pengguna root

  - Mengubah konfigurasi GRUB untuk output konsol kernel :
    - File `linuxiso/boot/grub/grub.cfg` adalah konfigurasi untuk GRUB, bootloader yang memuat kernel Linux
    - Pada baris `menuentry`, command `linux /boot/bzImage ...` adalah baris yang menentukan bagaimana kernel
      akan dimuat dan parameter apa yang akan diteruskan kepadanya
    - Dengan menambahkan `console=ttyS0` ke baris tersebut, kita mengarahkan kernel untuk mengirimkan semua
      pesan booting dan output konsol utamanya ke port serial pertama (`ttyS0`)

- **Screenshot:**

  - Hasil Command :

  ![nomor 8](https://github.com/user-attachments/assets/d7f0fafe-3761-48cb-afbe-413ace7ea14f)


### Soal 9

> Ketika mencoba sistem operasi buatanmu, Budiman tidak bisa mengubah text file menggunakan text editor. Budiman pun menyadari bahwa dalam sistem operasi yang kamu buat tidak memiliki text editor. Budimanpun menyuruhmu untuk menambahkan **binary** yang telah disiapkan sebelumnya ke dalam sistem operasinya. Buatlah sehingga sistem operasi Budiman memiliki **binary text editor** yang telah disiapkan!

> _When trying your OS, Budiman cannot edit text files using a text editor. He realizes that the OS you created does not have a text editor. Budiman asks you to add the prepared **binary** into his OS. Make sure Budiman's OS has the prepared **text editor binary**!_

**Answer:**

- **Code:**

  - Copy link file yang dari GitHub, kemudian download compile dan run text editornya :
  ```
  cd bin
  sudo git clone https://github.com/morisab/budiman-text-editor.git budiman-source
  sudo g++ -static budiman-source/main.cpp -o budiman
  sudo rm -rf budiman-source
  cd .. 
  ```

- **Explanation:**

  - Menambahkan "budiman-text-editor" ke OS kustom kita :
    - Pindah ke direktori `ramdiskfs/bin`, tempat executable disimpan
    - `sudo git clone https://github.com/morisab/budiman-text-editor.git budiman-source` adalah
      kode untuk clone text editor dari GitHub yang ditentukan ke dalam direktori budiman-source
    - `sudo g++ -static budiman-source/main.cpp -o budiman` File nya kan C++ (main.cpp), jadi di compile menggunakan g++
      - `-static` ini menautkan semua library yang diperlukan secara statis ke dalam executable. Ini berarti editor budiman
        tidak akan bergantung pada file shared library eksternal (.so) yang mungkin tidak ada di OS minimal kita
      - `-o budiman` menentukan bahwa file executable output harus bernama budiman
    - `sudo rm -rf budiman-source` direktori budiman-source yang berisi kode sumber dihapus untuk menjaga `ramdiskfs` tetap bersih
    - Executable budiman yang dihasilkan akan tersedia di `/bin` dalam OS custom

- **Screenshot:**

  - Hasil Command untuk copy file dari GitHub nya :
  
  ![nomor 9 a](https://github.com/user-attachments/assets/a1953b5e-fb08-476c-bce0-43b3199148ec)

  - Compile text editornya :
  
  ![nomor 9 b](https://github.com/user-attachments/assets/82e572b9-04f4-45d1-abc1-c3b47f6e2118)



### Soal 10

> Setelah seluruh fitur yang diminta Dosen dipenuhi dalam sistem operasi Budiman, sudah waktunya Budiman mengumpulkan tugasnya ini ke Dosen. Akan tetapi, Dosen Budiman tidak mau menerima pengumpulan selain dalam bentuk **.iso**. Untuk terakhir kalinya, Budiman meminta tolong kepadamu untuk mengubah seluruh konfigurasi sistem operasi yang telah kamu buat menjadi sebuah **file .iso**.

> After all the features requested by the lecturer have been implemented in Budiman's OS, it's time for Budiman to submit his assignment. However, Budiman's lecturer only accepts submissions in the form of **.iso** files. For the last time, Budiman asks for your help to convert the entire configuration of the OS you created into a **.iso file**.

**Answer:**

- **Code:**

  - Compress dan proses initial RAM filesystem nya :
  
  ```
  sudo find . | sudo cpio -oHnewc | sudo gzip > ../ramdiskfs.gz
  ```

  - Buat folder boot, kemudian copy `bzImage` dan `ramdiskfs` :
  
  ```
  cd ..
  mkdir -p linuxiso/boot/grub
  cp bzImage linuxiso/boot
  cp ramdiskfs.gz linuxiso/boot 
  ```

  - Buat file `grub.cfg` :
  
  ```
  nano linuxiso/boot/grub/grub.cfg 
  ```

  - Isi dari file `grub.cfg` :
  
  ```
  set timeout=5
  set default=0

  menuentry "Budiman OS'25 (Sisop)" {
      linux /boot/bzImage quiet loglevel=3 console=ttyS0
      initrd /boot/ramdiskfs.gz
  } 
  ```

  - Buat file `iso` :
  ```
  grub-mkrescue -o sisoplinux.iso linuxiso 
  ```

- **Explanation:**

  - Membuat initramfs :
    - Di dalam direktori `ramdiskfs`, perintah `sudo find . | sudo cpio -H newc -o | sudo gzip > ../ramdiskfs.gz`
      mengarsipkan semua file dan direktori di dalam `ramdiskfs` menggunakan cpio, kemudian mengompres arsip tersebut dengan gzip
    - Output `ramdiskfs.gz` adalah initial RAM filesystem
      
  - Mempersiapkan struktur ISO :
    - Sebuah direktori `linuxiso/boot/grub` dibuat (untuk media boot GRUB)
    - Kernel yang telah dikompilasi `bzImage` dan initramfs (`ramdiskfs.gz`) di copy ke dalam `linuxiso/boot/`
      
  - Konfigurasi GRUB :
    - File `grub.cfg` dibuat di `linuxiso/boot/grub/` : File ini memberitahu bootloader GRUB cara me-boot OS kita
      - `set timeout=5` : GRUB akan menunggu 5 detik sebelum melakukan auto-boot pada entri default
      - `set default=0` : Menuentry pertama adalah default
      - `menuentry "Budiman OS'25 (Sisop)"` : Mendefinisikan opsi menu boot
      - `linux /boot/bzImage quiet loglevel=3 console=ttyS0` : Menentukan image kernel yang akan dimuat (`/boot/bzImage`) dan parameter kernel
      - `quiet` mengurangi pesan boot, `loglevel=3` mengatur tingkat informasi pesan kernel
      - `initrd /boot/ramdiskfs.gz` : Menentukan initial RAM disk
    
  - Membuat ISO :
    - `grub-mkrescue -o sisoplinux.iso linuxiso` pakai grub mkrescue untuk membuat ISO bootable bernama sisoplinux.iso
      yang isi nya dari direktori linuxiso

- **Screenshot:**

  - Hasil Command compress `ramdiskfs` :
  
  ![nomor 10 a](https://github.com/user-attachments/assets/91ad3127-43ec-49d8-ba8b-c96384150ff6)

  - Setup Config grub :
  
  ![nomor 10 b](https://github.com/user-attachments/assets/9d0c0e50-609b-41ea-9bf1-02e19465dc30)

  - Hasil Command rubah file ke iso :
  
  ![nomor 10 iso file](https://github.com/user-attachments/assets/a6840203-de6b-45ce-a06b-8d03487a634d)

**Run QEMU**

- **Code:**

  - Run :
  
  ```
  qemu-system-x86_64 -smp 2 -m 256 -nographic -cdrom sisoplinux.iso -serial mon:stdio
  ```

  - Kill :
 
  ```
  pkill -f qemu
  ```

- **Explanation:**

  - smp 2     : mengatur 2 CPU
    
  - m 256     : mengatur RAM sebanyak 256 MB
    
  - nographic : menjalankan tanpa akses grafik
    
  - cdrom sisoplinux.iso  : menetapkan file ISO sebagai CD-ROM
    
  - serial mon:stdio      : mengalihkan output serial ke terminal

 
- **Screenshot:**

  - Hasil Run ISO :

  ![Tampilan Awal (1)](https://github.com/user-attachments/assets/512bb8fa-172f-403a-8cd5-47b0bd621d7c)

  ![Tampilan Awal (2)](https://github.com/user-attachments/assets/11990ac2-b954-4972-8903-231298482844)
    

---

Pada akhirnya sistem operasi Budiman yang telah kamu buat dengan susah payah dikumpulkan ke Dosen mengatasnamakan Budiman. Kamu tidak diberikan credit apapun. Budiman pun tidak memberikan kata terimakasih kepadamu. Kamupun kecewa tetapi setidaknya kamu telah belajar untuk menjadi pembuat sistem operasi sederhana yang andal. Selamat!

_At last, the OS you painstakingly created was submitted to the lecturer under Budiman's name. You received no credit. Budiman didn't even thank you. You feel disappointed, but at least you've learned to become a reliable creator of simple operating systems. Congratulations!_
