# [Portswigger] GraphQL API vulnerabilities

Trước tiên cần cài đặt Extension InQL (Extension này yêu cầu Jython => Down file jar bản standalone về rồi add đường dẫn vào trong phần Extender => Option => config Python Environment như sau:

<img width="401" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/35886676-50e8-48d1-96ff-7c7adb16fd58">

***

## 1. Lab: Accessing private GraphQL posts

* Content:
```
The blog page for this lab contains a hidden blog post that has a secret password. To solve the lab, find the hidden blog post and enter the password.

We recommend that you install the InQL extension before attempting this lab. InQL makes it easier to modify GraphQL queries in Repeater, and enables you to scan the API schema.

For more information on using InQL, see Working with GraphQL in Burp Suite.
```
* Exploit:

Thực hiện truy cập trang web và view trong Burp History => Phát hiện request gọi đến GraphQL như sau `POST /graphql/v1` :

<img width="624" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6be89567-9496-43e0-b13d-f085cf8b2160">

Tuy nhiên phát hiện thấy thiếu phần `"id": 3`

Copy link URL paste vào InQL Scanner => Ta thấy Scanner cho ta thông tin của 2 Query là "getAllBlogPosts" và "getBlogPost"

<img width="514" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/28d259b3-31ab-4d64-8540-53b18718fb34">

Chuột phải vào query "getBlogPost" và chọn Send to Repeater. Trong Repeater, chỉnh id thành 3:

<img width="625" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/388e71c9-eb59-4111-abec-4b6b72230cbc">

=> Get được nội dung của Post ID = 3 kèm postPassword. => LAB SOLVED!

**

## 2. Lab: Accidental exposure of private GraphQL fields

* Content:
```
The user management functions for this lab are powered by a GraphQL endpoint. The lab contains an access control vulnerability whereby you can induce the API to reveal user credential fields.

To solve the lab, sign in as the administrator and delete the username carlos.

We recommend that you install the InQL extension before attempting this lab. InQL makes it easier to modify GraphQL queries in Repeater, and enables you to scan the API schema.

For more information on using InQL, see Working with GraphQL in Burp Suite.
```
* Exploit:

Thực hiện truy cập vào tính năng `My Account` => Bắt request trên Burp History và sử dụng InQL Scanner:

<img width="524" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c3b3299c-8f37-4ace-b072-8d9d8e9f4d94">

Phát hiện 1 query tên là "getUser" => Sử dụng Query này với id = 1 => PHát hiện user/pass của administrator

<img width="551" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/836f35f0-dd74-4196-9621-a75f79a28555">

LAB SOLVED!

<img width="578" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9a0103c0-71bf-483a-8ecd-0b77d4bdbd57">
