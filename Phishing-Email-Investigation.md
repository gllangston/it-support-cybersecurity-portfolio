## Phishing Email Investigation – 

### Scenario

A user reported receiving a suspicious email and wanted to confirm whether their system had been compromised. The user stated they did not open or interact with the email. The reported timeframe was **February 6, 2024 between 18:30 and 18:40**.

---

### Objective

Determine whether the user interacted with the email, identify accessed URLs, and assess if any malicious activity occurred.


---

### Investigation Process

#### 1. Timeframe Filtering

Filtered logs to:

* **Feb 6, 2024**
* **18:30 – 19:00**

---

#### 2. Focus on Web Traffic

Applied filters:

* `data.url exists`
* `rule.description: Squid URI Connection Established`

---

#### 3. Google Drive Activity

Search used:

```
data.url : *drive.google*
```

**Finding:**

* `drive.google.com:443`

---

#### 4. Attachment Interaction

Search used:

```
data.url : *attachment*
```

**Finding:**

* `mail-attachment.googleusercontent.com:443`

---

#### 5. Malicious URL Identification

Search used:

```
NOT data.url : *google*
```

**Finding:**

* `www.getrekt.systems:443`

---

### Analysis

* The user **did interact with the email**, despite stating otherwise
* Attachment access confirms user interaction
* Suspicious domain indicates potential malicious activity

---

### Conclusion

The investigation confirmed:

* Email interaction occurred
* Attachment was accessed
* A connection to a malicious domain was made

This indicates a likely phishing-related compromise.

---

### Remediation

* Isolate system
* Run malware scan
* Block malicious domain
* Reset credentials
* Review environment for spread

---

### Key Takeaways

* Always verify user claims with logs
* Filtering is critical in SIEM investigations
* Identifying abnormal domains is key to detection

---
