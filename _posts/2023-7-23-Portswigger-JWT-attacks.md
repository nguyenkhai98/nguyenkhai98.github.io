# [Portswigger] JWT attacks

***

## 1. Lab: JWT authentication bypass via unverified signature

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.
```
* Exploit:

Bắt request qua BurpSuite và chuyển request đến `/admin` qua Repeater, thực hiện thay đổi giá trị tên **wiener** => **administrastor**
![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bea32242-5232-41fc-8ac6-765da1d6a36c)

Đã truy cập được link `/admin`, tiến hành gửi request xóa user carlos
![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d28ce9cc-0dfd-46d4-94bc-7fda9b2d27eb)

Lab Solved!
![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1cd0bbf8-385f-4ac3-8780-d678d72ce220)

***

## 2. Lab: JWT authentication bypass via flawed signature verification

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.
```
* Exploit:

Thực hiện tương tự Lab trước đó, đổi `alg` về `none` và `sub` thành `administrator`
![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e29c6dbb-44fa-4d2e-a0d8-d515adc9e45a)

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/599ce680-d88e-4608-989b-e8dfd6763fc7)

Sau khi truy cập đc link `admin`, thực hiện xóa user carlos
![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f896858c-1397-4993-bb63-fff622b2221e)

Lab Solved!
![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1c777346-8c65-49c9-b7c7-935dbf9d3bb3)

***

## 3. Lab: JWT authentication bypass via weak signing key

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. It uses an extremely weak secret key to both sign and verify tokens. This can be easily brute-forced using a wordlist of common secrets.

To solve the lab, first brute-force the website's secret key. Once you've obtained this, use it to sign a modified session token that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.

We also recommend using hashcat to brute-force the secret key. For details on how to do this, see Brute forcing secret keys using hashcat.
```
* Exploit:

Trong lab này, chúng ta sử dụng thêm các Extension sau để hỗ trợ tốt hơn khi thao tác với JWT: 
<img width="355" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/79bc0ed2-3865-4a52-83b4-653a78ab47ff">

Thực hiện crack jwt token để thu được key (dùng jwt list mà portswigger cung cấp) => Key thu được value là `secret1`
<img width="484" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9295e10d-2b54-442b-ac0c-e0fda94b0391">

Tạo key mới với giá trị base64 của `secret1` trong `JWT Editor Keys`
<img width="638" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/45c1b0a8-9641-466f-85e4-38ea224abbcd">

Dùng key `secret1` để sign lại value của payload (thay đổi sub từ `wiener` thành `administrator`

Truy cập vào link `/admin` với token vừa sign

<img width="495" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/57e006e7-c86c-4814-9d27-910e8d251ff0">

Thực hiện xóa user Carlos

<img width="494" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5e9b3520-2209-4083-ad49-5fa11b7a1c78">

Lab Solved!

<img width="588" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e6ea0d3d-183e-41d4-bde1-e81fe093007c">
