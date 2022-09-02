# WebGoat. `/WebGoat/xxe/simple` endpoint is open to list server file system
Backend endpoint `/WebGoat/xxe/simple` provides possibility to list contents of the server file system.

## Requirements Violation
The following requirements are violated: 
- [Application Security Requirements v. 4.0] [Req. 11.3.5] Active code (e.g. SSI or PHP code) should be removed from user input.
- Best Security Practices.

## Business Impact
Unauthorised access to the system.

## Steps to Reproduce
1. Login to the WebGoat application.
2. Copy session cookie. 
3. Do POST request to `/WebGoat/xxe/simple` endpoint with obtained session cookie and the following xml body:
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
    <!ENTITY root SYSTEM "file:///" >
]>
<comment><text>&root;</text></comment>
```
4. Open [comments page](http://www.webgoat.local:8080/WebGoat/start.mvc#lesson/XXE.lesson/3) in the browser.
5. Observe contents of the root directory of the server among user comments.

## Mitigation Recommendations
1. Switch off DTD for incoming XML data parsing. 
2. Switch off external entities support for incoming XML data parsing. 
3. Limit reading access rights of the application to directories and files which are vital for the application. 
3. See [OWASP XML External Entity Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)

## Additional Information
**Version**: 8.2.2
**CVSS**: https://www.first.org/cvss/calculator/3.1#CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:N/A:N