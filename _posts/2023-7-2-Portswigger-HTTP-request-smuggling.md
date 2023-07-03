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


