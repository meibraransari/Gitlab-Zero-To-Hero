## 🔐 1. The Core Difference: What’s Being Authenticated

| Method    | What You Use to Authenticate                    | Where It Lives                                                           | How It’s Protected                                                               |
| --------- | ----------------------------------------------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------- |
| **HTTPS** | Username + password (or personal access token)  | On your local system, cached by Git or stored in your credential manager | Encrypted but **still a reusable secret** — if someone gets it, they can log in  |
| **SSH**   | A pair of cryptographic keys (private + public) | Private key stays on your machine; public key is added to GitLab         | Authentication happens via **cryptographic challenge** — no reusable secret sent |

So, even if you save HTTPS credentials, Git still needs to **send** them (encrypted via TLS) with each request.
SSH never sends your private key — it just proves you have it through cryptography.
That’s a big security advantage.

---

## ⚙️ 2. Credential Storage and Risk

### **HTTPS (with saved credentials)**

* Stored in your OS credential manager or a plain text file (depending on configuration).
* If someone gains access to your computer, they could extract and reuse your token/password.
* Tokens can expire, forcing you to reauthenticate.

### **SSH**

* The private key is protected by file permissions and optionally a passphrase.
* If you lose it or it’s compromised, you can just remove the corresponding public key from GitLab.
* No plaintext password or token reuse.

---

## 🤖 3. Automation & CI/CD Scenarios

In automation (like GitLab runners, build servers, or scripts):

* SSH keys are **easier to manage** — you can use **deploy keys** scoped to a single repo.
* HTTPS requires storing a **personal access token**, which is more sensitive and often has broader permissions.

SSH keys = least privilege, safer automation.

---

## 🌍 4. Real-World Perspective

| Situation                        | HTTPS                             | SSH                               |
| -------------------------------- | --------------------------------- | --------------------------------- |
| Occasional contributor           | ✅ Easier to start                 |                                   |
| Frequent developer               | ⚠️ Caching helps, but less secure | ✅ Best choice                     |
| CI/CD or server automation       | ⚠️ Harder to manage tokens        | ✅ Secure + scriptable             |
| Behind strict corporate firewall | ✅ Works via port 443              | ⚠️ SSH (port 22) might be blocked |

---

## 🧭 5. TL;DR — Why Many Teams Prefer SSH

* **No password or token reuse**
* **Secure key-based authentication (never transmits secrets)**
* **Seamless workflow** after one-time setup
* **Great for automation and teams**

So, HTTPS with saved credentials is convenient and secure *enough* for casual use,
but SSH gives you **stronger, scalable, and more maintainable security** — especially in professional or automated setups.


---

### 🔸 **1. HTTPS Authentication Flow**

```
┌────────────────────┐
│   Your Computer    │
│--------------------│
│  git push/pull     │
│  ↓                 │
│  Send username +   │──────► GitLab Server
│  password/token    │        (over HTTPS)
│                    │◄────── Auth OK / Denied
└────────────────────┘

🔒 Transport Security: Encrypted by TLS  
⚠️ Secret Sent Each Time: Your token or password is transmitted (securely, but reusable).  
📦 Stored Locally: Can be cached in credential manager.
```

---

### 🔹 **2. SSH Authentication Flow**

```
┌────────────────────┐
│   Your Computer    │
│--------------------│
│  git push/pull     │
│  ↓                 │
│  Private Key (local│
│  only proves identity)│
│                     │
│  Cryptographic      │──────► GitLab Server
│  challenge/response │        (SSH port 22)
│                     │◄────── Auth OK / Denied
└────────────────────┘

🔐 Authentication: No password or key sent  
🧩 GitLab only stores your public key  
⚙️ If private key is lost/compromised → just remove that key in GitLab
```

---

### 🧭 **Key Takeaways**

| Aspect                                  | HTTPS                         | SSH                               |
| --------------------------------------- | ----------------------------- | --------------------------------- |
| **Secret Sent Over Network**            | ✅ (token/password, encrypted) | ❌ (never sent)                    |
| **Stored on Local Machine**             | Credential (token/password)   | Private key                       |
| **Easy to Automate**                    | Needs token management        | Uses deploy keys                  |
| **Security if Local Machine Is Stolen** | Token can be reused           | Private key protected + revocable |
| **Setup Complexity**                    | Easier at first               | One-time SSH key setup            |

---

So in short:
👉 **HTTPS = convenient but reusable secret.**
👉 **SSH = cryptographically proven identity — nothing secret ever leaves your machine.**

