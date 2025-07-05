# applicationSecurity
## Application Security Technical Challenge

### This repo is for the application security technical challenge given by Interact

### Question 1: (Answering this question will be straight from my work experience throughout my career)
Identify at least 5 distinct vulnerabilities.
For each vulnerability, provide:
o Vulnerability name
o How you found it
o Impact assessment
o Proof of Concept (PoC) or exploitation steps
o Remediation recommendation

#### Vulnerability 1: (at maya)
Name: Face Auth Bypass
How i found it: Tasked to pentest it using Deepfake or images with different brightness settings or live detection with different brightness settings as well
Impact Assessment: Critical Severity, impacts all ios users of our digital banking application that do multiple operations such as send money, apply for a loan, view virtual credit card details and password reset.
Proof of concept:
1. Connect your iphone to your macbook (for deubugging purposes)
2. Record a video on your macbook cam with deepface live
3. Run the app on your phone
4. Point the camera towards the video recorded on your macbook
5. Adjust the brightness on the video to fool the face auth
6. Bypass complete and you can now reset the account's password

Remediation Recommendation: Add OTP on top of face auth (temporarily) then Connect with Tencent to resolve the issue and fix liveness detection and then test again to confirm that the face auth cannot be bypassed again

#### Vulnerability 2: (At TORO)
Name: Stored XSS
How i Found it: I was pentesting petshed.com (legacy website)
Impact Assessment: Critical Severity, impacts the company's reputation
Proof of concept (POC):
Go to a product page and post in the comments section and post the following script <script>Test Alert()</script>
Remediation Recommendation: Apply HTTP Security headers and aws WAF rules

#### Vulnerability 3: (At Maya)
Name: Captcha Bypass
How I found it: While testing the reset password flow I found out that i can bypass captcha by deleting the csrf token sent as a cookie and teh cpatcha was completely bypassed
Impact Assessment: High Severity, Impacts all users on all devices, bypasses rate limiting for OTP for all password reset attempts
Proof of Concept:
1. Connect your phone to your macbook and start adb shell and setup your phone proxy to use burp and intercept the traffic
2. Attempt to reset the password and solve the captcha once to capture a request
3. Intercept the request and delete the csrf token
4. bruteforce the OTP
5. reset the password successfully

Remediation Recommendation:
1. Validate the tokens on the server side or backend logic
2. Rate limiting for OTP should be set separately for OTP (Dont reply on captcha to rate limit the otp requests)

#### Vulnerability 4: (At Maya)
Name: Default login credentials on collibra.paymaya.com
How I found it: I was testing rate limiting on the login page by bruteforcing it using hydra and i was able to get the credentials and they were the default credentials and i was able to run bruteforce attack endlessly.
Impact of assessment: Critical Severity, exposes sensitive information.
Proof of concept:
1. Go to collibra.paymaya.com
2. run Hydra bruteforce attack on the domain using the unixusers.txt unixpass.txt wordlists available on kali linux.

Remediation recommendation:
1. Change the credentials to be more complicated credentials.
2. Set rate limiting for login attempts
3. Set cooldown after the limit has been reached

#### Vulnerability 5: (At Maya)
Name:Tax fees bypass (business logic flaw)
How i found it: Testing the credit and lending function on our app, this exploit does not require interception of requests
Impact of assessment: Medium severity, causes financial losses to the organization.
Proof of concept:
1. Login to your banking app
2. go to credit tab and borrow 0.5 php
3. notice the credit document the app sends via email is free of charges
4. keep borrowing and you'd notice that there is no rate limiting as to how many times you can request the endpoint.

Remediation recommendations:
1. Change the minimum borrowing to 1php insted of 0.1php and apply tax fees
2. set rate limiting for the borrowing function to avoid bot automation

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




