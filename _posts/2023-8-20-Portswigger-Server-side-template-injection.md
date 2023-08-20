# [Portswigger] Server-side template injection

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


