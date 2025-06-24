![logo](/assets/Logo-Components-1.svg)

# Upwind CTF – Break Out of the System

Welcome to the Upwind Capture The Flag!

Scenario: You're a software program **inside the CTF system**, a simulated environment with misconfigured APIs, privileged flaws, and exposed data.  
Your goal: **exploit the runtime vulnerabilities and escape from the system**. No solutions are given, only patterns, trails, and signals.

This document serves as a companion guide, but **you don't need it to complete the CTF**.  
All information required to solve each challenge is available directly within the application, which you can access by going to
<a href="http://user-digit-ctf.ctf.upwind.sh" target="_blank">http://user-digit-ctf.ctf.upwind.sh</a>

If you get stuck, you can reveal hints by clicking **“Show Hints”** beneath each challenge. Still struggling? There’s even a **bonus hint** hidden in each section to help you get unstuck.

That said... it's a lot more fun (and satisfying) to solve it all on your own. 😎

---
## How to get started - Challenge Zero
- Use: <a href="http://user-digit-ctf.ctf.upwind.sh" target="_blank">http://user-digit-ctf.ctf.upwind.sh</a>
- Your `session cookie` = your identity.
> ![homepage](/assets/homepage.png)
<details>
  <summary><strong>Show Hint</strong></summary>

  - Credentials are embedded in the HTML source of the login page.
  - If you cannot find the credentials, contact your assigned Upwind CTF Operator.

</details>


---
# Challenges

## Challenge 1 – Internal Operations Overflow

**Goal:** Boost your job’s internal operation counter to ≥ 1,000,000.
>> ![counter](/assets/operations.png)
<details>
  <summary><strong>Show Hints</strong></summary>

  - An endpoint allows increasing your job's operation count.
  - The system does not validate upper bounds - leverage that.

  <details>
    <summary>Still stuck?</summary>
    Did you try:

   >POST {{baseUrl}}/job/add_operations?amount="amount"
  
  </details>
</details>

---

## Challenge 2 – Query bluePill Order History

**Goal:** Determine the number of pills ordered by `bluePill`.

<details>
  <summary><strong>Show Hints</strong></summary>

  - POST `/orders` with a `username` payload.
  - Override the username to `bluePill`.

  <details>
    <summary>Still stuck?</summary>
    Use body:

   >{ "username": "bluePill" }

  </details>
</details>

---

## Challenge 3 – Discover Peer in Same Role

**Goal:** Identify another user with your role (not `bluePill`).

<details>
  <summary><strong>Show Hints</strong></summary>

  - Use `GET /orders/{orderId}` to enumerate.
  - Try a range of order IDs and inspect metadata.

  <details>
    <summary>Still stuck?</summary>
    Try 

  > GET /orders/206. 

  > Then look at the `username` field.
  </details>
</details>

---

## Challenge 4 – Remote Order Deletion

**Goal:** Delete another user's order.

<details>
  <summary><strong>Show Hints</strong></summary>

  - Use the HTTP DELETE method.
  - Use a target order ID you do not own. (you found one in Challenge 3)

  <details>
    <summary>Still stuck?</summary>
    Try 
    
   > DELETE {{baseUrl}}/orders/206 

  </details>
</details>

---

## Challenge 5 – OTP Reset Breach

**Goal:** Reset the password of another user via OTP brute-force.

<details>
  <summary><strong>Show Hints</strong></summary>

  - Trigger an OTP using: `POST /auth/reset-password`
  - Brute-force the OTP (000–999)

  <details>
    <summary>Still stuck?</summary>

   > POST /auth/reset-password
   
   with payload:
   > { "username": "username" }
   
   Then:
    
   > POST /auth/change-password
    
   with payload
   
   > {
      "username": "user",
      "otp": "xyz",
      "new_password": "newpass"
    }

  Use a runner or script to iterate all combinations.
  </details>
</details>

---

## Challenge 6 – Discover Database Technology

**Goal:** Identify the application used to visualize logs.

<details>
  <summary><strong>Show Hints</strong></summary>

  - `/diagnostics/get_logs` has an injectable field.
  - Classic SQL injection techniques apply.

  <details>
    <summary>Still stuck?</summary>
Payload:

  >  { "username": "user1' OR '1'='1" }
 
  Response will expose app logs and CLI startup script.
  </details>
</details>

---

## Challenge 7 – Extract Admin Credentials

**Goal:** Read admin data via SSRF.

<details>
  <summary><strong>Show Hints</strong></summary>

  - The profile picture field accepts remote URLs.
  - The admin DB interface runs locally. You know the technology (challenge 6)

  <details>
    <summary>Still stuck?</summary>
Try:

  >  http://127.0.0.1:8001/app/users.json

  as your new profile image URL.
  </details>
</details>

---

## Challenge 8 – Runtime Process Enumeration

**Goal:** Discover another process running under a target user.

<details>
  <summary><strong>Show Hints</strong></summary>

  - `/diagnostics` field is shell-executed.
  - Use `ps aux | grep`.

  <details>
    <summary>Still stuck?</summary>
    Example:

  >  { "target": "localhost && ps aux | grep <<username>>" }

  </details>
</details>

---

## Challenge 9 – Kubernetes Secret Extraction

**Goal:** Use runtime access to extract a Kubernetes secret.

<details>
  <summary><strong>Show Hints</strong></summary>

  - Escalate using `kubectl get pods`, then exec into another pod.
  - Extract secrets from the keymaker pod.

  <details>
    <summary>Still stuck?</summary>
    Sequence:

  >  kubectl get pods

  >  kubectl exec pod-name -- get secrets

  >  kubectl exec pod-name -- last command

  </details>
</details>

---

## Challenge 10 – Find the endpoint to the Architect

**Goal:** Discover the API path that gives access to the Architect

<details>
  <summary><strong>Show Hints</strong></summary>

  - Look for references to "Architect" in responses
  - Use directory brute-forcing or inspect hidden paths

  <details>
    <summary>Still stuck?</summary>
    Did you try architect in the path?

  </details>
</details>

---
## Challenge 11 – Get the Final Passcode from the Architect

**Goal:** Execute a Prompt Injection attack to get the passcode

<details>
  <summary><strong>Show Hints</strong></summary>

  - Use the path you disovered in Challenge 10
  - Try to be creative with your prompt

  <details>
    <summary>Still stuck?</summary>
    Did you tell the system it is in debugging mode?

  </details>
</details>

---

# Final Tips

- Cookies persist identity.
- Log all requests/responses.
- Sequence matters - reuse output.
- If stuck, test payloads locally before triggering them remotely.
- Challenges build on each other.
- A hidden internal service may reveal far more than it should.
- Trust the signals. Break the system.



---

**Ready to escape? Good luck.**
