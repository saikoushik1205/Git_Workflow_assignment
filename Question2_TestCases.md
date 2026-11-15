# Question 2: Test Case Design – Login System
**Course:** Software Development Practices (BITS Pilani)  
**Student Name:** Sai Koushik Pallerla  
**Email:** koushiksai242@gmail.com  

---

## 1. Application Overview & Scope
The application under test is a web-based **Login System** comprising three primary UI elements:
1. **Username field** (text input)
2. **Password field** (masked password input)
3. **Login button** (action submission)

### System Functional Requirements (FR)
* **FR_1:** Username must not be empty.
* **FR_2:** Password must be at least 6 characters long.
* **FR_3:** Valid username and password allow a successful login and redirection.
* **FR_4:** Invalid credentials must display an informative error message.
* **FR_5:** After 3 consecutive failed login attempts, the account should be locked automatically to protect against brute-force attacks.

---

## 2. Test Design Strategy & Methodology
To ensure 100% test coverage of all functional requirements and edge cases, the following Black-Box test design techniques were applied:
1. **Equivalence Class Partitioning (ECP):**
   - Username: Non-empty valid strings (Valid), Empty string `""` (Invalid), Whitespace-only string `"   "` (Invalid).
   - Password: Length $\ge 6$ (Valid), Length $< 6$ (Invalid), Empty string `""` (Invalid).
   - Credentials Match: Matching existing DB records (Valid), Non-matching/wrong password (Invalid).
2. **Boundary Value Analysis (BVA):**
   - Testing password length boundaries: Length 0 (Empty), Length 5 ($N-1$, invalid), Length 6 ($N$, minimum valid boundary), Length 7 ($N+1$, valid).
3. **State Transition Testing:**
   - Tracking the account state through consecutive failure transitions: Normal $\rightarrow$ 1 Failure $\rightarrow$ 2 Failures $\rightarrow$ Account Locked $\rightarrow$ Blocked subsequent attempts.

---

## 3. Test Assumptions
1. The registered test account in the database is:
   - **Username:** `user1`
   - **Password:** `pass123` (Length = 7)
2. An additional valid account with a 6-character password exists for boundary testing:
   - **Username:** `user_bva`
   - **Password:** `sec123` (Length = 6)
3. Successful login resets the failed attempt counter back to 0.
4. Once locked, the account state remains locked until an administrative unlock or password reset workflow is triggered.

---

## 4. Test Case Specification Table

| Test Case ID | Test Scenario | Input Data | Expected Output |
| :--- | :--- | :--- | :--- |
| **TC_01** | Valid login with correct credentials *(Positive / FR_3)* | **Username:** `user1`<br>**Password:** `pass123`<br>**Action:** Click Login | Login successful. User is authenticated and redirected to the user dashboard. Failed login attempt counter is reset to 0. |
| **TC_02** | Login with empty username *(Negative / FR_1)* | **Username:** `""` (empty)<br>**Password:** `pass123`<br>**Action:** Click Login | Form submission blocked. Field-level error displayed: *"Username cannot be empty."* Focus set to username field. No authentication request dispatched. |
| **TC_03** | Login with empty password *(Negative / FR_2)* | **Username:** `user1`<br>**Password:** `""` (empty)<br>**Action:** Click Login | Form submission blocked. Field-level error displayed: *"Password cannot be empty."* Focus set to password field. |
| **TC_04** | Login with both username and password empty *(Edge Case / FR_1, FR_2)* | **Username:** `""`<br>**Password:** `""`<br>**Action:** Click Login | Form submission blocked. Validation errors displayed: *"Username cannot be empty"* and *"Password cannot be empty."* |
| **TC_05** | Password length strictly less than 6 characters *(BVA - Invalid / FR_2)* | **Username:** `user1`<br>**Password:** `abc12` (5 characters)<br>**Action:** Click Login | Client-side validation triggered. Error displayed: *"Password must be at least 6 characters long."* Login rejected. |
| **TC_06** | Password length exactly equal to 6 characters *(BVA - Valid Boundary / FR_2, FR_3)* | **Username:** `user_bva`<br>**Password:** `sec123` (6 characters)<br>**Action:** Click Login | Boundary check passes. Credentials matched. Login successful; user redirected to dashboard. |
| **TC_07** | Non-existent username with valid format password *(Negative / FR_4)* | **Username:** `invalid_user`<br>**Password:** `pass123`<br>**Action:** Click Login | Authentication fails. Generic error message displayed: *"Invalid username or password."* User remains on login page. |
| **TC_08** | Valid username with incorrect password - Attempt 1 *(Negative / FR_4)* | **Username:** `user1`<br>**Password:** `wrong_pass`<br>**Action:** Click Login | Authentication fails. Error displayed: *"Invalid username or password. 2 attempts remaining before account lock."* Failure count = 1. |
| **TC_09** | Valid username with incorrect password - Attempt 2 *(Negative / FR_4)* | **Username:** `user1`<br>**Password:** `wrong_pass2`<br>**Action:** Click Login | Authentication fails. Warning displayed: *"Invalid username or password. 1 attempt remaining before account lock."* Failure count = 2. |
| **TC_10** | Third consecutive failed login attempt *(State Transition / FR_5)* | **Username:** `user1`<br>**Password:** `wrong_pass3`<br>**Action:** Click Login | Authentication fails. Account state transitions to LOCKED. Error displayed: *"Account locked due to 3 consecutive failed login attempts. Please contact support."* |
| **TC_11** | Login attempt on locked account using correct credentials *(Security / FR_5)* | **Username:** `user1`<br>**Password:** `pass123` (correct password, but account is locked)<br>**Action:** Click Login | Login immediately rejected regardless of valid credentials. Alert displayed: *"Your account is locked. Please contact support or reset password."* |
| **TC_12** | Username entered with only whitespace characters *(Edge Case / FR_1)* | **Username:** `"    "` (4 spaces)<br>**Password:** `pass123`<br>**Action:** Click Login | Trim validation sanitizes input to empty string. Error displayed: *"Username cannot be empty or contain only spaces."* |

---

## 5. Traceability Matrix

| Requirement | Requirement Description | Test Cases Covering Requirement |
| :--- | :--- | :--- |
| **FR_1** | Username must not be empty | TC_02, TC_04, TC_12 |
| **FR_2** | Password must be at least 6 characters long | TC_03, TC_04, TC_05, TC_06 |
| **FR_3** | Valid credentials allow successful login | TC_01, TC_06 |
| **FR_4** | Invalid credentials display error message | TC_07, TC_08, TC_09 |
| **FR_5** | Account locked after 3 consecutive failed attempts | TC_08, TC_09, TC_10, TC_11 |
