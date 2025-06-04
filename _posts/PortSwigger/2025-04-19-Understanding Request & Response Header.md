---
date: 2025-04-19
linktitle: request&response
title: Understanding request and response
showreadingtime: true
tags: ['PortSwigger']
categories: ['PortSwigger']
---

## ✅ **Security Headers (Response)**

These help protect against XSS, clickjacking, MIME sniffing, and enforce secure communication.

|Header|Purpose|
|---|---|
|`Strict-Transport-Security`|Forces HTTPS for all requests (HSTS).|
|`Content-Security-Policy`|Controls resources the user agent is allowed to load.|
|`X-Content-Type-Options: nosniff`|Prevents MIME sniffing.|
|`X-Frame-Options: DENY / SAMEORIGIN`|Prevents clickjacking (iframe embedding).|
|`X-XSS-Protection: 1; mode=block`|Enables browser XSS filter (legacy).|
|`Referrer-Policy`|Controls how much referrer info is sent.|
|`Permissions-Policy`|Controls access to browser features like camera, mic, geolocation.|
|`Cross-Origin-Embedder-Policy`|Enforces stricter isolation for cross-origin resources.|
|`Cross-Origin-Opener-Policy`|Prevents window from being shared with cross-origin content.|
|`Cross-Origin-Resource-Policy`|Controls sharing of resources with other origins.|
|`Set-Cookie: Secure; HttpOnly; SameSite=Strict`|Protects cookies from XSS and CSRF.|
|`X-Permitted-Cross-Domain-Policies`|Restricts Flash/Adobe cross-domain policies.|
|`X-Download-Options: noopen`|Prevents file auto-open in IE.|

---

## 🌍 **CORS Headers (Response)**

These control cross-origin access to resources.

|Header|Purpose|
|---|---|
|`Access-Control-Allow-Origin`|Specifies allowed origins.|
|`Access-Control-Allow-Credentials`|Allows cookies/auth headers in CORS requests.|
|`Access-Control-Allow-Methods`|Specifies allowed HTTP methods for CORS.|
|`Access-Control-Allow-Headers`|Specifies allowed custom headers.|
|`Access-Control-Expose-Headers`|Allows specific response headers to be accessed by client.|
|`Vary: Origin`|Ensures correct caching based on origin.|

---

## 🔒 **Security-Relevant Request Headers**

|Header|Purpose|
|---|---|
|`Authorization`|Sends credentials (Basic, Bearer tokens).|
|`Cookie`|Sends cookies (can be secured with flags).|
|`Origin`|Indicates origin of the cross-site request.|
|`Referer`|Tells server which page made the request.|
|`User-Agent`|Identifies client software (can be fingerprinted).|
|`Host`|Specifies the domain being accessed.|
|`X-Requested-With`|Used in AJAX requests to detect JavaScript-based access.|
|`Forwarded / X-Forwarded-For / X-Real-IP`|Reveal client's original IP in proxied setups.|

---

## 🚦 **Rate Limiting & Abuse Protection (Response)**

| Header                  | Purpose                                            |
| ----------------------- | -------------------------------------------------- |
| `X-RateLimit-Limit`     | Max number of requests allowed.                    |
| `X-RateLimit-Remaining` | Requests remaining in window.                      |
| `X-RateLimit-Reset`     | Time when the rate limit resets.                   |
| `Retry-After`           | Suggests when to retry after rate limiting or 503. |

---
## ⚡ **Performance & Caching Headers**

|Header|Purpose|
|---|---|
|`Cache-Control`|Controls caching behavior (e.g., no-store, public).|
|`ETag`|Identifier for a specific version of a resource.|
|`Last-Modified`|Timestamp of last resource update.|
|`Expires`|Date/time after which response is considered stale.|
|`Vary`|Instructs caches to vary based on request headers.|
|`Transfer-Encoding: chunked`|Allows streaming responses in chunks.|

---

## 📦 **Content & File Management Headers**

|Header|Purpose|
|---|---|
|`Content-Type`|MIME type of the resource.|
|`Content-Length`|Size of the body in bytes.|
|`Content-Disposition`|Controls if content is inline or attachment (download).|
|`Content-Encoding`|Indicates compression method (e.g., gzip, br).|
|`Accept`|Media types the client accepts.|
|`Accept-Encoding`|Compression types client supports.|
|`Accept-Language`|Languages the client prefers.|

---
## 🔁 **Redirection & Response Control**

|Header|Purpose|
|---|---|
|`Location`|Used with 3xx responses to redirect.|
|`Retry-After`|Used with 429/503 to indicate retry time.|
|`Link`|Preloading resources, pagination, etc.|

---
## 🛠️ **Custom Headers (X-Headers)**

|Header|Purpose|
|---|---|
|`X-Request-ID / X-Correlation-ID`|Trace a request across systems.|
|`X-Powered-By`|Reveals backend tech (often removed for security).|
|`X-App-Version`|Indicates the API or app version.|

---
# Headers from DHL: 

1. `HttpOnly` - Mitigates XSS
2. `no-cache`: Forces revalidation with the server before use.
3. `no-store`: Don’t store any part of the response.
4. `max-age=0`: The response is immediately stale.
5. `must-revalidate`: Client must validate the response with the server before using cached data.
### **CORS (Cross-Origin Resource Sharing) Headers**

**`Vary: Access-Control-Request-Headers`**
- Instructs caches to consider request headers like `Access-Control-Request-Headers` when deciding if a cached response is valid.
**`Access-Control-Expose-Headers: Authorization`**
- Allows the client (JavaScript) to access the `Authorization` header in the response.
**`Access-Control-Allow-Credentials: true`**
- Indicates that the response can be shared with requests that include credentials (like cookies, HTTP auth).
**`Access-Control-Allow-Origin: *`**
- Allows any domain to access the resource. Not recommended with `Allow-Credentials: true`

### **Rate Limiting Headers**

**`X-RateLimit-Remaining: -1`**
- Indicates how many requests are remaining in the current rate limit window. `-1` may mean unlimited or rate limit not enforced.
**`X-RateLimit-Requested-Tokens: 1`**
- Custom header indicating how many tokens (or cost) this request used.
**`X-RateLimit-Burst-Capacity: 50`**
- Max number of requests that can be made in a burst.
**`X-RateLimit-Replenish-Rate: 25`**
- How quickly the token bucket is refilled (requests per second/minute etc.).

### **Security Headers**
**`X-XSS-Protection: 1; mode=block`**
- Enables XSS filter in some browsers and blocks detected attacks.
**`Strict-Transport-Security: max-age=631138519`**
- Enforces HTTPS connections for a specified period (in seconds). Helps prevent man-in-the-middle attacks.
**`X-Frame-Options: DENY`**
- Prevents the page from being embedded in an iframe (clickjacking protection).
**`X-Content-Type-Options: nosniff`**
- Prevents browsers from MIME-sniffing a response away from the declared content type.
**`Referrer-Policy: no-referrer`**
- Specifies that the browser should not send any `Referer` header.
**`Content-Security-Policy: default-src 'none'`**
- Restricts where resources (scripts, images, etc.) can be loaded from. `'none'` blocks everything unless overridden.
**`X-Download-Options: noopen`**
- Prevents file downloads from automatically opening in Internet Explorer.
**`X-Permitted-Cross-Domain-Policies: none`**
- Restricts Flash/Adobe PDF cross-domain data loading policies.
