# [Portswigger] Information Disclosure

***
## 1. Lab: Information disclosure in error messages

* Content:
```
This lab's verbose error messages reveal that it is using a vulnerable version of a third-party framework. To solve the lab, obtain and submit the version number of this framework.
```
* Exploit:
Truy cap link:

<img width="481" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/004efb41-b875-40ee-9b2c-494401543315">

Submit, Lab Resoved!

<img width="581" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ab54d870-f1c4-4efc-aef1-c79f86f42141">

## 2. Lab: Information disclosure on debug page

* Content:
```
This lab contains a debug page that discloses sensitive information about the application. To solve the lab, obtain and submit the SECRET_KEY environment variable.
```
* Exploit:

Dung engagement tool;

<img width="401" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f24152fc-ac2e-40d0-81c0-a89f8a70a119">

Find comment, tim duoc link debug

<img width="591" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/023e64d7-8682-411b-a822-5cd7e97fceee">

Truy cap vao ta lay duoc thong tin SECRET_KEY

<img width="543" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4bced420-9854-474b-8f61-39bf45f173a6">

Submit and Lab Solved!

<img width="585" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ba3a2a65-2216-4497-96e1-f7b5248c78bb">

***

## 3. Lab: Source code disclosure via backup files

* Content:
```
This lab leaks its source code via backup files in a hidden directory. To solve the lab, identify and submit the database password, which is hard-coded in the leaked source code.
```
* Exploit:

Truy cập vào `/robots.txt`

<img width="422" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5da893f5-d550-416f-83c5-1d70101b328f">

Tiếp tục truy cập vào `/backup`

<img width="399" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0d490608-c3aa-4a63-b902-d563584a6419">

Đọc nội dung file

<img width="367" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c9886130-8d05-4347-9453-a607b41c98d2">

Submit and Lab Resolved!

***
## 4. Lab: Authentication bypass via information disclosure

* Content:
```
This lab's administration interface has an authentication bypass vulnerability, but it is impractical to exploit without knowledge of a custom HTTP header used by the front-end.

To solve the lab, obtain the header name then use it to bypass the lab's authentication. Access the admin interface and delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Sử dụng method TRACE => Tìm ra header được add vào mỗi request khi đi qua Front End:

<img width="481" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ff168ffd-c8e5-4efe-996a-df64ac97ee10">

Cấu hình phần Match and Replace trong Burp để thêm Header sau vào mỗi Request

<img width="434" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1fb165b2-5bf8-4f00-b6f0-f12f1d1343ff">

Call đến `/admin` => Xác nhận đã vào được dashboard

<img width="603" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/44831833-2400-4cd2-b14a-01eec7e445ab">

Xóa user carlos => Lab Solved!

<img width="578" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/62893e36-ed67-4dfa-9ed0-a03b4c598e0e">

***

## 5. Lab: Information disclosure in version control history

* Content:
```
This lab discloses sensitive information via its version control history. To solve the lab, obtain the password for the administrator user then log in and delete the user carlos.
```
* Exploit:

Truy cập folder `/.git`:

<img width="388" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1ec2a09d-8517-445c-b22e-d50e6a24327f">

Tải folder git trên về máy local:

<img width="607" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5300ce25-b991-4d25-b90d-77fcb001afc4">

Show thông tin `git show` phát hiện ra password hard code của admin:

<img width="256" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/779da8b2-02d8-427d-af44-5adda1db502b">

Đăng nhập thành công bằng password trên, thực hiện xóa user Carlos => Lab Solved!

<img width="599" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c167b8dd-7bf1-40ab-aa73-906c988f3774">
