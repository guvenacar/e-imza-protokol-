# Ephemeral E-Signature Project

> **One-time, context-locked digital signatures — without hardware tokens.**  
> Designed for citizen-friendly use and post-quantum readiness.

![Protocol Flow Diagram](images/e-imza-protokolu.png)

## Quick Highlights

- ✅ **No Permanent Keys:** Private keys are generated per transaction, never stored.
- 🔒 **Context-Bound:** Signatures are locked to a single document + institution.
- 🛡 **Replay-Proof:** Tokens, nonces, and timestamps prevent reuse.
- ⏱ **Short TTL:** Certificates and keys auto-expire within minutes.
- 📱 **Citizen-Friendly:** Runs entirely on user’s device in an isolated workspace.

---

## Key Documents

- 🏛 **[Centralized Model](docs/centralized-model.md)** – all key generation and signing in secure central infrastructure  
- 🖥 **[Isolated Workspace Protocol](docs/isolated-workspace.md)** – fully local key generation and signing in sandboxed environment  
- 🔄 **[Hybrid Model](docs/hybrid-model.md)** – combines centralized coordination with local key generation  
- 🔑 **[Single-Use Key Generation Protocol](docs/single-use-key.md)** – how per-transaction keys are derived and why they cannot be reused  

---

## Additional Resources

- 📜 **[Legal & Compliance Considerations](docs/legal-framework.md)** – regulatory alignment, 5070 law, eIDAS  
- 🧪 **[Security Analysis & Threat Model](docs/security-analysis.md)** – attack scenarios, replay protection, risk calculations  

---

**Author:** Güven Acar  
**Contact:** guvenacar@gmail.com  
**ORCID:** [0009-0000-4232-7405](https://orcid.org/0009-0000-4232-7405)
