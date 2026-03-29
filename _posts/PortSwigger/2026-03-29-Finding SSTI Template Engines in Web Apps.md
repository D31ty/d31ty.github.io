---
date: 2026-03-29
linktitle: template_engines_ssti
title: Finding SSTI Template Engines in Web App
showreadingtime: true
tags: ["PortSwigger"]
categories: ["PortSwigger"]
---

{% raw %}

# Techniques to Identify Template Engines (Pre-SSTI Phase)

Identifying whether a web application uses a server-side template engine is a critical precursor to SSTI testing. In modern architectures—especially hybrid SSR + CSR apps—this is no longer obvious. Below are advanced methodologies used in professional security testing.

---

## 1. Differential Response Analysis

Instead of simply checking reflection, analyze **how the response changes under controlled input variations**.

### Method

Send structured payloads that would behave differently in:

- Plain text rendering  
- HTML escaping contexts  
- Template evaluation contexts  

### Example test inputs

```txt
test123
test{{7*7}}
test${7*7}
test<%=7*7%>
```

### What to observe

- Does the response length change significantly?  
- Are certain characters stripped, escaped, or preserved?  
- Are delimiters (`{{`, `${`) treated specially?  

### Insight

Template engines often preprocess input before rendering, leading to:

- Silent failures  
- Partial evaluation  
- Syntax normalization  

---

## 2. Context-Sensitive Injection Mapping

Advanced identification involves determining **where in the rendering pipeline your input lands**.

### Possible contexts

- HTML body  
- Attribute values  
- JavaScript blocks  
- Inline CSS  
- Inside existing template expressions  

### Technique

Inject payloads that behave differently depending on context:

```txt
"onmouseover=alert(1)"
{{7*7}}
</script><script>alert(1)</script>
```

### Goal

Map whether input is:

- Rendered directly  
- Escaped  
- Evaluated  
- Embedded inside another template expression  

### Why this matters

Some SSTI cases only trigger when input is placed inside an existing template block (second-order injection).

---

## 3. Template Syntax Polyglots

Instead of testing one syntax at a time, use **polyglot payloads** that target multiple engines simultaneously.

### Example

```txt
{{7*7}}${7*7}<%=7*7%>#{7*7}
```

### Analysis

- If only part of the payload evaluates, you can infer the engine  
- If none evaluate but output is altered, it still indicates preprocessing  

### Advanced variation

```txt
{{7*'7'}}
```

- Jinja2 → `7777777`  
- Twig → error  
- Some engines → literal output  

---

## 4. Error-Oriented Fingerprinting

Modern apps often suppress errors, but **forcing edge-case failures** can still reveal clues.

### Techniques

```txt
{{7*
```

```txt
{{7|unknown_filter}}
```

```txt
{{a.b.c.d.e}}
```

### What to look for

- Differences in HTTP status codes  
- Response timing changes  
- Partial rendering before failure  
- Hidden debug messages in comments  

---

## 5. Timing-Based Detection

In blind scenarios, you can detect template evaluation via **timing side channels**.

### Example

```txt
{{range(10000000)}}
```

### Observation

- Increased response time suggests server-side evaluation  
- No delay suggests plain text handling  

---

## 6. Behavioral Fuzzing with Grammar Awareness

Instead of random fuzzing, use **template-aware fuzzing strategies**.

### Examples

```txt
{{7*7}}
{{7|length}}
{{7 if 1 else 0}}
{{for i in range(5)}}
```

### Insight

Even if expressions don’t execute, behavior differences reveal engine presence.

---

## 7. Second-Order Injection Detection

In many real-world cases, input is not rendered immediately but stored and later used in templates.

### Example flow

1. User submits input → stored in database  
2. Admin panel renders stored data inside template  

### Detection strategy

- Inject template syntax into stored fields  
- Trigger different application flows  
- Observe delayed execution  

---

## 8. Response Canonicalization Analysis

Some template engines normalize or transform input before rendering.

### Test

```txt
{{7*7}}
{{ 7 * 7 }}
{{7* 7}}
```

### Observation

- Does the output normalize spacing?  
- Are certain characters removed?  

---

## 9. Cross-Feature Interaction Testing

Template engines often integrate with:

- Internationalization (i18n)  
- Email rendering  
- PDF generation  
- Logging systems  

### Technique

Inject payloads into:

- Email templates  
- Export features (PDF/CSV)  
- Notification systems  

---

## 10. Static + Dynamic Correlation

Combine runtime testing with **static artifact discovery**.

### Example

```js
template: "Hello {{username}}"
```

### Look for

- JavaScript files referencing template variables  
- API responses with template placeholders  
- Leaked source maps  

---

## 11. Blind SSTI Indicators

### Signals

- Response timing anomalies  
- Out-of-band interactions (DNS/HTTP callbacks)  
- Server resource spikes  

---

## 12. Framework-Specific Heuristics

Map frameworks to likely engines:

- Flask / Django → Jinja2  
- Spring Boot → Thymeleaf / Freemarker  
- Express → Pug / EJS / Handlebars  
- Laravel → Blade  

---

## References

1. https://portswigger.net/web-security/server-side-template-injection  
2. https://github.com/epinna/tplmap  
3. https://github.com/vladko312/SSTImap  

{% endraw %}