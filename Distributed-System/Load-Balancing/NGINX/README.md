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






