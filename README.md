# Ephemeral E-Signature Security System

**Author:** Güven Acar  
**Date:** September 2025

---

## Overview

This project introduces a revolutionary approach to e-signature security by eliminating the need for permanent private key storage. The system is built on **ephemeral (single-use) keys** and **temporary certificates**. When a key is compromised, it affects only one transaction—past or future transactions remain completely secure.

---

## Philosophical Breaking Point

This system initiates a new paradigm in digital identity and security:

### Societal Transformation
- **End of unsigned transactions:** All official, legal, and financial transactions will now be cryptographically signed
- **Universal e-signature ownership:** Just as everyone has a national ID number, everyone will have an e-signature
- **Background security:** Even elderly or non-technical users will unknowingly conduct transactions with their own e-signatures
- **Bridging the digital divide:** Everyone from young to old will be included in this ecosystem

### Economic Paradigm Shift
- **Current system:** Every citizen must purchase a 200-500 USD e-signature device
- **New system:** Universal access through government infrastructure
- **Cost advantage:** Zero hardware investment for users
- **Scale economy:** Centralized infrastructure for 80+ million citizens

### Security Revolution
- **Traditional risk:** One key compromise = all past transactions become suspicious
- **Ephemeral approach:** Compromise affects only that single transaction
- **Forward secrecy:** Backward security similar to TLS protocols
- **End of fraud:** Unauthorized transactions, identity theft, and transaction repudiation become nearly impossible

---

## System Models

### Model 1: E-Government Centralized Model

**Target Users:** Elderly individuals, fixed-income citizens, anyone who doesn't want technical complexity

**Core Concept:**
All transaction coordination, key generation, and certificate distribution are managed by government infrastructure. Users don't need to generate or store keys—they simply log in with existing e-Government credentials.

**Societal Impact:**
- **Inclusivity:** No one is left out, technical barriers are removed
- **Invisible security:** Users unknowingly sign every transaction
- **Rapid adoption:** Instant use with existing e-Government credentials

**Transaction Flow:**
1. User logs into e-Government and initiates transaction
2. Relying party (bank, institution) sends request to e-Government
3. e-Government requests temporary certificate from CA
4. NTA generates session token and distributes to parties
5. e-Government encrypts data and signs with Token-Egov
6. CA returns single-use certificate
7. e-Government forwards signature to relying party
8. Signed document is presented to user

<p align="center">
  <img src="images/model-1-diyagram.png" alt="Model 1 – E-Government Centralized Model" width="700">
  <br>
  <em>Figure 1: E-Government Centralized Model</em>
</p>

### Model 2: Isolated Workspace Protocol

**Target Users:** Anyone wanting maximum security with zero technical complexity  
**User Experience:** Simple app download, one-click signing, automatic cleanup  
**Core Concept:**
An isolated environment (Docker-like) is created on the user's device. All key operations are performed in this secure environment and destroyed after the transaction. 

**Security Advantages:**
- **Identity-independent signature:** Key material is independent of personal data
- **Even if stolen, identity doesn't leak:** No connection between private key and identity information
- **Limited impact:** Single-use keys ensure attacks affect only that transaction
- **Platform agnostic:** Docker on PC, TEE/Secure Enclave on mobile

**Transaction Flow:**
1. User initiates transaction, verifies identity with 2FA
2. Isolated workspace is created
3. uPub and CAPub are loaded
4. TxID and hash digest are generated
5. NTA generates session token
6. Institution sends uPub+CAPub+token to CA
7. CA generates temporary nPub and sends to user
8. Isolated environment signs nPub and sends to CA
9. CA validates and returns temporary certificate

<p align="center">
  <img src="images/model-2-diyagram.png" alt="Model 2 – Isolated Workspace Protocol" width="700">
  <br>
  <em>Figure 2: Isolated Workspace Protocol</em>
</p>

### Model 3: Hybrid (Transition) Model

**Target Users:** Existing e-signature holders, users wanting mixed usage during transition period

**Core Concept:**
Ensures existing e-signature holders aren't left out of the system. All transactions are secured with ephemeral certificates in the background.

**Transition Strategy:**
- **Smooth integration:** Old and new users in the same ecosystem
- **Security standardization:** All transactions elevated to the same ephemeral level
- **User habits preserved:** Existing devices continue to be used

**Transaction Flow:**
1. User applies to institution with existing e-signature
2. Institution sends transaction summary to user
3. User signs with their own device
4. Institution requests transaction initiation from NTA
5. NTA sends token to both institution and CA
6. Institution sends token + uPub information to CA
7. CA generates temporary certificate and returns to institution
8. Institution completes transaction

<p align="center">
  <img src="images/model-3-diyagram.png" alt="Model 3 – Hybrid Transition Model" width="700">
  <br>
  <em>Figure 3: Hybrid (Transition) Model</em>
</p>

### Model 4: Online E-Signature Enrollment Process

**Target Users:** New e-signature applicants, travelers, those wanting quick solutions

**Core Concept:**
User creates key pair on their own device and obtains e-signature. Physical CA visit is unnecessary.

**Revolutionary Features:**
- **End of physical applications:** E-signature can be obtained even while on vacation
- **Instant use:** Fully qualified e-signature within minutes
- **Self-service:** User-controlled entire process

**Transaction Flow:**
1. User initiates e-signature request through e-Government portal
2. E-Government requests session token from NTA
3. Token is sent to both e-Government and CA
4. Key pair is generated on user's device
5. E-Government sends public key and token to CA
6. CA generates certificate and delivers to isolated environment
7. User now has a real e-signature

<p align="center">
  <img src="images/model-4-diyagram.png" alt="Model 4 – Online E-Signature Enrollment Process" width="700">
  <br>
  <em>Figure 4: Online E-Signature Enrollment Process</em>
</p>

---

## Security Model

### Protocol-Level Security

**Ephemeral Key Lifecycle:**
- Unique key pair for each transaction
- Automatic destruction after transaction
- Forward secrecy guarantee

**Proof-of-Possession:**
- nPub challenge-response mechanism
- Private key ownership proof required
- Replay attack protection

**Session Management:**
- 30-second validity period
- Transaction binding (TxID)
- Token hijacking protection

**Certificate Lifecycle:**
- Single-use certificates
- Automatic revocation after transaction
- CRL/OCSP integration

### Security Assumptions

This protocol is secure under the following assumptions:

**Infrastructure Level (Existing Responsibilities):**
- CA HSM security
- e-Government authentication infrastructure security
- Network transport security (TLS/HTTPS)
- NTA availability and integrity

**Implementation Level:**
- Cryptographically secure random number generation
- Proper session token management
- Isolated workspace integrity (TEE/Docker/VM)
- Device-level basic security hygiene

**Legal Framework:**
- Turkish e-Signature Law 5070 compliance
- eIDAS regulation alignment
- GDPR privacy requirements

---

## Threat Model

### Addressed Threats
- **Long-term key compromise:** Solved by ephemeral approach
- **Replay attacks:** Prevented by session token and TxID binding
- **Identity disclosure:** Identity information invisible in signatures
- **Transaction repudiation:** Prevented by cryptographic binding
- **Man-in-the-middle:** Protected by TLS and token validation

### Out of Scope (Infrastructure Level)
- **CA infrastructure compromise:** General PKI security problem
- **Government surveillance:** Political/legal issue, outside protocol scope
- **Device malware:** General computer security problem
- **Social engineering:** User education issue
- **Physical device theft:** Device-level security responsibility

---

## Legal Compliance

### Turkey
- **e-Signature Law 5070:** Full compliance
- **GDPR (KVKK):** Privacy-by-design approach
- **BTK Regulations:** NTA coordination model

### International
- **eIDAS Regulation:** EU standards compliance
- **Common Criteria:** Security evaluation standards
- **ISO 27001:** Information security management

---

## Use Cases

### Banking Sector
**Model 1 Scenario:**
- Elderly customer transfers money via ATM
- Automatic e-signature in background
- User sees no technical details

**Model 2 Scenario:**
- Corporate customer high-volume transfer
- Maximum security in isolated workspace
- Audit trail and compliance requirements

### Public Services
**Universal Access:**
- All citizens have e-signatures
- From license applications to tax returns
- End of paper transaction era

**Elderly-Friendly:**
- Barrier-free technical usage
- Existing e-Government habits preserved
- Even pension payments are signed

### Travel Scenarios
**With Model 4:**
- Emergency e-signature need while traveling
- Online enrollment process
- Ready for use within minutes

---

## Economic Impact

### Cost Analysis
**User Perspective:**
- Traditional: 200-500 USD e-signature device + annual fees
- Ephemeral: Zero hardware cost
- ROI: Immediately positive return

**Government Perspective:**
- Infrastructure investment: One-time
- Scale advantage: 80+ million users
- Operational efficiency: Paperless savings

**Societal Benefits:**
- Increased digital inclusion
- Bureaucratic efficiency
- Fraud reduction benefits

---

## Technical Requirements

### Minimum System Requirements

**PC/Desktop:**
- Docker Desktop or VM support
- Modern web browser (Chrome 90+, Firefox 88+)
- 4GB RAM (for isolated workspace)
- Hardware security module (optional)

**Mobile:**
- Android 8+ (TEE support)
- iOS 12+ (Secure Enclave)
- Biometric authentication capability
- 2GB available storage

### Platform-Specific Isolation

**Android:**
- Samsung Knox Container
- Android Work Profile
- Hardware-backed Keystore
- ARM TrustZone TEE

**iOS:**
- Secure Enclave
- Hardware Security Module integration
- Keychain Services
- Touch/Face ID integration

**PC:**
- Docker containerization
- Windows Sandbox
- VMware/VirtualBox VM
- Hardware HSM integration

---

## Implementation Roadmap

### Phase 1: Pilot Program (6 months)
- Model 1 implementation
- Selected public institutions
- 10,000 user test

### Phase 2: Banking Integration (6 months)
- Model 2 implementation
- Pilot bank partnerships
- 100,000 user capacity

### Phase 3: Hybrid Transition (12 months)
- Model 3 implementation
- Existing e-signature holder integration
- 1 million+ user scale

### Phase 4: National Deployment (18 months)
- All models active
- 80+ million citizen capacity
- International expansion preparation

---

## Responsibility Boundaries

### Protocol Responsibility
This system guarantees the following security features:
- Ephemeral key lifecycle management
- Cryptographic transaction binding
- Session token validation
- Certificate revocation handling

### Infrastructure Responsibility (Out of Scope)
The following topics are the responsibility of existing infrastructure owners:
- e-Government infrastructure availability
- CA HSM security management
- Network-level DDoS protection
- Government policy compliance

This separation is analogous to Google Auth: OAuth protocol is secure, but Google's server security is Google's responsibility.

---

## Contributing

### Developer Community
- GitHub repository: [project-link]
- Issue tracking and feature requests
- Security vulnerability disclosure
- Documentation improvements

### Academic Collaboration
- Research paper collaborations
- Security analysis partnerships
- International standardization efforts

---

## Contact

**Project Owner:** Güven Acar  
**Email:** guvenacar@gmail.com  
**GitHub:** https://github.com/guvenacar/ephemeral-e-signature

---

## License

[License information to be added]

---

## Conclusion

This system offers a paradigmatic change in digital identity and e-signature domains. By harmonizing security, usability, and societal inclusivity, it creates a truly universal digital signature ecosystem.

Just as the internet became universal, this system aims for e-signatures to become a natural part of every citizen's daily life. Our vision is a future where everyone can conduct secure digital transactions regardless of age, technical knowledge, or economic status.