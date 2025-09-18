# Security Analysis & Threat Model

## 1. Threat Model

- **T1:** External attacker (network-level)
- **T2:** Compromised infrastructure (partial breach)
- **T3:** Malicious insider
- **T4:** Replay attacks / man-in-the-middle
- **T5:** Advanced Persistent Threat (APT)

## 2. Key Security Properties

- 🔑 One-time key derivation per transaction
- 🔒 Context-bound signatures (target-locked)
- ⏱ Short TTL certificates & tokens
- 🛡 Replay protection (nonce + timestamp)

## 3. Attack Scenarios & Mitigation

| Attack | Traditional PKI Impact | Ephemeral Protocol Impact |
|-------|----------------------|--------------------------|
| Key theft | Long-term identity takeover | Affects only one transaction |
| Device malware | Can steal stored keys | No key stored to steal |
| CA compromise | Large-scale impact | Limited by short-lived certs |

## 4. Metrics & KPIs

- Target: <0.001% successful attacks
- >99.9% transaction completion rate
- <5 min incident response time

## 5. Continuous Improvement

- Regular penetration testing
- Threat intelligence updates
- Independent security audits
