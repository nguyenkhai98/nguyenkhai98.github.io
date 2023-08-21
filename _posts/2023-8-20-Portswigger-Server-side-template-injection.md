la# [Portswigger] Server-side template injection

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3e6623ba-fc70-4e15-8d32-364d0918eb22)

***

## 1. Lab: Basic server-side template injection

* Content:
```
This lab is vulnerable to server-side template injection due to the unsafe construction of an ERB template.

To solve the lab, review the ERB documentation to find out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.
```
* Exploit:

Nghiên cứu cấu trúc của ERB template => Dùng  cú pháp: `<%= expression %>`

Khi ấn vào link 1 sản phẩm bất kỳ => Xuất hiện parameter `message`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/470745af-55b0-4f48-9f94-51bebcd97e22)

=> Thử fill cú pháp của ERB template: `message=%3C%=%207*7%20%%3E` (`message=<%= 7*7 %>`)

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/affb0a8c-e4af-44b3-ac9e-857edf87ce4d)

Dùng lệnh `system()` để truyền command:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f427c46c-028e-4a62-b101-753bb6175fe0)

Thực hiện xóa file theo yêu cầu của lab:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e24b737b-0e2f-4931-b3e8-6c46e5bb1cc7)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0c88bb34-9130-463c-aac1-822d3bba4ea8)

***

## 2. Lab: Basic server-side template injection (code context)

* Content:
```
This lab is vulnerable to server-side template injection due to the way it unsafely uses a Tornado template. To solve the lab, review the Tornado documentation to discover how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Nghiên cứu `Tornado template` => Sử dụng expression `{{ }}`

Tập trung vào tính năng `Preferred name`:

<img width="405" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/15203fc3-311a-4b63-979e-5389a5722ffd">

Thay đổi giá trị submit lên là `user.name}}{{7*7}}`:

<img width="451" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3b7fb10b-e4f4-4d79-bd79-35dd16d419d9">

Thực hiện post 1 comment lên và quan sát tên hiển thị => Thấy giá trị `49` xuất hiện, chứng tỏ vị trí `Preferred name` bị dính lỗi SSTI:

<img width="374" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9ee75eb4-c649-4c6c-a8f8-29449b058fae">

`Tornado template` sử dụng cú pháp sau để chạy lệnh OS => Áp dụng xóa file LAB yêu cầu:

<img width="167" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/189bd6e5-d9b8-4d41-abca-87c7f8cb9236">

LAB SOLVED!

<img width="593" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/310cf722-87fa-4eba-8da3-2ee23d43fe6f">

***

## 3. Lab: Server-side template injection using documentation

* Content:
```
This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and use the documentation to work out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials:

content-manager:C0nt3ntM4n4g3r
```
* Exploit:

Thực hiện edit mô tả một sản phẩm bất kỳ, thấy nội dung có chứa đoạn code template: `${product.stock} left of ${product.name} at ${product.price}.`

<img width="384" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e575e75a-e975-4899-a90c-7a32e7588c54">

Thử nhập vào các payload nhằm trigger thông tin lỗi như: `${}`, `${7/0}`, `${foobar}`, `${7*7}`

Thấy thông tin loại template sử dụng => `freemarker`

<img width="600" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/466f1726-a520-4123-a559-89b09c21cb6c">

