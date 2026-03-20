# Threat Model for `.well-known` Endpoints

It seems like a good time to review the use of well-known endpoints given the level of nation-state attacks against web sites. These endpoints are powerful tools for automation and standardization. But their impact on security requires due diligence to ensure they are not exploited as exfiltration or entry points by malicious actors.

Author: Tom Jones 2025-12-12

## Assets at Risk

- **Authentication flows** (OAuth, OIDC discovery, STS endpoints).  
- **User privacy** (URLs, identifiers, browsing context).  
- **Trust relationships** between clients, servers, and federated identity providers.  
- **System integrity** (redirect chains, metadata exposure).

## Threat Actors

- **External attackers** scanning predictable paths.  
- **Malicious intermediaries** intercepting traffic (MITM).  
- **Rogue service providers** misusing redirects.  
- **Automated crawlers/bots** harvesting URLs for tracking.  
- **Data Brokers** using redirects to track user activity

## Threat Categories

Note that the standards do not rely on TLS enforcement as a mitigation. That means that all of the security must be supplied at the application layer.

### 1\. **Predictable Endpoint Exposure**

- **Attack Vector**: Scanning `/.well-known/` paths including constant AI monitoring.  
- **Impact**: Metadata leakage, discovery of hidden services and other reconnaissance.  
- **Mitigation**: Signed message authentication, endpoint minimization, TLS enforcement.

### 2\. **Redirect Exploitation**

- **Attack Vector**: Open redirects or misconfigured endpoints.  
- **Impact**:  
  - **Phishing**: Users redirected to malicious domains.  
  - **Federation Hijack**: Redirects to rogue IdPs compromise authentication flows.  
  - **Leakage of User URL**: Redirect chains expose full user request URLs (including query params, tokens).  
  - **Evaluation:** Of redirection vulnerabilities will be hard to detect.  
- **Mitigation**:  
  - Whitelist redirect targets.  
  - Strip sensitive query parameters before redirection.  
  - Use signed metadata to validate redirect destinations.  
  - Prohibit redirects  
  - Signing all messages can validate a download, but assumes the trust domain is already known so that the signature can be validated.

### 3\. **Man-in-the-Middle (MITM) Attacks**

- **Attack Vector**: Interception of traffic to `/.well-known/` endpoints, especially during redirects.  
- **Impact**:  
  - **Token Theft**: Intercepted tokens or credentials.  
  - **Metadata Manipulation**: Altered discovery documents mislead clients.  
  - **User Tracking**: MITM logs user URLs and redirect chains.  
- **Mitigation**:  
  - Enforce HTTPS/TLS with HSTS.  
  - Validate certificates and signatures on metadata.  
  - Pin trusted endpoints to prevent rogue intermediaries.

### 4\. **Namespace Collisions**

- **Attack Vector**: Multiple apps register overlapping URIs.  
- **Impact**: Confusion, misrouting, unintended exposure.  
- **Mitigation**: IANA compliance, governance registry.

### 5\. **Hidden Capabilities**

- **Attack Vector**: Exposure of undocumented endpoints.  
- **Impact**: Reconnaissance, exploitation of unfinished features.  
- **Mitigation**: Endpoint audits, disable experimental URIs.

### 6\. **Web Browsing Auto-Fetch**

- **Attack Vector**: Browsers auto-query well-known URIs.  
- **Impact**: Fingerprinting, URL leakage, manipulation of client behavior.  
- **Mitigation**: Limit auto-fetch, sanitize responses, avoid sensitive data exposure.

### 7\. **Misconfiguration of the .well-known file data**

- **Attack Vector**: Data is entered incorrectly or changed after deployment.  
  - Incorrect issuer URLs  
  - Wrong JWKS locations  
  - Stale or mismatched keys  
  - Missing required fields  
  - Incorrect MIME types  
- **Impact**:  Clients may fail open or fail closed, depending on implementation.  
- **Mitigation**: Testing of deployed systems immediately after release and from time-to-time

### 8\. **Caching & Staleness Risks**

- **Attack Vector**: Metadata is often cached aggressively.
  - Clients using outdated issuer metadata
  - Slow propagation of key revocation
  - Inconsistent behavior across clients
- **Impact**: Trust decisions based on stale information.
- **Mitigation**: Force reloads before impactful trust decisions are made.

## Threat Model Table

| Threat Area | Attack Vector | Impact | Mitigation |
| :---- | :---- | :---- | :---- |
| Predictable endpoints | Scanning `/.well-known/` | Metadata leakage | Authentication, TLS, endpoint minimization |
| Redirect exploitation | Open/misconfigured redirects | Phishing, federation hijack, URL leakage | Whitelist domains, strip query params, signed metadata |
| MITM attacks | Intercepted traffic, altered redirects | Token theft, metadata manipulation, tracking | Enforce HTTPS/HSTS, cert validation, endpoint pinning |
| Namespace collisions | Overlapping registrations | Confusion, misrouting | IANA compliance, governance registry |
| Hidden capabilities | Undocumented endpoints | Reconnaissance, exploitation | Endpoint audits, disable experimental URIs |
| Auto-fetch exploitation | Browser queries | Fingerprinting, URL leakage | Limit auto-fetch, sanitize responses |

#### Some Security Best Practices

To mitigate risks associated with well-known endpoints, follow these guidelines:

1. Restrict Access: Use Web Application Firewalls (WAFs) or server configurations (.htaccess, Nginx rules) to restrict access to these paths to only necessary origins or methods.  
2. Monitor Logs: Monitor access logs for the /.well-known/ directory for unusual traffic patterns, failed requests, or scans, which may indicate reconnaissance activity.  
3. Implement security.txt: Ensure a current security.txt file is in place to responsibly manage vulnerability disclosure.  
4. Validate Configurations: Regularly audit the configuration of security-critical endpoints like those for OIDC/OAuth to ensure they adhere to current standards and best practices.  
5. Minimize Footprint: Only implement the specific well-known URIs your application explicitly requires. If you don't use WebFinger or OpenID Connect, ensure those paths are not active.  
6. Minimize content: Only include necessary data and evaluate the risk associated with every data element exposed by the end point.  
7. Don’t mix security trust boundaries by having an endpoint serve different purposes.  
8. Update new releases in phases and test immediately on deployment and beyond.

#### RFC 8615 Security Considerations

* Predictable Paths: Because /.well-known/ is standardized, attackers know exactly where to look for sensitive metadata.  
* Exposure Risk: Misconfigured endpoints can leak authentication details, federation metadata, or hidden services.  
* Mitigation: Apply normal web security controls (TLS, authentication, access restrictions) to well-known URIs.

#### Common and Critical Well-Known Endpoints

| Endpoint Path | Purpose | Security Considerations |
| :---- | :---- | :---- |
| /.well-known/security.txt | Defines security policies and contact information for the site's security team \[1\]. | Ensures attackers have a legitimate channel to report vulnerabilities; a missing file makes coordinated disclosure harder \[1\]. |
| /.well-known/change-password | Redirects users to the site's password change page. | Improves user experience, but requires careful configuration of redirects to prevent open redirects or phishing risks. |
| /.well-known/dnt | Specifies the site's compliance status with "Do Not Track" requests. | Primarily a privacy signal, less of a direct attack vector. |
| /.well-known/webfinger | A discovery protocol used primarily in decentralized identity systems (like Mastodon) to find information about users on a server \[3\]. | Vulnerabilities here can lead to user enumeration, privacy leaks, or potential Server-Side Request Forgery (SSRF) if poorly implemented \[3\]. |
| /.well-known/pki-validation/ | Used for Automated Certificate Management Environment (ACME) protocol challenges (e.g., Let's Encrypt) to prove domain control. | Access to this directory must be tightly controlled; compromise allows an attacker to issue valid TLS certificates for your domain. |
| /.well-known/openid-configuration and /.well-known/oauth-authorization-server | Discovery endpoints for OpenID Connect and OAuth 2.0 servers. | Critical: These endpoints expose the configuration details (like key locations and authorization URLs) for identity providers. Misconfigurations or attacks here can lead to authentication bypass, token leakage, or other identity-related attacks. |

## Summary

Adding **redirects** introduces two critical risks:

1. **Leakage of user URLs** (tokens, identifiers, query strings exposed in redirect chains).  
2. **Man-in-the-middle exploitation**, where attackers intercept or alter redirect flows to hijack trust.

Governance must enforce strict redirect policies, TLS everywhere, signed metadata, and URL sanitization to prevent these risks. But governance is not one of the topics covered by most standards or compliance tests. That means that defects in the redirect pipeline might never be evaluated in real-world implementations.

References  
Phil Smart, Review of well-known redirects in OIDC Federation (2025-12) Github [https://github.com/openid/federation/issues/316](https://github.com/openid/federation/issues/316)

M. Nottingham, Well-Known Uniform Resource Identifiers (URIs)  (2019-05)  [RFC 8615 \- Well-Known Uniform Resource Identifiers (URIs)](https://datatracker.ietf.org/doc/html/rfc8615)  
