# Threats Created by Introducing Intelligent Agents to the WWW

AI agents impersonating humans present critical dangers to both individuals and organizational networks, fundamentally changing the threat landscape.

Threats Against Humans

* **Synthetic Identity Fraud & Deepfakes:** Cybercriminals use deepfakes and cloned voices in Business Email Compromise (BEC) and call center scams to bypass biometric and verbal authentication, tricking victims into transferring large sums of money.  
* **Hyper-Personalized Phishing:** AI agents industrialize tailored spear-phishing campaigns, quickly gathering personal information to create highly believable, contextual lures that manipulate human emotions and trust.  
* **Fake Interview & Job Scams:** Attackers deploy synthetic identities and real-time deepfake videos to pass remote job interviews, enabling them to steal salaries, exfiltrate internal data, or act as insider threats.  
* **Reputational Extortion:** Bad actors use fabricated, hyper-realistic media to blackmail executives or employees, threatening to publicly release compromising deepfake content.

Threats Against Websites

* **Malicious "Agent Poisoning":** Cybercriminals conceal malicious instructions inside webpage code or documents. When an autonomous AI agent browses the site to complete a task, it interprets these concealed text elements as commands, causing it to perform unintended or destructive actions.  
* **Web Scraping & Shadow AI:** Unapproved or autonomous AI bots silently scrape websites, internal portals, and wikis to build massive, proprietary datasets without proper authorization.  
* **Tool Misuse & API Exploitation:** Compromised AI agents, or fake AI agents posing as legitimate automated systems, can manipulate a website's exposed integrations or APIs to force unauthorized access.  
* **Session & Cookie Hijacking:** Attackers use AI to target browser cookies to bypass multi-factor authentication (MFA). This allows attackers to impersonate a legitimate user or administrative session to compromise website databases or hosting accounts.

Different Principal types result in different approaches: Personhood creds from the POV of the human and Private Access Tokens from the POV of the enterprise web site. To understand how Private Access Tokens (PATs) relate to \*\*personhood credentials\*\*, you have to look at the difference between \*\*what\*\* is being proven (the credential) and \*\*how\*\* it is shared (the token). In short: \*\*Personhood is the "Claim," and a Private Access Token is the "Privacy-Preserving Delivery Vehicle. Here is the detailed breakdown of that relationship.

### 1\. The Concept: Personhood Credentials

A "personhood credential" is a digital assertion that says: \*"The entity holding this credential is a unique, living human being and not a bot or an AI."\*

Traditional mechanisms for validating human presence online have proven largely inefficient and intrusive:

* **CAPTCHA Systems:** These require users to solve manual puzzles, creating significant friction and negative user experiences.  
* **SMS-Based Attestation:** By linking personhood to mobile numbers, this method compromises user privacy and exposes personal identity.  
* **Identity Document Verification:** While confirming humanity, this approach results in massive data over-sharing, including sensitive PII like names and dates of birth.

### 2\. The Solution: Private Access Tokens (PATs)

A PAT is not the "credential" itself; rather, it is a \*\*blinded cryptographic proof\*\* of that credential.

If a website requires a personhood credential to prevent bot attacks, instead of asking you for your ID or making you click on pictures of traffic lights, it asks for a PAT.

#### How they relate in a workflow:

1. The Attestation (Establishing Personhood):\*\* Your device (iPhone, Android, Windows PC) already knows you are likely a human because you’ve passed biometric checks (FaceID/TouchID), have a verified account, or possess a secure hardware chip (TPM). This is your \*\*Personhood Credential\*\*.  
2. The Blinded Request:\*\* When you visit a website, your browser asks the OS: \*"Can you give me a token that proves I'm a human for this site?"\*  
3. The Issuance:\*\* The OS (the Issuer) signs a token. Crucially, it uses \*\*Blind Signatures\*\*. This means the OS knows it gave a "Human" token to \*someone\*, but it doesn't know which website that token is being sent to.  
4. The Presentation:\*\* Your browser sends the PAT to the website. The website verifies the cryptographic signature. It now knows you are a human, but it has no idea who you are.

### 3\. Why this matters for "Personhood"

The relationship between PATs and personhood credentials solves the "Privacy-Utility Paradox.

* The Paradox:= To prove I am a human (Utility), I usually have to tell you who I am (Loss of Privacy).  
* The PAT Solution:= I can prove my "humanity" without revealing my "identity."

| Personhood Concept | Traditional Method (ID/SMS) | Private Access Token (PAT) |
| :---- | :---- | :---- |
| **Verification** | Identity-based (Who are you?) | Attribute-based (Are you human?) |
| **Data Leakage** | High (Name, Phone, Email) | Zero (Cryptographic proof only) |
| **Tracking** | Site can track you across sessions | Issuer doesn't know where token is used |
| **User Effort** | High (Upload ID / Solve CAPTCHA) | Zero (Automatic/Invisible) |

### 4\. PATs vs. Decentralized Identity (Worldcoin, etc.)

You may have heard of other "Personhood" projects like Worldcoin (Orb scans) or Gitcoin Passport. It is helpful to distinguish them from PATs:

* Worldcoin/Decentralized IDs: Focus on creating a Global Unique ID. They want to prove that you are one specific human among 8 billion people, often using a blockchain.  
* Private Access Tokens (PATs): Focus on Session-based Verification. They don't care if you are "User \#452"; they only care that the device you are currently using is owned by a human and not a bot farm.

### Summary

If Personhood Credentials are the "Passport" that proves you are a citizen of Earth (a human), then Private Access Tokens are like a "Privacy Screen" placed over that passport. The website can see the official stamp saying "Verified Human," but it cannot see your photo, your name, or your passport number.  
---

## How does Personhood approach Compare to OAuth

If you are looking at this from the perspective of an **Identity Provider (IdP)** like Google, Okta, Microsoft Entra ID, or Apple, the shift toward Private Access Tokens (PATs) represents a fundamental change in your business model: **Moving from "Identity Provisioning" to "Attribute Attestation."**

For a traditional IdP, the goal has always been: *"I can tell you exactly who this user is."* With PATs and personhood credentials, the goal becomes: *"I can prove this user meets a certain criteria (e.g., they are human), without telling you who they are or tracking where they go."*

Here is how an IdP views the relationship between Personhood Credentials and PATs.

### 1\. The Paradigm Shift: Identity → Attestation

In the traditional **OIDC/SAML** world (the "Sign in with Google" model), you are an **Identity Provider**. When a user logs into a third-party site, you send an ID Token that says: *"This is user\_123, their email is john@gmail.com, and they are authenticated."*

In the **PAT/Personhood** world, you move toward being an **Attestation Provider**. You aren't providing an identity; you are vouching for a characteristic.

* **The Claim:** "This entity is a unique human."  
* **The Evidence:** The user has a verified account, a biometric lock on their device, and a hardware-backed key (TPM/Secure Enclave).

### 2\. The Technical Role: The "Issuer" in the Blinded Loop

As an IdP, you act as the **Issuer**. However, unlike traditional tokens where you know exactly which app is requesting the data, PATs use **Blinded Signatures** to protect the user's privacy from *you* (the provider).

**From your perspective as the IdP, the workflow looks like this:**

1. **The Request:** Your client (e.g., a browser on an iPhone or a Chrome window) sends you a request for a token. But because it is "blinded," the request doesn't tell you which website the user is trying to visit.  
2. **The Verification:** You check your internal records. *"Does this device have a verified human associated with it? Is the biometric check passed?"*  
3. **The Signing:** If yes, you cryptographically sign a "blinded" token. You are essentially signing a blank piece of paper that says **"Verified Human."**  
4. **The Hand-off:** You send that signed token back to the user's device. The user then "unblinds" it and presents it to the website.

**Crucially:** As the IdP, you know *that* you issued a personhood credential, but you have no idea *where* it was used. This removes the "tracking" liability from your shoulders.

### 3\. Why this is better for the IdP (The Business Case)

You might ask: *"Why would Google or Okta want to give up the ability to track where users are logging in?"*

* **Reduced Friction/Churn:** CAPTCHAs and multi-step identity verification cause "drop-off." By issuing PATs, you make the internet seamless for your users. They stay within your ecosystem because it's the "path of least resistance."  
* **Liability Reduction (GDPR/CCPA):** Every time you send PII (Personally Identifiable Information) like an email address to a third party, you create a privacy risk and a compliance burden. A PAT contains **zero PII**. You are providing value without transferring risky data.  
* **Ecosystem Lock-in:** If your OS or ID service is the only one trusted by websites to provide "Personhood Tokens," users are more likely to stay with your platform to avoid being blocked by bot-detection software on other sites.

### 4\. Comparison: Identity Token vs. Personhood PAT

From an IdP's engineering perspective, here is how the two differ:

| Feature | Traditional Identifier Token (OIDC) | Personhood Private Access Token (PAT) |
| :---- | :---- | :---- |
| **Your Goal** | Identify the user to a 3rd party. | Attest to a trait (humanity). |
| **Data Shared** | Name, Email, Subject ID. | Cryptographic proof of Human. |
| **Visibility** | You know exactly which app the user is visiting. | The request is blinded; you don't know the destination. |
| **Verification** | App checks your public key to verify identity. | Website checks your public key to verify personhood. |
| **User Experience** | Redirect → Login→ Redirect. | Invisible background exchange (Zero-click). |

### Summary for the IdP using OAuth

As an Identity Provider, you are evolving from a **"Digital Passport Office"** (where you tell the world exactly who someone is) into a **"Trusted Notary"** (where you simply stamp a document saying "Yes, this person is real," without needing to know why they need that stamp or where they are taking it).

