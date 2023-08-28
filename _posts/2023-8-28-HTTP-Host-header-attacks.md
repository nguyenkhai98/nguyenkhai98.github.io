# [Portswigger] HTTP Host header attacks

***

## 1. Lab: Basic password reset poisoning

* Content:
```
This lab is vulnerable to password reset poisoning. The user carlos will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account.

You can log in to your own account using the following credentials: wiener:peter. Any emails sent to this account can be read via the email client on the exploit server.
```
* Exploit:

Thực hiện thử nghiệm tính năng forgot-password => Xác nhận đã nhận được email báo link reset: 

<img width="607" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/549e998e-516b-4563-9254-dd43479467f7">

Theo dõi trên burp history => Phát hiện ra request `POST /forgot-password` sau:

<img width="622" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e5452b39-f057-41f9-8508-9505d3a4dc46">

Chuyển request này sang Burp Repeater => Thử thay đổi giá trị `Host` thì thấy là Request vẫn được xử lý bình thường:

<img width="626" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/efe595a2-d39f-476d-9568-725e74d0e16b">

Đồng thời Email Client nhận được link reset password theo Host fake:

<img width="604" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9837ba05-bd86-497a-9243-6bcb45f70c68">

=> Thử fake username thành `carlos` và `Host` header về địa chỉ của Exploit Server:

<img width="626" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/893affae-070c-44c6-bde0-4d95da97d71f">

=> View web access trên Exploit Server => Phát hiện request sau:

<img width="630" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/13b4f24b-f845-42d0-8bed-28ce5ec19a88">

Truy cập link trên với Host chuẩn => Thực hiện đổi pass => Đã thành công đổi pass của user `carlos`. 
Sau đó sử dụng password mới đổi này để login với user `carlos`. => LAB SOLVED!

<img width="585" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/34429f22-c562-49ff-96c5-94dd35150517">





