# applicationSecurity
## Application Security Technical Challenge

### This repo is for the application security technical challenge given by Interact

### Question 1: 
Identify at least 5 distinct vulnerabilities.
For each vulnerability, provide:
o Vulnerability name
o How you found it
o Impact assessment
o Proof of Concept (PoC) or exploitation steps
o Remediation recommendation

#### Vulnerability 1:
Name: SQL injection (Login page)

How i found it: I just tried SQLi at the login page and i logged in successfully

Impact Assessment: Critical Severity, directly impacts the database of the webapp which leads to critical data leak

Proof of concept:
1. Navigate to the login page
2. type ' or 1=1-- at the username
3. Type anything at the password field and hit enter and you will login successfully and as an admin.

   <img width="1438" alt="image" src="https://github.com/user-attachments/assets/17466fd6-30d0-416b-8a59-068e1e787a40" />
   <img width="1360" alt="image" src="https://github.com/user-attachments/assets/49674817-01ec-4646-81f8-df9d5f88a397" />



Remediation Recommendation: Sanitize input and paramterize the query and for additional protection, implement rate limiting and MFA

#### Vulnerability 2:
Name: Directory Listing Leak

How i Found it: Tried to access http://ec2-34-251-197-70.eu-west-1.compute.amazonaws.com/ftp/

Impact Assessment: Critical Severity, impacts org information leak

Proof of concept (POC):
1. Go to profile page
2. Read the java script and you will notice there is a directory called ftp
3. Try to access it through the url

Recommended Remediation:
1. Implement Authentication and Authorization
2. Hide the ftp directory and restrict its access (Access Control List)

#### Vulnerability 3:
Name: JWT Token Bypass

How I found it: While testing the add item to basket api i saw the jwt token and i thought of decoding it and changing the token to see if the server validates the token or not

Impact Assessment: Critical Severity, the impact includes, privilege escalation, Account takeover and authentication bypass

Proof of Concept:
1. Setup burp suite
2. Add an item to the cart
3. Capture the valid request with the jwt token
4. go to jwt.io and forge your own token
5. modify alg:RS256 to alg:none
6. send the post request to the repeater
7. change the auth token and replace it with the forged one
8. send the request and the request will be successful

<img width="1308" alt="image" src="https://github.com/user-attachments/assets/efbd2c74-01d5-438e-bf35-19e4cdc97cb2" />
<img width="1156" alt="image" src="https://github.com/user-attachments/assets/21362548-5644-4a1f-be03-0ba68b7eda32" />




Remediation Recommendation:
1. Validate the tokens signature
2. never allow alg:none

#### Vulnerability 4:
Name: Lack of rate limiting

How I found it: I tried to bruteforce the login page

Impact of assessment: High, usernames and passwords can be bruteforced, system can experience DDOS attacks

Proof of concept:
1. Write a simple bruteforce script to test the rate limit
2. Execute the script
3. Watch for headers response timing to see if there is rate limit or throttling or not or simply watch how many requests your script can send before you get an error response

<img width="539" alt="image" src="https://github.com/user-attachments/assets/19fad5da-1fd4-48b2-b586-e09d7dd114cb" />




Remediation recommendation:
1. Apply rate limiting and throttling
2. Use complicated passwords and MFA or even passkey logins

#### Vulnerability 5:
Name:Insecure password reset

How i found it: Tried resetting the password

Impact of assessment: High, security question can be bypassed easily using social engineering methods, will lead to account takeover

Proof of concept:
1. in the login page click Forgot Password

Remediation recommendations:
1. Apply rate limiting on the endpoint
2. Replace the security question with OTP or email verification or passkey

### Part 2: Secure Code Review (Static Analysis)
1. ```SECRET_KEY = 'my$ecretKey' ``` Saving the secret directly in the code, must use environment in python os.environ to inject the secrets, Risk Level: High
2. ```username = request.form['username'] , password = request.form['password']``` not using paramterized values, this will lead to hackers directly executing querries (not validating input) Risk Level: Medium
3. ```query = f "SELECT * FROM users WHERE username='{username}'; AND password='{password}'"``` This is not paramterized, it allows the user to input any value they want into the query like for example to get the admin credentials from the db and the right way to do it is to put "?" which is the placeholder so that the value will be treated as an input and not as part of the query. Risk Level: High
4. ```return {'token' = token}, return {error: Invalid credentials}``` This line forces auth using tokens and the token field is not using environment variables instead. Risk Level: High.
5. ```return {error: Token missing},``` Error message is too blunt and straightforward, error message should be more generic like "invalid login" to avoid enumeration

## Part 3: Threat Modeling Exercise
A startup is launching a fintech web app that connects to banking APIs and stores PII and transaction history.
You’re part of the security review.

* Top 5 key assets
  1. Users Personal identifiable information
  2. Backend Operations such as transactions, money movements
  3. Transaction History
  4. banking APIs
  5. Frontend and user journey
 
  * Potential Threat Actors and Threats
    1. Threat Actor: Internal/External , PII leak.
    2. Threat Actor: Internal, Backend business logic flaws that involve money movement logic and charges.
    3. Threat Actor: Internal/External, Leak of transaction history records.
    4. Threat Actor: External, API abuse that will lead to ddos to requests manipulations.
    5. Threat Actor: Internal/External(Bugs/exploits), Frontend spiels leaking PII or leaking any info that might lead to an exploit or for some information to be stolen.
    6. Threat Actor: Internal, Website error messages being too straightforward.
    7. Threat Actor: Internal, Login API abuse.
    8. Threat Actor: Internal, Lack of Authentication and Authorization.
   
    * DFD (Data Flow Diagram)

<img width="665" alt="image" src="https://github.com/user-attachments/assets/d0d4e8da-08f2-430f-bc4f-cc767ff4095c" />

* Top 3 Risks
  1. Login Api abuse
 
     Security Controls: use HTTPS and http security headers, apply rate limiting and generic error message upon failed logins + OTP and enforce complicated password combinations

  2. Connecting to Banking API Abuse

     Security Controls: HTTPS and http security headers and OAuth 2.0 and Rate Limiting

  3. PII Leak
 
     Security Controls: User input validation and sanitation, Encryption and hashing, Masking of debit/credit card numbers, proper network segmentation for backend operations




