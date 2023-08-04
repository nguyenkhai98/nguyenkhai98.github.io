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

<img width="237" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/23bb1dab-3586-45cd-a8df-241d2946a1fa">

<img width="577" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9c142a50-1849-4a95-b0b9-031a10a4d9a3">

LAB SOLVED!
