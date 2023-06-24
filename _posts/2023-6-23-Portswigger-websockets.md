# [Portswigger] WebSockets

***

## 1. Lab: Manipulating WebSocket messages to exploit vulnerabilities

* Content: 
```
This online shop has a live chat feature implemented using WebSockets.

Chat messages that you submit are viewed by a support agent in real time.

To solve the lab, use a WebSocket message to trigger an alert() popup in the support agent's browser.
```

* Giao diện web:
<img width="621" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4863f28f-ee8e-4f85-a362-e674c9b5d436">

* Exploit:

Bắt request với Burpsuite và Sent to Repeater:

<img width="634" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/e42ab7f8-8cd9-4c04-b23d-d3d359c0206b">

<img width="630" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ee8ca367-37e0-44f0-aa52-45b84c6a3831">


## 2. Lab: Manipulating the WebSocket handshake to exploit vulnerabilities

* Content:
```
This online shop has a live chat feature implemented using WebSockets.

It has an aggressive but flawed XSS filter.

To solve the lab, use a WebSocket message to trigger an alert() popup in the support agent's browser.
```

Giao diện web tương tự Lab #1

* Exploit:

Dùng payload của Lab 1 cho Lab này sẽ bị chặn và bị ứng dụng Block IP

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/46c1088c-567a-4bff-b369-6e2b2ed90427)

Sau khi bị block IP thì Websocket Connection cũng bị disconnect => Cần Reconnect. Lab 2 detect IP bằng trường X-Forwarded-For Header => Ta fake source IP khác vào trường này để ứng dụng cho phép Reconnect.

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/927ef4f0-baa8-44b6-b2dd-42b60851d19a)

Nhiều khả năng payload `onerror` hay `alert` đã bị filter, ta thử biến đổi payload 1 chút:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/cf00a12a-915d-4562-8608-9ca35f23574f)

Biến đổi `onerror` => `oNerRor` thì vẫn bị block và chặn IP. Ta tiếp tục thử payload khác:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/edc02a7c-5718-473f-bf72-b9300abf40ca)

Lần này payload đã không bị block. Tuy nhiên, làm thế nào để view được kết quả của payload trên được trả về trên trình duyệt???
=> Thực hiện bật Burpsuite ở chế độ Intercept **On** và gọi lại request vào link `/chat`. Sau đó tiến hành bổ sung thêm **X-Forwarded-For Header** với giá trị IP bằng với giá trị mà ta sử dụng khi send payload ```<img src=1 oNerRor=alert`1` />``` => Foward gói tin đến trình duyệt.

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d52a8401-c478-4374-b1ed-fc5e0a3db5f2)

Tiếp tục thực hiện như vậy, cuối cùng trình duyệt đã hiển thị thông báo **Lab Solved**.

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a92c0ce1-fe3a-4f53-b410-97079d20be91)

