# [Portswigger] HTTP Host header attacks

***

## 1. Lab: Basic password reset poisoning

* Content:
```
This lab is vulnerable to password reset poisoning. The user carlos will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account.

You can log in to your own account using the following credentials: wiener:peter. Any emails sent to this account can be read via the email client on the exploit server.
```
* Exploit:

Thực hiện thử nghiệm tính năng forgot-password => Xác nhận đã nhận được email báo link reset: 

<img width="607" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/549e998e-516b-4563-9254-dd43479467f7">

Theo dõi trên burp history => Phát hiện ra request `POST /forgot-password` sau:

<img width="622" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e5452b39-f057-41f9-8508-9505d3a4dc46">

Chuyển request này sang Burp Repeater => Thử thay đổi giá trị `Host` thì thấy là Request vẫn được xử lý bình thường:

<img width="626" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/efe595a2-d39f-476d-9568-725e74d0e16b">

Đồng thời Email Client nhận được link reset password theo Host fake:

<img width="604" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9837ba05-bd86-497a-9243-6bcb45f70c68">

=> Thử fake username thành `carlos` và `Host` header về địa chỉ của Exploit Server:

<img width="626" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/893affae-070c-44c6-bde0-4d95da97d71f">

=> View web access trên Exploit Server => Phát hiện request sau:

<img width="630" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/13b4f24b-f845-42d0-8bed-28ce5ec19a88">

Truy cập link trên với Host chuẩn => Thực hiện đổi pass => Đã thành công đổi pass của user `carlos`. 
Sau đó sử dụng password mới đổi này để login với user `carlos`. => LAB SOLVED!

<img width="585" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/34429f22-c562-49ff-96c5-94dd35150517">

***

## 2. Lab: Password reset poisoning via middleware

* Content:
```
This lab is vulnerable to password reset poisoning. The user carlos will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account. You can log in to your own account using the following credentials: wiener:peter. Any emails sent to this account can be read via the email client on the exploit server.
```
* Exploit:

Thực hiện reset password và bắt traffic trên BurpSuite => Capture thấy có request sau:

<img width="625" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6e501f98-a0f8-4b3d-8610-18ac34d13c1b">

Thử nghiệm thêm 1 header tên là `X-Forwarded-Host` => Response vẫn trả về 200 - OK

<img width="627" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c627f3e0-44d0-4f50-926b-1cdb7a6afca0">

Đồng thời nhận được email báo link reset password trỏ về địa chỉ host trong `X-Forwarded-Host`

<img width="591" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d8fa58cd-f72e-462f-b4b6-644875c9d50d">

OK, vậy là có thể lợi dụng header này => Lần này fake sang host của Exploit Servẻ và username sang `carlos`

<img width="622" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6cc99dff-d7e4-46ff-92f1-a9de54d72b76">

Mặc định user carlos sẽ click vào mọi link nhận được trong hòm thư => Sẽ truy cập vào link reset password vừa nhận được. Bắt access log trên server exploit, ta thấy:

<img width="638" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/051b6073-dc08-4f99-ae01-db593fa02ec3">

Sử dụng token trên để reset password của carlos rồi truy cập vào => LAB SOLVED!

<img width="590" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/df28af6d-4fc6-4c6b-ab76-263e77acade6">

***

## 3. Lab: Password reset poisoning via dangling markup

* Content:
```
This lab is vulnerable to password reset poisoning via dangling markup. To solve the lab, log in to Carlos's account.

You can log in to your own account using the following credentials: wiener:peter. Any emails sent to this account can be read via the email client on the exploit server.
```
* Exploit:

Thực hiện reset password và bắt traffic trên BurpSuite, theo dõi hiện tượng thấy email client trả nội dung như sau:

<img width="596" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f788646f-d014-442a-a682-ac6bfdea19e5">

Nhận định thấy lần này ứng dụng không trả về link reset pass nữa mà trả về luôn password mới kèm link login cho user.

Đẩy request `POST /forgot-password` lên Burp Repeater và thực hiện thêm tham số port ở `Host` Header => Thấy response vẫn trả về thành công.

<img width="626" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b36d84ca-d364-49ab-989d-64ebe9295274">

Theo dõi nội dung trả về trong email Client => Thấy phần port được Refected vào trong phần link Login.

<img width="622" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c34eb66f-4ded-4aed-8681-703013174770">

Craft payload cho phần port như sau (thay bằng link của Exploit server) + đồng thời chỉnh `username=carlos`

<img width="625" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/17999656-859c-4a79-8090-8f03491542f1">

Lúc này nội dung email raw sẽ như sau (giả lập vẫn dùng user wiener để nhận được nội dung mail):

`<p>Hello!</p><p>Please <a href='https://0ac2001f04ae4f6c802b915300c400ce.web-security-academy.net:'<a href="//exploit-0a29001404a04fc980cc90c501ba004d.exploit-server.net/?/login'>click here</a> to login with your new password: T8L7r8ECTp</p><p>Thanks,<br/>Support team</p><i>This email has been scanned by the MacCarthy Email Security service</i>`

=> Khi view trên Email Client sẽ chỉ thấy phần Body như sau (giả lập vẫn dùng user wiener để nhận được nội dung mail):

<img width="593" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/32d8c341-e35d-4823-9701-f23b76df3ab9">

Tuy nhiên giả định rằng user carlos sẽ click vào mọi link nhận được trong Email => Sẽ thấy có log access truy cập vào Exploit Server:

<img width="625" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/da036647-e5d9-48ad-bba3-3f26990aaeb6">

=> Có thể dùng password trên để truy cập vào user carlos => LAB SOLVED!

<img width="580" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a15958d5-1c89-4bc9-a406-f235f12e263e">

