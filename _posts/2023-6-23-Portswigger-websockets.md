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



