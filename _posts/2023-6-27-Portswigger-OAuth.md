# [Portswigger] OAuth 2.0 authentication vulnerabilities

***

## 1. Lab: Authentication bypass via OAuth implicit flow

* Content:
```
This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is carlos@carlos-montoya.net.

You can log in with your own social media account using the following credentials: wiener:peter.
```
* Giao diện Web:

Trang web dạng tin tức đơn thuần, có phần **My Account** trỏ đến link đăng nhập

<img width="334" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1da9e6ac-468f-4507-876d-986d2dfb9b2c">

* Exploit:

Bật BurpSuite và thực hiện quá trình login với thông tin được đề bài cung cấp `wiener:peter`.
Theo dõi flow trên Burp HTTP History, ta thấy các request xác thực OAuth như sau:

<img width="634" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/da08f540-46ab-4cde-a483-6af709a23e63">

<img width="636" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6818c186-38e7-43d1-ae20-3831beeb5918">

<img width="635" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4201ce5f-fede-47b9-b78b-b14cea717ff9">

OAuth Services gửi lại thông tin về user mà Client Application muốn lấy

<img width="629" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0e6d0f69-c7fa-4dac-8bc0-47f785e7392c">

Sau khi nhận được thông tin, Client Application sẽ gửi gói tin POST đến `/authenticate`, dùng các thông tin nhận được từ OAuth Services (bao gồm cả thông tin Token)

<img width="638" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/24b6127f-44da-4f4f-8f94-15be056c336a">

=> Chuyển request này sang Repeater, thay thế email truyền lên thành `carlos@carlos-montoya.net`. Sau đó chuột phải vào phần Response => Chọn "Request In Browser" => "In Original Session"

<img width="633" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5c52cb1d-bd03-4345-8df5-2319af0f1e81">

Copy request được Burp Generate ra dán vào trình duyệt, ta thấy đã log in được vào bằng user **Carlos** và LAB SOLVED!

<img width="605" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5a8b88b5-74e0-4954-be66-01c08701e827">





