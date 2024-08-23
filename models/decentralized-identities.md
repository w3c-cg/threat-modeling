# Introduction

## Status of this document

This document is the live "meta" Threat Model related to Decentralized Identities, focusing on Digital Credentials. 

This outlines the many concerns related to these work areas and the initial principles for addressing user considerations for starting a discussion.

Editor: Simone Onofri (W3C) simone@w3.org

## Scope

The topic of Digital Identities is vast and intricate. Defining the initial scope of the model, which can be expanded when necessary, if we consider the ecosystem of Digital Identities, we find ourselves in the specific case of Decentralized Identities, those identities related to people and in particular those considered high-assurance (e.g., those issued by governments) and in the **Layer 3: "Credentials" and precisely the *Credential-Presentation phase*** - as defined by the [SSI Technology Stack from ToITP](https://trustoverip.org/toip-model/) and [DIF FAQ](https://identity.foundation/faq/).

On the one hand, the need arose within the W3C about the potential adoption of the [Digital Credentials API](https://wicg.github.io/digital-credentials/) - which would allow User-Agents to mediate communication between a website requiring the submission of evidence and the user's Wallet - by the [Federated Identity Working Group](https://github.com/w3c/strategy/issues/450), on the other hand the lack of a more general model analyzing threats on the various levels related to Security, Privacy, and Human Rights was also identified.

As the Threat Model is a living document, it can be expanded on the other parts of the architecture and at a different level of detail, e.g., going deep into cryptographic aspects of a specific profile or expanding in the broader context of governance to identify or mitigate some threats.

It is also important to note that because it is a generic model, it is useful for understanding the properties and requirements needed for Security, Privacy, and Harm.
Therefore, it is important later to carry over these properties into the specific architecture or implementation, which is defined precisely by architectural and technological choices (e.g., a profile).

It is intended to be a choral analysis. It stems from the need to understand a Threat Model to guide the development of Decentralized Identities in a secure/privacy-preserving way and avoid harm. It will start from the Digital Credentials API from a high-level perspective. 

### Terminology

For Identity, we can refer to the definition in ISO/IEC 24760-1:2019 "[IT Security and Privacy - A framework for identity management](https://standards.iso.org/ittf/PubliclyAvailableStandards/c077582_ISO_IEC_24760-1_2019(E).zip)".

**Identity** is “_a set of attributes related to an entity_”. Where the entity is something "_that has recognizably distinct existence_", and that can be "_logical or physical_" such as "_a person, an organization, a device, a group of such items, a human subscriber to a telecom service, a SIM card, a passport, a network interface card, a software application, a service or a website_" and the attributes are “_characteristics or properties_” such as “_an entity type, address information, telephone number, a privilege, a MAC address, a domain name_”.

We present **credentials** to claim that we have a certain identity, whether in the physical or digital world. Just as we do not have a one-size-fits-all definition of identity, we also do not have a one-size-fits-all definition of credential in IT, as it changes according to context.

If we use the credential definition from the [W3C Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model-2.0/#dfn-credential) (VCDM), it states: “a _set of one or more claims made by an issuer._” Its framing is in the Decentralized Identity Model, and we can map the ISO’s attributes to VCDM claims.

Taking the example of a person, these characteristics can be physical appearance, voice, a set of beliefs, habits, and so on. It is important to distinguish identity from the identifier (e.g., a user name).

It is usual to think of Digital Credentials as related to humans, particularly those issued by a government, also known as "Real-world Identities", even if we also have Non-Human Identities.

This leads to a broader consideration of the Threat Model, as it also includes Privacy as a right and Harm components.

## Related Work
- [joint work on rights-respecting digital credentials ](https://github.com/w3c/strategy/issues/458)
- [User considerations for credential presentation on the Web](https://github.com/w3cping/credential-considerations/blob/main/credentials-considerations.md)
- [Building Consensus on the Role of Real World Identities on the Web](https://github.com/w3c/breakouts-day-2024/issues/12)

## Methodology

Since security is a _separation function between the asset and the threat_, the threat can have different impacts, such as on security, privacy, or [harm](https://learn.microsoft.com/en-us/azure/architecture/guide/responsible-innovation/harms-modeling/).

There are many approaches to Threat Modeling. The first approach we will use is based on [Adam Shostack 4 questions frame](https://github.com/adamshostack/4QuestionFrame):
 - What are we working on? (understanding the architecture, actors involved, etc...)
 - What can go wrong? (threats, threat actors, attacks, etc...)
 - What are we going to do about it? (countermeasures, residual risk, etc...)
 - Did we do a good job? (reiterating until we are happy with the result)

For the central phases, it is possible to use (as in Risk Management) prompt lists or checklists of either threats, attacks, or controls, for example:
 - [Solove's Taxonomy](https://wiki.openrightsgroup.org/wiki/A_Taxonomy_of_Privacy) 
 - [Privacy Patterns](https://privacypatterns.org/patterns/)
 - [Privacy Principles](https://www.w3.org/TR/privacy-principles/)
 - [LINDDUN](https://linddun.org/threats/)
 - [RFC 6973](https://datatracker.ietf.org/doc/html/rfc6973)
 - [RFC 3552](https://datatracker.ietf.org/doc/html/rfc3552)
 - [STRIDE](https://learn.microsoft.com/en-us/previous-versions/commerce-server/ee823878(v=cs.20)?redirectedfrom=MSDN)
 - [OSSTMM v3](https://www.isecom.org/OSSTMM.3.pdf)
 - [Microsoft's Types of harm](https://learn.microsoft.com/en-us/azure/architecture/guide/responsible-innovation/harms-modeling/type-of-harm)
 
It is useful to frame the analysis with OSSTMM. OSSTMM controls allow analyses of what can go wrong (e.g., control not present or problem with a control).

Even if it is control-oriented and seems security-oriented, privacy is an operational control that can connect different pieces. 

## Channel and Vector

OSSTMM is very precise when used to analyze, so it defines a channel and a vector. 

For an accurate analysis, We are considering the COMSEC Data Networks Channel in the specific Internet/Web vector for this issue.

Although different digital credentials may have a different channel/vector (e.g., Wireless), they can still be analyzed similarly.

# Analysis

## What are we building?

To begin to create a good Threat Model, we can first consider the components of the Decentralized Identity architecture (which in this context is synonymous with Self-Sovereign Identity) as defined in [W3C's Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model-2.0/) and how they interact.

### Architecture and Actors

- We have a *Holder* who, inside a _Wallet_, has its credentials.
- We have an *Issuer* who issues the credentials to the *Holder* and manages the revocation.
- We have a *Verifier* who verifies the Holder's credentials to give access to a resource or a service.
- We also have a *Verifiable Data Registry* (VRP) that stores identifiers and schemas.

<img src="https://www.w3.org/TR/vc-data-model-2.0/diagrams/ecosystem.svg">

Interactions between actors occur normatively through software or other technological mediums. We will generically call *Agents* such components. One agent might be embedded in a *Wallet* (the component that contains the *Holder's* credentials), and another might be a Browser (which, by definition, is a user agent).

### Flows

We can consider three general flows, with four "ceremonies" where the various actors interact.
- Credential-Issuing
- Credential-Presentation and Credential Verification
- Credential Revocation

It is important to note that the flow stops here and can be safely continued in several ways. For example, the _Holder_ receives credentials from an _Issuer_ and uses them to identify themself on a _Verifier_ to buy a physical object or ticket to an event. So the _Verifier_ could become an _Issuer_ to issue a certificate of authenticity for good or issue the ticket directly into the _Holder's_ Wallet.

* **Credential Issuing (CI):**
    1. The *Issuer* requests a certain authentication mechanism from the *Holder*.
    2. After authentication, the *Holder* asks the *Issuer* for the credential or the *Issuer* submits it.
    4. If both parties agree, the *Issuer* sends the credential to the Holder in a specific format.
    5. The *Holder* enters their credential into the *Wallet*.
* **Credential-Presentation (CP)**
    1. The *Holder* requests access to a specific resource or service from the *Verifier*.
    2. The Verifier then requests a proof from the *Holder*. This can be done actively (e.g., the Verifier presents a QR code that the Holder has to scan) or passively (e.g., they accessed a web page and were asked to access a credential).
    3. Through the *Wallet*, the holder's user agent determines whether there are credentials to generate the required Proof. 
    4. The *Holder* may use the proof explicitly if they possess it.
    5. The user agent of the *Holder* then prepares the Presentation - which can contain the full credential or part of it-  and sends it to the *Verifier*.
* **Credential-Verification (CV)**
    1. The user agent of the *Verifier* verifies the *Presentation* (e.g., if the Presentation and the contained Credentials are signed correctly, issued by an *Issuer* they trust, compliant with their policy, the Holder is entitled to hold it, and that it has not been revoked or expired). The revocation check can be done using the methods defined by the specific credential.
    2. If the verification is successful, the *Verifier* gives the *Holder* the access.
* **Credential-Revocation (CR)**
    1. The *Issuer* can revoke a credential in various ways.

### Trust and Trust Boundaries

Trust is a key element in threat modeling. In fact, in OSSTMM, it is an element of privileged access to the asset, which, by trusting, lowers the various operational controls.

At the **Process level**, trust relationships are:
- The *Holder* trusts the *Issuer* during issuance.
- The *Holder* trusts its *agents* and *wallet*, always.
- The _Holder_ trusts the _verifier_, during the Presentation.
- The *Verifier* must trust the *Issuer* during Verification.
- All *actors* trust the record of verifiable data.
- Both the *holder* and *verifier* must trust the *issuer* to revoke VCs that have been compromised or are no longer true.

At the **Software level**, Trust boundaries are documented in the [Data Model in section 8.2](https://www.w3.org/TR/vc-data-model-2.0/#software-trust-boundaries):
- An issuer's user agent (issuer software), such as an online education platform, is expected to issue only verifiable credentials to individuals that the issuer asserts have completed their educational program.
- A verifier's user agent(verification software), such as a hiring website, is expected to only allow access to individuals with a valid verification status for verifiable credentials and verifiable presentations provided to the platform by such individuals.
- A holder's user agent (holder software), such as a digital wallet, is expected to divulge information to a verifier only after the holder has consented to its release.

However, from a threat modeling perspective, the issuer, verifier, and holder are external entities, so we have trust boundaries between the parties. This makes sense and is also why we have the concept of (crypto) verification.

### Data Model, Formats, Protocols

To model Decentralized Identities and Credentials, it is possible to use them as a high-level meta-model using Verifiable Credentials documentation (the list of technology is partial; feel free to extend):

* **Data Models:** abstract models for Credentials and Presentation (e.g., the [Verifiable Credentials Data Model](https://www.w3.org/TR/vc-data-model/), mDL in ISO/IEC [18013-5:2021](https://www.iso.org/standard/69084.html)).
* **Identifiers**: [DIDs](https://www.w3.org/TR/did-core/) and the [DID methods](https://w3c.github.io/did-spec-registries/#did-methods), or [WebID](https://w3c.github.io/WebID/spec/identity/).
* **Encoding Schemas**: JSON, JSON-LD, CBOR, CBOR-LD.
* **Securing Mechanisms:** Each mechanism may or may not support different privacy features or be quantum-resistant:
    * **Enveloped Formats (Credential Formats)**: The proof wraps around the serialization of the credential. \
JSONs are enveloped using JSON Object Signing and Encryption ([JOSE](https://datatracker.ietf.org/wg/jose/about/)), and we can find JWT, JWS, and JWK here. JOSE is _cryptographically agile_ (as it can fit different cryptographic primitives) and can also have Selective Disclosure (SD) with  [SD-JWT](https://www.ietf.org/archive/id/draft-fett-oauth-selective-disclosure-jwt-02.html) (which uses HMAC). New securing mechanisms are coming up, like [SD-BLS](https://arxiv.org/abs/2406.19035) (which uses BLS) and ongoing efforts to fit BBS#. \
CBORs are enveloped using CBOR Object Signing and Encryption ([COSE](https://www.rfc-editor.org/rfc/rfc9052)). \
Other formats include _mdoc_ and _[SPICE](https://datatracker.ietf.org/wg/spice/about/)_. \
The mechanism to use VCDM with JOSE/COSE is described in [Securing Verifiable Credentials using JOSE and COSE](https://www.w3.org/TR/vc-jose-cose/).
    * **Embedded Formats (Signature Algorithms):** The proof is included in the serialization alongside the credentials (e.g., BBS, ECDSA, EdDSA). The mechanism is described in [Verifiable Credential Data Integrity 1.0](https://www.w3.org/TR/vc-data-integrity/).
* **Status Information (Revocation Algorithms)**: _Issuers_ can implement several ways to keep up to date the status of the credential, such as Revocation List, Status List (e.g., [Bitstring Status List v1.0](https://www.w3.org/TR/vc-bitstring-status-list/)), Cryptographic Accumulators, etc.
* **Communication Protocols**: for the different phases of Issuance and Presentation (e.g.,  [OID4VCI](https://openid.github.io/OpenID4VCI/openid-4-verifiable-credential-issuance-wg-draft.html), [OID4VP](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html%5D), [SIOPv2](https://openid.net/specs/openid-connect-self-issued-v2-1_0.html)).

### Assets

Assuming that the main asset is the credentials and information derived during its life cycle, we can consider the protection of its three Privacy Properties, as they were defined by [Ben Laurie](http://www.links.org/files/selective-disclosure.pdf), as the basis:
- Verifiable
- Minimal
- Unlinkable

These properties were defined in a very specific case of Decentralized Identities. Those related to people, and even more specifically, those issued by governments, are based on the concept of Privacy, specifically for the protection of the Holder.

While we can, therefore, consider the *Minimal* and _Unlinkable_ properties as elements of the _Holder_, the _Verifiable_ property is of interest to all. Verifiable means that the _Verifier_ can confirm who issued the credential, that it has not been tampered with, expired, or revoked, contains the required data and is possibly associated with the holder.

Therefore, The Threat Model wants to start from this specific use case, that of government-issued credentials for people, considering that it is one of the most complex.

Minimization and Unlinkability are generally interrelated (e.g.,  the less data I provide, the less they can be related). They must coexist with *Verifiability* (e.g., if I need to know that the credential has been revoked, I usually need to contact the Issuer, who has a list of revoked credentials, but in this way, it is possible to link the credential).

#### Minimization Scale
To try to qualify *Minimization*, we can use a scale defined by the various cryptographic techniques developed for Digital Credentials:
 - Full Disclosure (e.g., I show the whole passport).
 - Selective Disclosure (e.g., I show only the date of birth).
 - Predicate Disclosure (e.g., I show only the age).
 - Range Disclosure (e.g., I show only that I am an adult).

#### Unlinkability Scale
To try to qualify *Unlinkability*, we can use the [Nymity Slider](https://www.cypherpunks.ca/~iang/pubs/thesis-final.pdf), which classifies credentials by:
 - Verinymity (e.g., Legal name or Government Identifier).
 - Persistent Pseudonymity (e.g., Nickname).
 - Linkable Anonymity (e.g., Bitcoin/Ethereum Address).
 - Unlinkable Anonymity (e.g., Anonymous Remailers).

Therefore, it might be possible to consider "moving" the slider toward Unlinkable Anonymity, as per the properties.

## What can go wrong?

After reasoning about assets, what we can protect and "who" becomes obvious.

### Threat Actors

We have mentioned before that one of the key points is the protection of the *Holder*. Still, by simplifying and referring to the well-known practice of "trust-no-one", we can easily get the list of actors involved: 

Holder, Issuer, Verifier, and their agents/software components (e.g., Browser, Wallet, Web Sites). Which of these can be a Threat Actor? To simplify the question, each actor is potentially a Threat Actor for the others. So, all against all. Indeed, one is often also a Threat Actor to oneself (e.g., [Alert fatigue](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7272062/)).

In addition, although there are trust relationships between the various actors and their software (valid in the various steps), such software can also be malicious. Specifically, it can track the Holders, the type of credential they have, and how and where they use it through telemetry and statistical collection, and it can push the user to certain attitudes.

One must also consider a possible external **threat actor**, who could also be an observer or use active techniques, and who wants to track the three main actors or their agents, such as Marketers, Data brokers, Stalkers, Identity thieves, intelligence and law enforcement agencies (laws often constrain them), and OSINT investigators.

A further case is the combination of such actors, such as multiple _Verifiers_ in cahoots or a potential collaboration between Issuer and _Verifier_ to track the _Holder_. 

### Evil user stories

Using the information we now have, we can create some generic [Evil User Stories](https://www.owasp.org/index.php/Agile_SoHware_Development:_Don%27t_Forget_EVIL_User_Stories): 
 - A malicious *Verifier* who wants to collect too much data from the Holder.
 - A malicious *Holder* who wants to get what he is not entitled to from the verifier.
 - A malicious *Issuer* who wants to track its holders.
 - A malicious *Agent* who wants to track its holder.
 - An external *Adversary* who wants to track the _Issuer_, how a _Verifier_ works or a specific _Holder_.

 ### Finding the Threats

One effective though inefficient approach to threat modeling is to cycle the various lists of threats and attacks, controls, and objectives in a brainstorming session to assess how they may affect architecture components, actors, assets, and the flow in general. Using multiple frameworks may repeat some elements.

 #### Ben's Privacy Properties (Objectives)
 - **Verifiable**:
      - *Description*: There’s often no point in making a statement unless the relying party has some way of checking it is true. Note that this isn’t always a requirement—I don’t have to prove my address is mine to Amazon because it's up to me where my goods get delivered. But I may have to prove I’m over 18 to get alcohol delivered.
      - *Analysis*: This brings us to the concept of integrity (via cryptographic means), which is authenticated and trusted at the level of the issuer and from what exits.
 - **Minimal**:
    - *Description*: This is the privacy-preserving bit - I want to tell the relying party the very least he needs to know. I shouldn’t have to reveal my date of birth or prove I’m over 18 somehow. 
    - *Analysis*: We must release only what is strictly necessary. Since this is an interaction, we can consider Subjugation interactive OSSTMM control. For example, if we need to verify age with our credentials, it is one thing to show the whole document or the date of birth (Selective Disclosure) or to answer a specific query with true-false (Predicate Proofs). This property also helps Unlinkability (the less data I have, the less correlation I can do).
    
 - **Unlinkable**:
   - *Description*: If the relying party or parties, or other actors in the system, can, either on their own or in collusion, link together my various assertions, then I’ve blown the minimality requirement out of the water.
   - *Analysis*: It must be minimal. It should not be possible to map the issuer (Blind Signature), contact them to know if the credential has been revoked (e.g., Revocation via Cryptographic Accumulation), or use revocation lists that expose the list of credentials. Generally, if an identifier can be exploited to link identities, it should rotate (Rotational Identifiers), as with PAN numbers using Apple Pay.

#### LINDDUN (Threats)

- **Linking**:
  - *Description*: Learning more about an individual or a group by associating data items or user actions. Linking may lead to unwanted privacy implications, even if it does not reveal one's identity.
  - *Threat*: We are generally driven to think of this as a threat to the Holder and linking its attributes, but per se, even an _Issuer_ can have the problem of tracking its users. This applies to a _Verifier_ (or a group of Verifiers) and an external third party observing the various exchanges or any Revocation list.
  - *Mitigations*:
    - Use Blinded Signatures.
    The _Verifier_ should request the following: Range Proof, Predicate Proof, Selective Disclosure, and the credential.
    - The _Issuer_ should use an anonymous revocation method such as Cryptographic Accumulators.
    - The _Issuer_ should use random identifiers when generating the credential.
    - The _Holder_ should use - when interacting with the _Verifier_ rotational and always random identifiers specific to that interaction session.
    - The _Issuer_ should use (e.g., DID) privacy-preserving identifiers. Once resolved, they do not generate a connection to a system controlled directly or indirectly by the _Issuer_ itself.

- **Identifying**:
  - *Description*: Identifying threats arises when the identity of individuals can be revealed through leaks, deduction, or inference in cases where this is not desired.
  - *Threat*: The threat is the ability to identify an individual using his credentials.
  - *Mitigations*:
    The _Verifier_ should request the following: Range Proof, Predicate Proof, Selective Disclosure, and the credential.
    - The _Issuer_ and the _Holder_ must not write personally identifiable information (PII) or linkable identifiers in the _VDR_.
     - The _Issuer_ should use an anonymous revocation method.

- **Non-Repudiation**:
  - *Description*: Non-repudiation threats pertain to situations where an individual can no longer deny specific claims.
  - *Threat*: The inability of an actor to deny the issuance or presentation of a credential; an example is a [use-cases from DHS](https://www.dhs.gov/science-and-technology/publication/misuse-mobile-drivers-license-mdl-investigative-aid).
   - *Mitigations*
      - The _Issuer_ must use proper Authentication during the issuing process depending on the Levels of Assurance (LOAs).
      - The _Issuer_, the _Verifier_ and the _Holder_ (and their agents) need to have proper logging, e.g., following the OWASP [ASVS 7.1](https://github.com/OWASP/ASVS/blob/master/5.0/en/0x15-V7-Error-Logging.md) e.g., each log must contain enough metadata for an investigation, time with timezone reference, without PII but with session identifiers but in a hashed format, in a common machine-readable format and possibly signed. 

- **Detecting**:
  - *Description*: Detecting threats pertains to situations where an individual's involvement, participation, or membership can be deduced through observation.
  - *Threat*: In this case, the threat can happen in several stages: when a credential is required to be presented, the credential is verified.
   - *Mitigations*:
      - When proof or a credential is requested, the Holder agent must return the same message and behavior (including timing, to avoid side-channel attacks) whether or not a wallet is present, whether the wallet has a credential or not, whether it has a valid credential, or whether the user does not accept instead. It is the same whether or not the user gives the browser access to the wallet. 
       - When a credential's validity is verified, there should be no direct connections or systems controlled by the Issuer (e.g., when a DID is resolved) to avoid back-channel connections.

- **Data Disclosure**:
  - *Description*: Data disclosure threats represent cases in which disclosures of personal data to, within, and from the system are considered problematic.
  - *Threat*: The threat will be disclosed during presentation and verification. 
   - *Mitigations*:
      The _Verifier_ should request the following: Range Proof, Predicate Proof, Selective Disclosure, and the credential.
      - The _Issuer_ and the _Holder_ must not write personally identifiable information (PII) or linkable identifiers in the _VDR_.
       - The _Issuer_ should use an anonymous revocation method.

- **Unawareness & Unintervenability**:

  - *Description*: Unawareness and unintervenability threats occur when individuals are insufficiently informed, involved, or empowered concerning the processing of their data.
  - *Threat*: For the _Holder_, unaware of how their credentials are used or shared.
  - *Mitigations*:
       - The _Holder_ must be informed when a _Verifier_ asks for the credential's Full Disclosure or Selected Disclosure.
        - The _Holder_ must be informed when their credentials is _Phoning Home_ or possible _back-channel connections_
           The _Holder_ must consent to each use of their credential and must identify the Verifier, the Proof Requested (at the moment of request), and which credentials and information are shared with the Verifier after the selection. 
  
- **Non-Compliance**:
  - *Description*: Non-compliance threats arise when the system deviates from legislation, regulation, standards, and best practices, leading to incomplete risk management.
  - *Threat*: The risk of credentials not complying with legal, regulatory, or policy requirements. It is also possible to translate this element about minimal training for the _Holder_, particularly if they are in a protected or at-risk category, so they can be aware of what they are doing and the risks associated with Social Engineering.
  - *Mitigations*:
     - Provide Security Awareness Training to the _Holder_ 
     - Verifier and Issuers must be subjected to regular audit
     The standards and their implementation must contain mitigations for Harms such as Surveillance, Discrimination, Dehumanization, Loss of Autonomy, and Exclusion.

#### RFC 6973 (Threats)

- **Surveillance**:
  - *Description*: Surveillance observes or monitors an individual's communications or activities. 
  - *Threat*: Although we can semantically link this threat to surveillance of governments (precisely of the _Holder_ or an adversary), we can actually consider surveillance also related to profiling for targeted advertising (and thus from software agents also used to trust the _Holder_) or even of threat actors such as stalkers or similar.
   - *Mitigations*
      - refer to LINDDUN's _Linking_, _Identifying_, _Data Disclosure_

- **Stored Data Compromise**:
  - *Description*: End systems that do not take adequate measures to secure stored data from unauthorized or inappropriate access expose individuals to potential financial, reputational, or physical harm.
  - *Threat*: All actors can be compromised. Therefore, they must be considered, especially in implementing wallets and agents (for the _Holder_), compromising the end-user device and the signature keys of the _Issuer_.
   - *Mitigations*:
      - Keys must be stored securely and protected from compromise of the device or location where they are contained (e.g., Secure Enclave, Keystore, HSMs).
       - At the Issuer's organizational level, the Incident Response Plan must include what to do in case of compromise of [private keys](https://www.wionews.com/world/eus-green-pass-hacked-adolf-hitlers-covid-certificate-doing-rounds-online-report-424736) or [underlying device technology](https://ria.ee/en/news/2011-researchers-identified-security-vulnerability-id-card-used-estonia).

- **Intrusion**:
  - *Description*:  Intrusion consists of invasive acts that disturb or interrupt one's life or activities.  Intrusion can thwart individuals' desires to be left alone, sap their time or attention, or interrupt their activities.  This threat is focused on intrusion into one's life rather than direct intrusion into one's communications.
  - *Threat*: Intrusive and multiple data requests by _Verifier_
  - *Mitigations*:
       - refer to LINDDUN's _Linking_, _Identifying_, _Data Disclosure_
       - Implement time-based throttling to requests

- **Misattribution**:
  - *Description*:  Misattribution occurs when data or communications related to one individual are attributed to another. 
  - *Threat*: Incorrect issuance or verification of credentials.
  - *Mitigations*:
      - refer to LINDDUN's _Non-Reputiation_

- **Correlation**:
  - *Description*: Correlation is the combination of various information related to an individual, or that obtains that characteristic when combined.
  - *Threats*: Linking multiple credentials or interactions to profile or track a _Holder_. We are linking individuals to the same _Issuer_.
  - *Mitigations*:
         - refer to LINDDUN's _Linking_, _Identifying_, _Data Disclosure_

- **Identification**: 
  - *Description*:  Identification is linking information to a particular individual to infer an individual's identity or to allow the inference of an individual's identity.
  - *Threats*: Verifiers asking more information than necessary during credential verification.
   - *Mitigations*:
     - refer to LINDDUN's _Unawareness & Unintervenability_  and _Identifying_.

- **Secondary Use** :
  - *Description*:  Secondary use is the use of collected information about an individual without the individual's consent for a purpose different from that for which the information was collected.
  - *Threat*: Unauthorized use of collected information, e.g., for targeted advertising or create profiles, and Abuse of Functionality on collected data
  - *Mitigations*: 
      - refer to LINDDUN's _Non-Compliance_.

- **Disclosure**:
  - *Description*:  Disclosure is the revelation of information about an individual that affects how others judge the individual.  Disclosure can violate individuals' expectations of the confidentiality of the data they share.
  - *Threat*: A _Verifier_ that asks for more data than needed.
  - *Mitigations*:
     - refer to LINDDUN's _Data Disclosure_

- **Exclusion**:
  - *Description*:  Exclusion is the failure to let individuals know about the data that others have about them and participate in its handling and use.  Exclusion reduces accountability on the part of entities that maintain information about people and creates a sense of vulnerability about individuals' ability to control how information about them is collected and used.
  - *Threats*: Lack of transparency in using the data provided.
  - *Mitigations*;
      - refer to LINDUNN's _Unawareness & Unintervenability_.

#### RFC 3552 (Attacks)

- **Passive Attacks**:
  - *Description*:  In a passive attack, the attacker reads packets off the network but does not write them, which can bring Confidentiality Violations, Password Sniffing, and Offline Cryptographic Attacks.
  - Mitigations:
       - Encrypt Traffic.
      - Use Quantum-Resistant Algorithms.
      - Use Key Management practices to rotate keys.

- **Active Attacks**:
  - *Description*: When an attack involves writing data to the network. This can bring Replay Attacks (e.g., recording the message and resending it), Message Insertion (e.g., forging a message and injecting it into the network), Message Deletion (e.g., removing a legit message from the network), Message Modification (e.g., copying the message, deleting the original one, modifying the copied message reinjecting it into the flow), Man-In-The-Middle (e.g., combination of all the previous attacks).
  - *Mitigations*:
     - Use a nonce to prevent replay attacks
     - Use Message Authentication Codes/Digital Signatures for message integrity and authenticity
     - Use a specific field to bind the request to a specific interaction between the Issuer, Verifier, and Issuer to Holder.
      - Encrypt Traffic.
      - Use Quantum-Resistant Algorithms.

#### STRIDE (Threats)

- **Spoofing** (Threats to Authentication):
  - *Description*: Pretending to be something or someone other than yourself.
  - *Mitigations*:
     - Implement Digital Signatures
     - During the presentation, Indicate proper messages for identifying the _Verifier_ to limit Phishing Attacks.
      - During issuing, use proper LOAs depending on the issued credentials.
  
- **Tampering** (Threats to Integrity):
  - *Description*: Modifying something on disk, network, memory, or elsewhere.
  - *Mitigations*:
      - Implement Digital Signatures in transit and at rest.
  
- **Repudiation** (Threats to Non-Repudiation):
  - *Description*: Claiming that you didn't do something or were not responsible can be honest or false
  - *Mitigations*:
      - refer to LINDDUN's _Non-Repudiation_
  
- **Information disclosure** (Threat to Confidentiality and Privacy):
  - *Description*: Confidentiality	Someone obtaining information they are not authorized to access
  - *Mitigations*:
     - refer to LINDDUN _Data Disclosure_
  
- **Denial of service** (Threats to Availability and Continuity):
  - *Description*: Exhausting resources needed to provide service
  - *Mitigations*:
    - Use a decentralized VRP for verification
    
  - **Elevation of privilege**  (Threats to Authorization): 
    - *Description*: Allowing someone to do something they are not authorized to do
     - *Mitigations*:
       - During issuing, use proper LOAs depending on the issued credentials.


#### OSSTMM (Controls)
- **Visibility**:
   - *Description*: Police science places “opportunity” as one of the three elements that encourage theft, along with “benefit” and “diminished risk.” visibility is a means of calculating opportunity. It is each target’s asset known to exist within the scope. Unknown assets are only in danger of being discovered as opposed to being in danger of being targeted.
    - *Analysis*: In the specific case of (request for) submission, the visibility of a specific wallet credential or assertion should be limited as much as possible when the website requests it. The whole thing must be handled at the user-agent level—or even better. It has to be hidden from it and go directly to the Wallet.
- **Access**
    - *Description*: Access in OSSTMM is precisely when you allow interaction. 
     - *Analysis*: In this case, the only way to do this is with the available API subset, which must be a specific request.
- **Trust**:
     - *Description*: Trust in OSSTMM is when we leverage an existing trust relationship to interact with the asset. Normally, this involves a "relaxation" of the security controls that otherwise manage the interaction.
      - *Analysis*: This specific case should have no trusted access. However, the whole thing could be triggered when asking permission for powerful features. Consider avoiding or limiting this over time (balancing Trust with Subjugation).

- **Authentication**:
  - *Description*: is control through the challenge of credentials based on identification and authorization.
  - *Analysis*: This can be considered the Trust of the issuers and the signatures (in the OSSTMM definition, Identity, Authentication, and Authorization are collapsed in the Authentication).

- **Indemnification**: 
  - *Description*: is a control through a contract between the asset owner and the interacting party. This contract may be a visible warning as a precursor to legal action if posted rules are not followed, specific, public legislative protection, or with a third-party assurance provider in case of damages like an insurance company.

  - *Analysis*: This is the agreement between the interacting parties, such as contracts. In this case, *Notifications* can describe what happens in a "secure" context (e.g., Payments API); all operations must be specifically authorized with Informed Consent. The holder must be notified if the Verifier asks for Full Disclosure, if the Issued Credentials do not support Selective Disclosure, or if it is phoning home.

    *Note: this can be used as a [nudge](https://en.wikipedia.org/wiki/Nudge_theory) (used in Behavioural Economics) and then can be used to educate the Verifiers, Holders, and Issuers to do the right thing.*

- **Resilience**: 

  - *Description*: Control all interactions to protect assets in the event of corruption or failure.
  - *Analysis*: In this context, it means failing securely, which can be considered a failure in the case of cryptographic problems.

- **Subjugation**: 

  - *Description*: It is a control that assures that interactions occur only according to defined processes. The asset owner defines how the interaction occurs, which removes the interacting party's freedom of choice and liability for loss.
  - *Analysis*: This is a particularly interesting aspect based on the context. As mentioned earlier, one would need to make sure that one is always asked for the minimum information, if and when available (e.g., priority to Predicates Proof, then to Selective Disclosure, bad the whole credential), somewhat like what happens on SSL/TLS with the ciphers.

- **Continuity**: 

  - *Description*: controls all interactions to maintain *interactivity* with assets in the event of corruption or failure.
  - *Analysis*: This is the concept of continuity balancing in case of problems, although in this case, if there are problems, it's the case to terminate the interaction. But in general terms, for the Holder, there is the need for a secure backup.

- **Non-Repudiation**: 

  - *Description*:  is a control that prevents the interacting party from denying its role in any interactivity.
  - *Analysis*: The issue of non-repudiation is interesting, and it makes me think about the logging issue, where it should be done, by whom, and how. It requires a strong trust element. Still, in general, it's useful for the Holder to have the log of what happened and probably the Verifier as well (e.g., in case of checks of having given access to a certain service by those who had the right to do so having presented a credential, but what to keep considering privacy as well)?

- **Confidentiality**:

  - *Description*: is a control for assuring an asset displayed or exchanged between interacting parties cannot be known outside of those parties.
  - *Analysis*: One of cryptography's important aspects is how effectively it can guarantee confidentiality. A point can be post-quantum cryptography (PQC) readiness. A countermeasure is limiting a credential's lifetime and re-issuing it with a more secure cryptosuite.

- **Privacy**:

  - *Description*:  is a control for assuring the means of how an asset is accessed, displayed, or exchanged between parties cannot be known outside of those parties.
  - *Analysis*: mainly unlinkability and minimization, as described before. In the context of the Digital Credentials API, this also includes preventing third parties from unnecessarily learning anything about the end-user's environment (e.g., which wallets are available, their brand, and their capabilities).

- **Integrity**: 

  - *Description*: It is a control to ensure that interacting parties know when assets and processes have changed.
  - *Analysis*: The credential and its presentation to be verified must be cryptographically signed.

- **Alarm**:

  - *Description*: is a control to notify that an interaction is occurring or has occurred.
  - *Analysis*: It is important to notify users and then allow when an interaction happens, particularly when it has low values related to the Unlinkabiity Scale or Minimization Scale; this can happen for several reasons. For example, the Issuer uses a technology that "calls home," or the Verifier asks for data that could be minimized instead.

#### Responsible Innovation (Harms)

- **Opportunity loss** (_Discrimination_): This complex issue spans multiple areas. Digital divide: if digital identities are required for access to public services and no alternatives are present, and if they depend on certain hardware, software, or stable connectivity, it can lead to discrimination for people who do not have availability of these resources. In addition to discrimination within the same country, there is further discrimination if there is no “cross-border” interoperability between the technologies and implementations used by different governments.
- **Economic loss** (_Discrimination_): the availability of digital identities and related credentials, which can contain a lot of information regarding wealth status, can be used to discriminate against access to credit. This can also be generalized - as was identified during a W3C breakout session concerns the Javons paradox. The more information available in this mode, the more likely it is that collection, particularly in greedy data-driven contexts, is abused.
- **Dignity loss** (_Dehumanization_): For example, if the vocabulary does not correctly describe people's characteristics, this can reduce or obscure people's humanity and characteristics.
- **Privacy Loss** (_Surveillance_): if this technology is not designed and implemented properly, it can lead to surveillance by state and non-state actors such as government and private technology providers. For example, centralized or federated models are more prone to these threats, while decentralized models are less so, but it depends on how they are implemented. Therefore, it is necessary to provide privacy-preserving technologies and implement them properly.

### Other Threats and Harms

Considering the specific case of government credentials issued to people, it is useful to think about some use cases:
- In some countries, at-risk workers who are taken abroad have their passports seized by those who exploit them so that they can be controlled. Digital Credentials can generally mitigate this as being intangible; they can be regenerated in case of theft. A further consideration is how the threat agent will act when faced with this situation and what mitigations (more process than merely technical) governments can implement.
- Normally, we assume that the _Holder_ of the credential is also the _Subject_ to whom the credential refers.  This is not necessarily the case.
  - One particularly useful and interesting aspect is the delegation of a credential (we use the term delegation loosely, as questions such as Guardianship have a precise legal meaning). This prevents abuse and identity theft and should be modeled properly as Issuer rules on the upper layers of the architecture.
  - Also, delegation could be a crucial feature if the government generates a credential at the organizational level, which is then used by legal representatives (who are people).

Another scenario is the use of a credential for authentication:
- In contrast to what can happen with credentials in other identity models, where credentials are used primarily for authentication, it can be risky to use a credential issued by an issuer to authenticate to a service that is not under the control of the issuer, as a malicious issuer could generate a parallel ad-hoc credential to authenticate. For example, it may not be a good idea to log into your personal e-mail with a government-issued credential such as a passport.

## What are we going to do about it?

Countermeasures/Features:
- **[Blinded Signature](http://www.hit.bme.hu/~buttyan/courses/BMEVIHIM219/2009/Chaum.BlindSigForPayment.1982.PDF)**:  is a type of digital signature for which the content of the message is concealed before it is signed. With Public-Private Key Cryptography, the signature can be correlated with who signed it, specifically to their public key (and this is an important feature if we think about when we want to be sure of the sender when using GPG). Zero-knowledge cryptographic methods do not reveal the actual signature. Instead, with ZKP, we can send cryptographic proof of signature without providing the verifier with any other information about who signed. Thus protecting the public key of the holder.
- **[Selective disclosure](http://www.links.org/files/selective-disclosure.pdf)**:  is the ability to show only a part (claim) of the credential and not all of it or show only possession of that credential. as needed in the context of the transaction. For example, we can show only the date of birth rather than the entire driver's license where it is contained. This allows us to minimize the data sent to the verifier further.
- **[Predicate Proofs and Range Proof](https://arxiv.org/pdf/2401.08196)**: is the ability to respond to a boolean assertion (true-false) to a specific request, and it is an additional step for privacy and minimization. For example, if we say we are of age, I don't have to show just the date of birth but compute the answer.
- **Anonymous Revocation**: A credential has its life cycle: it is issued, it is used, and then it can be revoked for various reasons. Therefore, a verifier must be able to verify whether the credential has been revoked, but this must be done without allowing the ability to correlate information about other revoked credentials. There are different Techniques:
    - **Revocation List**: This is the current generally used approach, although it creates privacy issues, as the lists must be public and typically contain user information.
   - [**Status List**](https://www.w3.org/community/reports/credentials/CG-FINAL-vc-status-list-2021-20230102/): revocation document only contains flipped bits at positions that can only be tied to a given credential if you'd been privy to the disclosure of their association.
   - [**Status Assertions**](https://datatracker.ietf.org/doc/html/draft-demarco-oauth-status-assertions): is a signed object that demonstrates the validity status of a digital credential. These assertions are periodically provided to holders, who can present these to the verifier along with the corresponding digital credentials.
   - **[Cryptographic accumulators](https://eprint.iacr.org/2024/657.pdf)**: can generate proof of validity ***[without exposing other information](https://ieeexplore.ieee.org/document/10237019)***.
- **Rotational Identifiers**: As indicated by the [Security and Privacy Questionnaire](https://www.w3.org/TR/security-privacy-questionnaire/#temporary-id), identifiers can be used to correlate, so it is important that they are temporary as much as possible during a session and changed after they are used. In this context, the identifiers that can be exploited to correlate can be present at different levels.
- **No Phoning home or back-channel communication**: Software often "calls home" for several reasons. They normally do this to collect usage or crash statistics (which could indicate a vulnerability). The problem is that this feature, often critical to software improvement and security, has privacy implications for the user, in this case, the _Holder_. At the Credentials level, this call can be made at different times and by different _agents_. For example, suppose the _Issuer_ is contacted by the _Verifier_ to check the revocation of a credential, or the _Wallet_ can contact its vendor to collect usage statistics. In that case, we can consider two types of countermeasures:
  - **Do not phone home or back-channel communication**: This could also be an operational necessity (several use cases require the presentation to be made in offline environments or with limited connection) or a choice of the _Holder_, who should always _consent_ to telemetry and external connections to third-parties. 
  - **Minimize and Anonymize the Data**: Limit the data passed or, even better, cryptographic privacy-preserving techniques like [STAR](https://arxiv.org/pdf/2109.10074) that implements [k-anonymity](https://dataprivacylab.org/dataprivacy/projects/kanonymity/paper3.pdf) for telemetry.
  - **Using Privacy-Preserving DIDs**: When resolving a DID, it is possible that the method uses a connection to a system for resolution. If this system is under the direct or indirect control of the _Issuer_, generating potential privacy issues. For example, this typically happens with `did:web` [as mentioned in section 2.5.2](https://w3c-ccg.github.io/did-method-web/#read-resolve) where a GET is generated that retrieves a file, effectively exposing the requesting user agent and allowing the _Issuer_ to make statistics. 
- **Notification/Alerts**: An important aspect, particularly about interactions where the user is required to interact through an Internet credential, is communication with the user, which occurs at the following times
   - Before the request for the proof: for example, a website requests age verification, permission must first be given to the website to access the functionality, and when the user decides whether or not to give access, the URL and type of credential requested and the level of Minimization (to discourage you know the Verifier and the Holder from using Full Disclosure) must be indicated in a secure context.
   - Before sending the proof, the user selects the Wallet of his choice, the credential or set of credentials from the wallet, and the specific claims from the credentials. The Holder must be notified and asked for confirmation and consent, particularly when the type of presentation he proposes has **phone calling** or **back-channel communication** features (to discourage the _Issuer_ and _Verifier_ from these practices).
