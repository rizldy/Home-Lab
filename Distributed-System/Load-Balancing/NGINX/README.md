# Melakukan load balancing dengan memanfaatkan NGINX

Hal pertama yang perlu dilakukan ialah menyiapkan lingkungan server yang akan ditetapkan load balancing menggunakan NGINX. Pada proses kali ini kami menggunakan server yang disiapkan oleh prodi kami dengan memenafaatkan PROXMOX VE sebagai enviroment dalam pemanfaatan resource server. Lingkungan yang digunakan dianratanya ialah 3 buah server, 1 server sebagai load balencer dan 2 sebagai server backend:
- 10.2.23.47 (Backend server 1).
- 10.2.23.48 (Backend Server 2).
- 10.2.23.49 (NGINX Load Balancer).

## Langkah Kerja
1. Melakukan instalasi bare metal atau bawaan kernel os linux
  - Proses awal ialah melakukan instalasi layanan NGINX ke dalam sistem server yang bertindak sebagai load balancer

    <img width="501" height="20" alt="image" src="https://github.com/user-attachments/assets/d309b1ca-5f18-41d2-9496-8fab57578192" />

    ```
    sudo apt install nginx
    ```

  - Semisal pada saat rose instalasi mengalai corrupt atau error di tengah proses instalasi, maka lakukan pembacaan errornya terlebih dahulu sebelum menindak lanjuti proses istalasi kembali. Dalan kasus ini ialah adanya bentrok anatara layanan open vSwitch milik mininet yang merupakan bekas pada praktikum sebelumnya, sehingga perlu dibersihkan dan dihapus sebeb tidak digunakan lagi.

  <img width="937" height="237" alt="image" src="https://github.com/user-attachments/assets/ed06a02f-97c9-4ddc-8098-c2bd22e89d2d" />


  <img width="720" height="25" alt="image" src="https://github.com/user-attachments/assets/1fb01961-68dc-4c90-a336-db5f0a74bf45" />

  - Setelah mengatasi errornya, dilanjutkan untuk melakukan instalasi yang sebelunya itu corrup, cukup menjalankan perintah berikut:

      <img width="530" height="25" alt="image" src="https://github.com/user-attachments/assets/e611c8f9-343d-4e2c-aa3c-82e24b6afef1" />

    ```
    sudo apt-get install -f
    ```

- Setelah proses instalasi selesai, dilanjukat verivikasi versi nginx yang telah terinstal

  <img width="446" height="50" alt="image" src="https://github.com/user-attachments/assets/a2aaafa4-a0ec-47b4-bb0c-d637fe706b1f" />

  ```
  nginx -v
  ```

 - Dilanjutkan dengan melakukan enable terhadap layanan NGINX agar setiap kali server mengalai restart maka layanan akan langsung otomatis berjalana kembali
  
    <img width="926" height="70" alt="image" src="https://github.com/user-attachments/assets/1448a00e-1f8d-4785-b30f-580abc1afd64" />

    ```
    sudo systemctl enable nginx
    ```

  catatan:
  perintah diatas digunakan ketika berada di user biasa, jika mengguanakn super user (root) tidak perlu mengunakan kata "sudo".

- Selanjutnya melakuakn start untuk layanan NGINX

    ```
    sudo systemctl start nginx
    ```

- Jika pada saat poses start terjadi error atau gagal, maka perlu membaca log pada saat proses start dilakukan memalui perintah:

    <img width="935" height="122" alt="image" src="https://github.com/user-attachments/assets/21b3edcb-2162-4500-ab24-04d2a2016576" />

    ```
    sudo systemctl status nginx.service
    ```

    <img width="1426" height="440" alt="image" src="https://github.com/user-attachments/assets/4b11485f-3312-4fdb-89b6-041f86b69f54" />


Terlihat errornya bahwa port 80 yang secara defaultnya unutk layanan NGINX berjalan tidak tersedia, maka perlu dilakukan tindakan lanjutan yaitu melakukan pengecekan layanan apa yang sedang berjalan menggunakan port 80. Ketikkan perintah berikut untuk melakukan pengecekan:

    sudo ss -tulpn | grep :80

  <img width="982" height="41" alt="image" src="https://github.com/user-attachments/assets/c95e9dcd-dd59-4c49-af01-8a9ba8ad6d1f" />

Layanan yang sedang berjalan ialah apache2 sehingga port 80 telah terisi. Terdapat 2 keputusan yang dapat diambil, ayitu meninaktifkan layanan apache2 apabila tidak memiliki tugas yang penting atau mengalokasikan layanan nginx ke port lain semisal port 81 agar layanan tidak bentrok. Pada kasus kali ini dipuskan untuk mematikan layanan apache dikarenakan tidak menjalankan proses yang penting.

  <img width="667" height="22" alt="image" src="https://github.com/user-attachments/assets/6a04c650-b458-4434-9e8c-25412b02366c" />


  <img width="930" height="120" alt="Screenshot 2026-10-23 152645" src="https://github.com/user-attachments/assets/15a7450f-8e31-4442-b103-d3ee54ed14b4" />



    sudo systemctl stop apache2.service
    sudo systemctl disable apache2.service

- Melakukan start kembali ke layanan NGINX
    <img width="910" height="283" alt="image" src="https://github.com/user-attachments/assets/8d3ad2e2-6c1c-4144-881e-3c96073930b1" />

- Melanjutkan konfigurasi proxy sebagai fitur load balancer pada server dengan mengakser direktori /etc/nginx/sites-available/ untuk melakukan konfigurasi pada sebuah file config bernama default yang telah disiapkan oleh NGINX ketika terinstal

  <img width="573" height="52" alt="image" src="https://github.com/user-attachments/assets/d26afaf2-1f9b-4d97-9f9c-ffc04c659d58" />

Setelah berhasil masuk ke direktori, lanjutkan dengan menuliskan beberapa perntah ke dalam file default dengan perintah nano.

  ```
  sudo nano default
  ```
Tambagkan beberapa perintah diantaranya menambahakn variable upstream backend sebagai media proxy load balencer yang akan mengarahkan ke 2 node server. Masukkan indikator berikut ke dalam file default tepat diluar variabel server{}:

  <img width="570" height="292" alt="image" src="https://github.com/user-attachments/assets/27c55938-f829-42fe-8cf6-5705930e13cd" />


  ```
  upstream backend {
        server 10.2.23.46;
        server 10.2.23.48;
}
  ```
Catatan: ip addres diatas disesuaikan dengan ip address milik node - node server yang menjadi backend server.

Kemudiian tambahkan value  proxy_pass http:backend didalam variabel location di dalam file default sebagai pintu akses yang akan mengarahkan ke tiap node yang telah didaftarkan di variabel upstream backend

  <img width="815" height="200" alt="image" src="https://github.com/user-attachments/assets/c7c11215-a10c-4735-8243-6008c1d260c4" />



  ```
   proxy_pass http://backend;
  ```

- Melakukan restart layanan NGINX agar melakukan load resorce yang barusan telah ditambahakan di dile default melalui direktori /etc/nginx/sites-available

```
sudo systemctl restart nginx.service
```
 Apabila pada saat proses restart gagal, maka dapat dipastikan dalam file default tertadapat typo ataupun kesalana dalam penulisan, sehingga dapat membatalkan proses restart. Oleh krena itu perlu diperhatikan dengan teliti ketika menuliskan konfogurasi dalam file.

- Melakukan instalasi layanan NGINX ke 2 node server backend

  <img width="610" height="112" alt="image" src="https://github.com/user-attachments/assets/32d80e07-f546-4e75-aeb9-27d3cca27adb" />


  <img width="533" height="92" alt="image" src="https://github.com/user-attachments/assets/4b232e24-1049-4031-8a97-38bb71942226" />

- Menganses direktori /var/www/html/ untuk mengaudit file html agar membedakan anatara sisi node 1 dan node 2 server backend ketika load balencer berjalan. Proses ini sebagai media bantu untuk validasi dalam proses load balencer mengguanakn 2 node server.

  <img width="420" height="43" alt="image" src="https://github.com/user-attachments/assets/4a23981d-ef34-46ce-8448-62e85cc6d1e7" />

  <img width="946" height="592" alt="image" src="https://github.com/user-attachments/assets/c7bb8846-1dc6-4a60-bd3e-1099512a1135" />




  <img width="527" height="47" alt="image" src="https://github.com/user-attachments/assets/ebadd1ba-4dd3-45cf-a5ff-21e91bddfba2" />

  <img width="929" height="595" alt="image" src="https://github.com/user-attachments/assets/c1aad098-04dc-4a24-85eb-07482d64303b" />


  ```
  sudo nano index.nginx-debian.html
  ```

- Melakukan percobaan dengan melakukan akses via web browser milik pihak client, dalam hal ini adalah sebuah unit laptop os windows.

<img width="1600" height="600" alt="image" src="https://github.com/user-attachments/assets/2fdb7f7c-ea11-480c-8d27-dd9c3cb2323e" />

<img width="1616" height="607" alt="image" src="https://github.com/user-attachments/assets/a3a47bf8-40ae-4734-8847-5f6ac81cbe85" />

Proses laod balancing berhasil dilakukan, terlihat setiap ada client yang mengakses, maka traffic akan di bebankan secara bergantian ke kedua node, sehingga pembagian beban kerja yang selaras dengan konsep load balancing itu terpenuhi.

Perlu diperhatikan bahwa proses akses - mengakses antara server dan client hanya dapat dialakuakn ketika kedua unit ini berada dalam satu segment jaringan yang sama. Pada kasus kali ini menggunakan server yang direct langsung ke router milik kampus yang meneyediakan gateway ke internet, maka client pun perlu berada dan menggunakan jaringan yang sama. Adapun alternatif lain yaitu menggunakan VPN seagai media penguhubung.



















