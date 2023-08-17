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

***

## 11. Lab: Infinite money logic flaw

* Content:
```
This lab has a logic flaw in its purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Bắt burp History phát hiện 1 vấn đề, khi sử dụng mã coupon giảm 30% để mua gift card 10$ => tài khoản sẽ bị trừ 7$. Sau đó dùng gift card vừa nhận được redeem => Tài khoản sẽ đc +10$. Khi này số dư tài khoản ban đầu từ 100$ sẽ thành 103$

<img width="377" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6c1b8045-32e5-4ce7-bdbb-762b2c6896b0">

Vấn đề sai là mã coupon 30% được dùng không giới hạn => Khi ta tiếp tục quy trình như trên thì tài khoản tiếp tục được tăng thêm 3$

=> Thực hiện bước để automatic quá trình trên:

Trên Burpsuite, vào phần `Project options` => `Session Handling Rules` => `Add` => Phần Rule Action, chọn `Add` => Phần `Select Macro`, chọn `Add`. Một khung config macro hiện ra

Tiếp tục chọn các request sẽ sử dụng trong macro này => Chọn lần lượt như sau: 
`POST /cart`
`POST /cart/coupon`
`POST /cart/checkout`
`GET /cart/order-confirmation?order-confirmed=true`
`POST /gift-card`

Sau khi đã chọn các request trên, click OK => Màn hình config hiện ra như sau:

<img width="586" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d1a67696-22a1-4de4-9461-4dafcd755066">

Chọn request số 4 `GET /cart/order-confirmation?order-confirmed=true` => `Configure item` => Ở phần `Custom Parameter` chọn `Add` => Chọn `Parametter name` là `gift-card`, đồng thời high light phần mã gift card code hiện trong Response => Click OK.

<img width="635" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c59a6939-d80e-477b-ba71-8a086c53a1e2">

Chọn Request số 5 `POST /gift-card`, ở phần `Parametter Handling`, chọn parametter name `gift-card` => Chọn setup như hình:

<img width="379" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ee9b5020-0885-4c54-b2f1-beaa60d39329">

Thực hiện gửi request `GET /my-account` đến Intruder, cấu hình Attack type `sniper` và gửi 412 payload dạng `Null Payload`

=> Sau khi thực hiện xong, tài khoản sẽ đủ tiền để mua `Lightweight l33t leather jacket` => LAB SOLVED!

<img width="592" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f5cd2a97-2f79-4ba0-920a-e93c200855ef">

***

## 12. Lab: Authentication bypass via encryption oracle

* Content:
```
This lab contains a logic flaw that exposes an encryption oracle to users. To solve the lab, exploit this flaw to gain access to the admin panel and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Thực hiện login vào tài khoản `wiener:peter` với tùy chọn `Stay logged in` => Theo dõi trên Burpsuite thấy rằng Session được set cookie tên là `stay-logged-in`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/443acbd6-5037-4559-bbd3-a91a4aee716f)

Thực hiện Post 1 comment với một email không hợp lệ trong bài viết bất kỳ thì thấy là Session được set thêm 1 biến cookie có tên là `notification` có cùng kiểu mã hóa giống cookie `stay-logged-in`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/61382346-fee5-4154-bef1-3d2c7eb8d0df)

Truy cập vào nội dung bài viết, ta thấy nội dung của cookie `notification` được reflect sang Response dưới dạng Clear text

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e51d5ce7-256f-48d1-b52e-4ea7c21d750f)

Tiếp tục thử copy nội dung value của cookie `stay-logged-in` sang `notification` để xem value giải mã ra có nội dung gì

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2800d0b4-5a5f-450f-8fdd-fc4927c92ef6)

 => Thấy giá trị là `wiener:1692285956470` => Ta thử fake sang tài khoản admin như sau => `administrator:1692285956470`

 ![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/12ad43e8-602c-4389-855b-3fb4bd25277d)

Lấy giá trị thu được để hiển thị value ra bên ngoài => Nhận được giá trị là: `Invalid email address: administrator:1692285956470`

 ![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/376597f7-9137-4e20-b9a8-7b50e06371b9)

Do ta chỉ mong muốn value `administrator:1692285956470` nên sẽ cần loại bỏ phần `Invalid email address: ` (gồm 23 characters) đi. Thực hiện chuyển phần value trong biến `notification` sang tab Decoder và thực hiện lần lượt như sau:

Decode as URL => Decode as Base64 => Xóa đi 23 bytes => Encode as Base64 => Encode as URL

Copy giá trị vừa thua được vào biến `notification` => Nhận được error báo `Input length must be multiple of 16 when decrypting with padded cipher`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/555c60fa-c023-4449-b3c1-e9d8f9c62c58)

Error là do ta xóa đi 23 character nên input đầu vào ko còn đảm bảo là tích của 16 => Lần này cần xóa đi 32 characters (16x2) => Thêm đoạn padding 9 ký tự bất kỳ vào như sau: `xxxxxxxxxadministrator:1692285956470`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e60c5991-cbb6-4f07-84ef-4e04160eeb69)

Tiếp tục cho dữ liệu thu được vào Decoder như trước, lần này ta xóa đi 32 bytes:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b2bd376d-a956-4778-9d04-ec00ec283758)

Lấy giá trị thu được cho vào biến `stay-logged-in`, đồng thời xóa giá trị Cookie Session ID nếu có đi => Đã vào được tài khoản administrator

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/8e6b8ee5-397a-4e31-9557-42e52f54caee)

Thực hiện xóa user carlos => LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7b6a5bf5-3a0f-4710-aac9-089ebd8b4c79)





