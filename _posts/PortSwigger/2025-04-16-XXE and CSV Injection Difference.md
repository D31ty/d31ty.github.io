---
date: 2025-04-16
linktitle: xxe&csv
title: XXE and CSV Injection Difference
showreadingtime: true
tags: ['PortSwigger']
categories: ['PortSwigger']
---

### 🧨 **1. XXE (XML External Entity) Injection**

**Target**: Applications that parse XML input  
**Cause**: Improper XML parser configuration that allows external entity declarations  
**Exploitation**: Attackers inject malicious XML with external entities that can:

- Read local files (`file://`)
    
- Perform SSRF (Server-Side Request Forgery)
    
- Potentially execute remote code (in edge cases)
    

**Example Payload**:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<root>
  <data>&xxe;</data>
</root>
```

**Impact**:

- Data exfiltration (e.g., `/etc/passwd`)
    
- Internal network scans (via SSRF)
    
- Denial of Service
    

---

### 📊 **2. CSV Injection (Formula Injection)**

**Target**: Applications that export data to spreadsheets (like Excel)  
**Cause**: Unescaped user input exported into CSV files  
**Exploitation**: Attackers inject spreadsheet formulas like `=cmd|' /C calc'!A0` that execute when the file is opened in Excel or similar

**Example Payload**:

```
=HYPERLINK("http://attacker.com","Click me")
```

or

```
=cmd|' /C calc'!A0
```

**Impact**:

- Code execution (on the client machine)
    
- Phishing via links
    
- Exfiltration of data (e.g., using `WEBSERVICE()` or `HYPERLINK()`)
    

---

### 🧾 Summary Table

|Feature|XXE Injection|CSV Injection|
|---|---|---|
|**Affects**|XML parsers|Spreadsheet software (Excel, etc.)|
|**Input type**|XML|CSV / spreadsheet rows|
|**Goal**|Server-side file access, SSRF, etc.|Client-side code execution or phishing|
|**Risk Level**|Server compromise|Client compromise|
|**Mitigation**|Disable external entities in XML parsers|Sanitize values starting with `=`, `+`, `-`, `@`|

Now you may ask - are excel sheets are combination of/written in xml language?

Nice catch — yes, modern **Excel files (.xlsx)** are actually **ZIP archives** that contain **XML files** inside. 😲

Let me break that down:

---

### 📁 **Modern Excel File Format (.xlsx)**

- Introduced with **Office 2007**, the `.xlsx` format is based on **Open XML**, a Microsoft-developed standard.
    
- If you rename an `.xlsx` file to `.zip` and unzip it, you'll see a structure like:
    

```
[Content_Types].xml
_rels/
xl/
  workbook.xml
  worksheets/
    sheet1.xml
  styles.xml
  sharedStrings.xml
docProps/
```

Each worksheet (like `sheet1.xml`) is stored as an **XML file** describing cells, styles, data types, etc.

---

### 🔍 So Does That Mean CSV = XML?

Nope! Even though `.xlsx` uses XML **internally**, **CSV files are completely different**:

|File Type|Format|Structure|
|---|---|---|
|`.xlsx`|XML in ZIP|Complex, styled, structured|
|`.csv`|Plain Text|Simple rows and columns separated by commas|

**CSV files** are just **text-based** and don’t use XML at all. They’re usually generated/exported as raw data, like:

```
Name,Email,Comment
John,john@example.com,"=HYPERLINK('http://evil.com')"
```

So, **CSV Injection ≠ XML**, but **Excel** (the app) can process both `.csv` and `.xlsx`.

---

### 🔐 Bottom Line

- **CSV injection** abuses Excel formulas in `.csv` files.
    
- **XXE** abuses vulnerabilities in **XML parsers**.
    
- `.xlsx` files are XML-based, but CSVs are not.
    

## **CSV File upload Scenarios:**

| Scenario                                                     | Vulnerability?                                | Payload Example                     |
| ------------------------------------------------------------ | --------------------------------------------- | ----------------------------------- |
| User uploads `.csv` resume file                              | ✅ Yes, if viewed in Excel                     | `=WEBSERVICE("http://yourserver")`  |
| You input a comment, and the site exports comments to `.csv` | ✅ Yes                                         | `=HYPERLINK("http://attacker.com")` |
| CSV file gets parsed server-side                             | ❌ Usually safe (if no Excel viewing involved) | N/A                                 |
## **XXE File upload Scenarios:**
|Scenario|Vulnerability?|Notes / Payload Example|
|---|---|---|
|Site accepts **`.xml` file upload**|✅ Yes|Try basic XXE to read `file:///etc/passwd`|
|Site accepts **`.docx` or `.xlsx` uploads**|✅ Sometimes|These are ZIP files with embedded XML (inject in `word/document.xml`, etc.)|
|Site imports **user config via XML**|✅ Yes|Classic XXE opportunity|
|Site parses **SVG files** (they’re XML too)|✅ Yes|Inject `<!ENTITY>` inside SVG — many parsers are vulnerable|
|Site parses XML **but uses secure parser**|❌ No|If external entities are disabled (good config), XXE won’t work|
|Site uses **JSON only**|❌ No|XXE only works in XML-based data|
|Site returns an error after XML upload|🟡 Maybe|Could be trying to parse it — test further with blind XXE or SSRF payloads|
|File upload leads to **internal API processing XML**|✅ Yes|Upload XML file → backend service parses it (even if you don’t see output)|