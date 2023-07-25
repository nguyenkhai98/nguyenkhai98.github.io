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

***

## 4. Lab: JWT authentication bypass via jwk header injection

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. The server supports the jwk parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.

To solve the lab, modify and sign a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.
```
* Exploit:

Generate một key RSA new:

<img width="450" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/68af6119-61a9-48b6-8751-6d86cdaf795e">

Chuyển sang tab Repeater, chọn `JSON Web Token` => `Attacke` => `Embedded JWK`

<img width="479" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bcc28ce4-fdd3-49dd-8db4-ca1264378931">

Chọn key RSA vừa tạo ở bước trên, click OK.

<img width="169" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/785e5316-6528-4bbf-9879-55763c3b54e7">

Send Request => Đã truy cập được vào link `/admin`

<img width="481" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f1c8b027-2ca8-43cc-9b22-59320f426acc">

Tiếp tục thực hiện xóa user Carlos:

<img width="479" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f29879bf-af5a-4794-8b01-a1ff24deceeb">

Lab Solved!

<img width="586" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/aa74e89b-6748-4127-9ff8-9ad7d870c90f">

***

## 5. Lab: JWT authentication bypass via jku header injection

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. The server supports the jku parameter in the JWT header. However, it fails to check whether the provided URL belongs to a trusted domain before fetching the key.

To solve the lab, forge a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.
```
* Exploit:

Thực hiện gen mới khóa RSA trong tab `JWT Editor Keys`:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0e54be4d-753d-4757-a2a1-a670a164af84)

Chuột phải vào key vừa tạo chọn `Copy Public Key as JWT` => Paste nội dung vào Server Exploit:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7157777b-48ed-4209-aed7-4dc77ca6f5f3)

Sửa payload các thông tin sau trong tab `JSON Web Token`, ấn vào Sign => Chọn ký với Khóa RSA vừa tạo. Send Payload, xác nhận đã vào được link `/admin`:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a1cb545f-51b7-421a-a8b5-254717e6b113)

Thực hiện xóa User carlos:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/94dfbbd5-7bc0-498d-b28f-4e78efa27c82)

=> LAB Solved!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f21e2220-51d1-4451-8d86-4020968dbcf6)

***

## 6. Lab: JWT authentication bypass via kid header path traversal

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. In order to verify the signature, the server uses the kid parameter in JWT header to fetch the relevant key from its filesystem.

To solve the lab, forge a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.
```
* Exploit:

Thực hiện tạo Symmetric key với giá trị là `null` (Base64 của null là `AA==`)

<img width="264" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bb59b9aa-f93a-4f5f-9378-0169276f8acd">

Thực hiện Sign lại JWT mới với key vừa tạo, chỉnh sửa đường dẫn `kid` về `../../../../../../../../dev/null`, `sub` thành `administrator` và gửi request => Đã truy cập được link `/admin`

<img width="479" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/100ac7ed-bcf3-411e-96c8-f905f48cae00">

Tiến hành xóa user carlos:

<img width="472" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4e216014-7310-456c-bea4-64715b43d264">

Lab Solved!

<img width="590" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0a310314-189d-474f-8971-0d86e8670846">

***

## 7. Lab: JWT authentication bypass via algorithm confusion

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. It uses a robust RSA key pair to sign and verify tokens. However, due to implementation flaws, this mechanism is vulnerable to algorithm confusion attacks.

To solve the lab, first obtain the server's public key. This is exposed via a standard endpoint. Use this key to sign a modified session token that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.
```
* Exploit:

Trong tab `JWT Editor Keys` tạo new RSA key:

<img width="259" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/df315b87-0099-4e81-9732-e45c580cd01b">

Chuyển sang định dạng pem và copy sang Tab `Decoder` để thực hiện `Base64 Encode`

<img width="261" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/543b393d-2757-47f2-b989-eb8421cf4466">

<img width="540" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0c6ae2f0-d239-4585-a286-23b83a01d081">

Sử dụng giá trị Encode ở bước trên, tạo 1 khóa Symmetric trong tab `JWT Editor Keys`

<img width="639" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b4e65f76-75d4-4732-82fd-0ed3af101118">

Chỉnh payload `alg` thành `HS256`, `sub` thành `administrator` => Sign and Send Request

<img width="232" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5bcddfdb-6e92-4ea5-8233-133e4cf9e0ca">

<img width="495" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/75598357-8d9d-46f6-8572-04cf7c098320">

Xóa user carlos:

<img width="493" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c7e2c1b6-e0bf-40ec-b54d-cf4802397583">

LAB Solved!

<img width="582" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/8a1a6271-3215-4512-83eb-ba6a551765a1">
