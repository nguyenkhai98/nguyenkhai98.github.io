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
