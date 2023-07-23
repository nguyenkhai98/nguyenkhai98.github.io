# [Portswigger] JWT attacks

***

## 1. Lab: JWT authentication bypass via unverified signature

* Content:
```
This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter

Tip
We recommend familiarizing yourself with how to work with JWTs in Burp Suite before attempting this lab.
```
* Exploit:

Bắt request qua BurpSuite và chuyển request đến `/admin` qua Repeater
![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/bea32242-5232-41fc-8ac6-765da1d6a36c)

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d28ce9cc-0dfd-46d4-94bc-7fda9b2d27eb)

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1cd0bbf8-385f-4ac3-8780-d678d72ce220)

