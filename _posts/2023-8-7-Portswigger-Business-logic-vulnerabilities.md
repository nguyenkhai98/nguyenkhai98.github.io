# [Portswigger] Business logic vulnerabilities

***
## 1. Lab: Excessive trust in client-side controls

* Content:
```
This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Đăng nhập vào bằng tài khoản: wiener:peter

Thực hiện add sản phẩm `Lightweight l33t leather jacket` vào giỏ hàng, bắt trên burpsuite và chỉnh giá về còn nhỏ hơn 100$

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e14d66b3-85e6-492d-a9e0-c1b49a36d348)

Tiến hành thanh toán, mua sản phẩm => LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/431a0997-6492-4392-8085-21d83071bb87)
