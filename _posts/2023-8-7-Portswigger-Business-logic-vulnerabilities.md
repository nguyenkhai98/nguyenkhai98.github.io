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

***
## 2. Lab: 2FA broken logic

* Content:
```
This lab's two-factor authentication is vulnerable due to its flawed logic. To solve the lab, access Carlos's account page.

Your credentials: wiener:peter
Victim's username: carlos
You also have access to the email server to receive your 2FA verification code.
```
* Exploit:

Theo dõi quá trình login qua burpsuite, phát hiện request verify mã PIN (2FA) có thể bị lợi dụng để login thẳng vào user carlos mà không cần biết mật khẩu.

Chuyển gói tin request `/login2` sang Intruder và chuyển giá trị verify sang `carlos` như sau:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ca1716f4-024f-4710-a2b1-4e14da2e18ab)

Setup payload như sau:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d548a0a3-584c-4918-b0d7-bb9e9b86891b)

Start attack, xác nhận đã brute force đc mã 2FA đúng (trả về Response 302)

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/579b73c7-b71b-43a6-9dc2-5e41172db26f)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d1561df2-9828-4604-84d1-f75371712dc5)

***
## 3. Lab: High-level logic vulnerability

* Content:
```
This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:
Thực hiện ATC với Quanlity là giá trị âm

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a654d61d-43cb-41db-bd69-fa75ba787071)

Giao hiện giỏ hàng hiển thị: 

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d1d03c55-9515-45dc-8e63-05605fd30c1e)

Thực hiện place order thì bị báo lỗi tổng giá trị tiền không được nhỏ hơn 0

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/27e748f7-9fb4-4db9-8636-39ef14af7516)

=> Mua thêm các loại hàng khác cho giá trị trong giỏ hàng nằm trong khoảng 0 -> 100$

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6f7960a4-a9ce-48f9-9c9b-6a8f6a4c8396)

Do đầu bải yêu cầu phải mua đc sản phẩm id 1, nên ta thay đổi 1 chút như sau => LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d9cb1d43-617d-4dac-8e1f-a5e156488b7f)

***
## 4. Lab: Low-level logic flaw

* Content:
```
This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Quan sát hiện tượng khi add to card số lượng sản phẩm với giá trị price tổng của giỏ hàng đạt ngưỡng `2,147,483,647` thì khi tiếp tục add thêm sản phẩm vào giỏ hàng => Giá tổng sẽ quay lại giá trị âm là `-2,147,483,648` và dần dần reach lại về `0`

<img width="374" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/60fac819-e8f7-407a-bf1c-16f95148729b">

Khi này ta cần điều chỉnh để tổng price trong giỏ hàng đạt giá trị trong khoảng `0-100$`

Nếu trong giỏ hàng có tổng cộng `32123` item `Lightweight "l33t" Leather Jacket` thì tổng price sẽ là `-$1221.96` => Lúc này ta cần add thêm các mặt hàng khác để thỏa mãn điều kiện `0-100$`. Ví dụ, thêm 13 cái `Hydrated Crackers`, ta có giá trị giỏ hàng như sau:

<img width="371" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b20ad906-009c-4637-8c4e-eaf94e9260c0">

Place order and LAB SOLVED!

<img width="582" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d5b1b604-7c0e-4d8a-8301-db84d90ae61a">

***

## 5. Lab: Inconsistent handling of exceptional input

* Content:
```
This lab doesn't adequately validate user input. You can exploit a logic flaw in its account registration process to gain access to administrative functionality. To solve the lab, access the admin panel and delete the user carlos.
```
* Exploit:

Thực hiện đăng nhập và truy cập vào link `/admin` => Xác nhận chỉ user có email @dontwannacry.com mới được permit. 

<img width="599" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0e01d31d-2464-4082-8e59-8dc7557a3974">

Thử thực hiện register với 1 email có địa chỉ dài. Ví dụ: `0123456789a0123456789b0123456789c0123456789d0123456789e0123456789f0123456789g0123456789h0123456789i0123456789j0123456789k0123456789l0123456789m0123456789n0123456789o0123456789p0123456789q0123456789q0123456789@exploit-0a3000f003b29db080aff2a701b80078.exploit-server.net` (268 ký tự)

Sau khi login vào tài khoản trên thì thấy giá trị email chỉ còn như sau:

<img width="591" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/17ca17df-af0e-456c-9928-77e2e30ecd36">

Phần `it-server.net` đã bị cắt mất (13 ký tự) => Xác nhận địa chỉ email sẽ chỉ nhận vào 255 ký tự. Phần còn thừa sẽ bị truncate.

Do vậy Craft một địa chỉ email như sau: `0123456789a0123456789b0123456789c0123456789d0123456789e0123456789f0123456789g0123456789h0123456789i0123456789j0123456789k0123456789l0123456789m0123456789n0123456789o0123456789p0123456789q0123456789q0123456789012345678901234567890123456789@dontwannacry.com.exploit-0a3000f003b29db080aff2a701b80078.exploit-server.net`

Sau khi truncate, giá trị email của user vừa registry chính là thuộc domain `@dontwannacry.com`

<img width="609" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/012ca238-5ad5-4458-8e99-67fbdb998d4b">

Đã có thể truy cập vào link `/admin`

<img width="594" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d99073c5-36d2-48bf-bddb-88a31e70c76a">

Xóa user carlos, LAB SOLVED!

<img width="595" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/8233bd77-feaa-485c-b5ec-b6ecb0002e32">

***

## 6. Lab: Inconsistent security controls

* Content:
```
This lab's flawed logic allows arbitrary users to access administrative functionality that should only be available to company employees. To solve the lab, access the admin panel and delete the user carlos.
```
* Exploit:

Bài này có chức năng update email mà ko cần xác minh => Update email có đuôi `@dontwannacry.com` là ok.

<img width="596" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0b39780d-e730-4973-8b19-383f702acb47">

## 7. Lab: Weak isolation on dual-use endpoint

* Content:
```
This lab makes a flawed assumption about the user's privilege level based on their input. As a result, you can exploit the logic of its account management features to gain access to arbitrary users' accounts. To solve the lab, access the administrator account and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Chức năng đổi password chỉ validate Current Password trên trình duyệt (Client Site) chứ ko validate phía Server Site => Dùng burpsuite đổi được password

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b66253e3-8636-4be6-9fbf-c809b6197571)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a08b601c-07c4-4d8d-87b2-2445a9fd9a1f)

***

## 8. Lab: Password reset broken logic

* Content:
```
This lab's password reset functionality is vulnerable. To solve the lab, reset Carlos's password then log in and access his "My account" page.

Your credentials: wiener:peter
Victim's username: carlos
```
* Exploit:

Thực hiện đăng nhập vào bằng credential `wiener:peter`

Scan bằng tính năng `Discovery Content` của BurpSuite => Tìm ra link `/forgot-password`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bf5eb967-0ce9-4844-8337-0e90a6e8cb6f)

Truy cập sử dụng link trên, bắt được request sau => Đổi username sang carlos

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e3807935-4fb4-429a-a55e-b34d73551d0c)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2c9c3808-3f88-4c0b-83ea-3e993c27d4ea)

***

## 9. Lab: Insufficient workflow validation

* Content:
```
This lab makes flawed assumptions about the sequence of events in the purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Thực hiện purchase 1 món hàng giá dưới 100$ => Xác nhận thấy khi mua thành công, ứng dụng sẽ gọi đến link: `GET /cart/order-confirmation?order-confirmed=true`

=> Thực hiện add sản phẩm `Lightweight l33t leather jacket` vào giỏ hàng rồi call đến link `GET /cart/order-confirmation?order-confirmed=true`

=> Lab Solved!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/626531d8-595f-4b3d-b696-a99090c9a513)

***

## 10. Lab: Authentication bypass via flawed state machine

* Content:
```
This lab makes flawed assumptions about the sequence of events in the login process. To solve the lab, exploit this flaw to bypass the lab's authentication, access the admin interface, and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Lab này giả định người dùng sau khi login sẽ gọi đến `POST /role-selector` để chọn role. => Ta thực hiện drop gói tin này => Mặc định hệ thống login với quyền administrator

Sau đó thực hiện xóa user carlos, LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/67548186-fd3d-4f43-8a34-f28d2fcf1cf7)
