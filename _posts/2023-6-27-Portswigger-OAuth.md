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

***
## 2. Lab: Forced OAuth profile linking

* Content:
```
This lab gives you the option to attach a social media profile to your account so that you can log in via OAuth instead of using the normal username and password. Due to the insecure implementation of the OAuth flow by the client application, an attacker can manipulate this functionality to obtain access to other users' accounts.

To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete carlos.

The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

You can log in to your own accounts using the following credentials:

Blog website account: wiener:peter
Social media profile: peter.wiener:hotdog
```
* Exploit:

Giao diện login cho chúng ta 2 options: (Login bằng account local hoặc social media account)

<img width="369" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4d9e12df-f09a-4603-85b8-fb780702bacc">

- Khi thực hiện login bằng account local thì sau khi login thành công sẽ có thêm 1 tùy chọn là "Attach a social profile" => Tức là gắn account local hiện tại với một tài khoản social media. Burpsuite bắt được 2 requests bao gồm Request đến OAuth để xác minh Social Media Account và Request đến `/oauth-linking?code=k3pEgFyxRLJQBs9GGo30g8K4xlKpPd4dtDMjyBuB763` (Nội dung đoạn code kia là ngẫu nhiên, dự đoán đoạn code trên tương ứng với tài khoản local mà đang request attach)

<img width="461" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/62fbe2f0-71b5-4d36-bf86-8ed97efb8632">

- Khi thực hiện login bằng social media account thì Client Application sẽ thực hiện request đến OAuth Service Provider

<img width="460" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d6251c61-8710-44b7-8bf9-7846faa74386">

Quan sát thấy nội dung Request OAuth không có parametter **state** (Biến này có vai trò như CSRF Token trong việc xác minh các request giữa Client Aplication và OAuth Services Provider) => Có thể bị hacker lợi dụng.

Thực hiện kịch bản tấn công bằng cách sau:
1. Call đến tính năng "Attach a social profile", bật chế độ Burp Intercept On, copy lại URL gọi đến `/oauth-linking?code=xxx` (bao gồm cả mã code) => Sau đó drop gói tin này.
2. Logout khỏi tài khoản hiện tại.
3. Chuẩn bị 1 form CSRF trên Exploit Server với nội dung như bên dưới rồi Deliver to Victim:
```<iframe src="https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?code=STOLEN-CODE"></iframe>```
4. Đợi 1 thời gian và truy cập lại ứng dụng, chọn Login with Social Media Account.
5. Sau khi truy cập bằng Social Media thì thấy tài khoản Social của chúng ta đã được gắn với Account victim (Administrator). Thực hiện xóa Carlos => Lab Solved!

<img width="619" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/25aa6234-25b6-4ce4-ba37-ffaefcc3edce">



