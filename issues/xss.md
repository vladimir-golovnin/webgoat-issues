# WebGoat. Debug code in GoatRouter.js is vulnerable for XSS attack
Client side application code in GoatRouter.js contains debug lines that is vulnerable for XSS attacks.

## Requirements Violation
The following requirement is violated: Best Security Practices.

## Business Impact
- Unauthorised access to user accounts.
- Disclosure of sensitive customer information.
- Denial of service (account).

## Steps to Reproduce
GoatRouter.js contains following debug code:
Line 52: `'test/:param': 'testRoute'`
Line 114:  
```js
    testRoute: function (param) {
        this.lessonController.testHandler(param);
        //this.menuController.updateMenu(name);
    } 
```
This code is vulnerable for XSS attack such as following: 
1. Open this link in web browser [http://www.webgoat.local:8080/WebGoat/start.mvc#test/%3c%73%63%72%69%70%74%3e%61%6c%65%72%74%28%22%58%53%53%20%65%78%70%6c%6f%69%74%21%22%29%3c%2f%73%63%72%69%70%74%3e](http://www.webgoat.local:8080/WebGoat/start.mvc#test/%3c%73%63%72%69%70%74%3e%61%6c%65%72%74%28%22%58%53%53%20%65%78%70%6c%6f%69%74%21%22%29%3c%2f%73%63%72%69%70%74%3e)
2. A dialog window with phrase 'XSS exploit!' will be opened.  

## Mitigation Recommendations
1. Remove debug code from production environment. 
2. Validate, filter and encode untrusted input of the application, especially before showing it on a page. See: [OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)

## Additional Information
**Version**: 8.2.2  
**CVSS**: https://www.first.org/cvss/calculator/3.1#CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:L/I:N/A:N