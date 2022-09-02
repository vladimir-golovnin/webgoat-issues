# WebGoat. Voting. JWT signature check bypass. 
JWT signature check may be circumvented for 'Voting' endpoints, using the following JWT header: `{"alg":"none"}`
It is possible to gain rights to perform admin actions, such as resetting votes by changing 'admin' field value to `true` in JWT body. 

## Requirements Violation
The following requirement is violated: 
- Best Security Practices.

## Business Impact
Unauthorised access to the system.

## Steps to Reproduce
1. Do POST request to endpoint `/WebGoat/JWT/votings` with cookie 'access_token' equal to `eyJhbGciOiJub25lIn0.eyJpYXQiOjE2NjI3NDA4MDgsImFkbWluIjoidHJ1ZSIsInVzZXIiOiJUb20ifQ.` and valid session cookie.
2. Receive successful response meaning that you have purged the voting results.  

Expected result: Requests with improper signing algorithm or without a signature are rejected.  
Actual result: JWT token signing algorithm is not restricted. Token without a signature is handled as valid.  

## Mitigation Recommendations
1. Use trustworthy proven framework to process JWT or OAuth. 
2. Restrict JWT signature algorithms. Reject JWT without signatures and improper signature algorithms. 
3. Do not store session specific information in JWT.
4. See https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html

## Additional Information
**Version**: 8.2.2  
**CVSS**: https://www.first.org/cvss/calculator/3.1#CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:H/A:N