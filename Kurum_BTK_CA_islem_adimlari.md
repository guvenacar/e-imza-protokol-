# BTK Jetonu — Üretim & Doğrulama (Markdown)

Bu belge, Model‑2 akışında geçen **BTK token** üretim ve doğrulama sürecinin kesin, canonical ve uygulanabilir tanımını içerir. Mühendislerin doğrudan kullanabileceği formatlar, kriptografik parametreler, hata senaryoları ve kısa kontrol listesi eklenmiştir.

---

## Kısa Özet

BTK, her işlem için bir **token** üretir. Bu token:

* `token_id`, `issued_at`, `ttl_seconds` gibi meta alanlar içerir.
* Her işlem için **CA** ve **Kurum (Institution)** için ayrı **şifreli blob** (ca_blob, inst_blob) taşır.
* Token, **canonical serialization** üzerinde BTK tarafından **HSM** ile imzalanır.
* CA kendi blob'unu açıp kullanıcı onayı aldıktan sonra `sPriv` ile belge hash'ini imzalar ve Kurum'a **hybrid-encrypt** edilmiş bir paket gönderir.
* Kurum gelen paketi açıp token ile çapraz doğrulama (challenge‑hash) yapar, TTL ve tek‑kullanım kontrollerini uygular, işlemi tamamlar.

---

## Adım Adım Açıklama (Detaylı)

Aşağıda her adım için: **Ne**, **Neden**, **Nasıl / Format**, ve **Kontroller** açıklanmıştır.

### 1) Kurum → BTK: İşlem Başlat

**Ne:** Kurum BTK'ya işlem talebi gönderir.
**İçerik:** `requester_org`, `operation` (ör. `DOCUMENT_SIGN`), `document_hash` (canonical SHA‑256).
**Nasıl:** `document_hash = sha256(canonical_bytes_of_document)` (hex veya `sha256:<hex>` formatı). TLS ile gönder.

---

### 2) BTK: Token Temel Alanlarını Oluşturma

**Ne:** BTK aşağıdaki alanları üretir:

* `token_id` (UUIDv7 veya 128/256-bit CSPRNG)
* `issued_at` (ISO8601 UTC, ör: `2025-11-23T11:45:40Z`)
* `ttl_seconds` (örn. `30`)
* `ca_ch` ve `inst_ch` (her biri 32 byte CSPRNG, hex)

**Neden:** `issued_at` + `ttl_seconds` token geçerliliğini belirler; `*_ch` değerleri cross‑validation için kullanılır.

**Kontroller:** Sistem saati NTP ile sync olmalı.

---

### 3) BTK: ca_blob Oluşturma (CA için şifreli blob)

**Ne:** CA_only plaintext JSON oluşturulur:

```json
{
  "my_challenge": "<hex ca_ch>",
  "peer_challenge_hash": "sha256:<hex inst_ch>",
  "document_hash": "sha256:<hex>",
  "ephemeral_pub": "<base64 X25519 ephemeral pub>",
  "nonce_meta": "..."
}
```

**Nasıl (hybrid encrypt):**

* Ephemeral X25519 keypair oluştur (ephemeral_priv, ephemeral_pub).
* shared = X25519(ephemeral_priv, CA_pub).
* sym_key = HKDF(shared, info="BTK-hybrid", L=32).
* AEAD encrypt plaintext (ChaCha20‑Poly1305 veya AES‑GCM).
* Paket: `{ephemeral_pub, nonce, ct, tag}` → Base64 → `ca_blob`.

**Kontroller:** CA_pub geçerli ve versiyon uyumlu olmalı.

---

### 4) BTK: inst_blob Oluşturma (Kurum için şifreli blob)

Aynı şekilde Kurum public key'i ile şifrelenmiş plaintext:

```json
{
  "my_challenge": "<hex inst_ch>",
  "peer_challenge_hash": "sha256:<hex ca_ch>",
  "document_hash": "sha256:<hex>",
  "ephemeral_pub": "<base64 X25519 ephemeral pub>",
  "nonce_meta": "..."
}
```

---

### 5) BTK: Token Oluşturma

**Şablon (canonical JSON; belirli alan sırası kullanılmalı)**:

```json
{
  "token_id": "<id>",
  "issued_at": "<ISO8601 UTC>",
  "ttl_seconds": 30,
  "operation": "DOCUMENT_SIGN",
  "document_hash": "sha256:<hex>",
  "requester_org": "<org>",
  "target_ca_id": "<ca_id>",
  "ca_blob": "base64:<...>",
  "inst_blob": "base64:<...>"
}
```

**Not:** canonical serialization (deterministic key order, UTF‑8, no extra whitespace) zorunludur.

---

### 6) BTK: Token'ı HSM ile İmzalama

**Ne:** `btk_signature = HSM.sign(canonical_serialize(token))` (Ed25519 tavsiye).
**Neden:** Token kaynağı ve bütünlüğü garanti edilir.
**Kontroller:** HSM loglanmalı; `btk_key_id` token ile iliştirilir.

---

### 7–8) BTK → CA ve Kurum: Token Gönderimi

**Ne:** `token + btk_signature + btk_key_id` TLS üzerinden CA ve Kurum'a iletilir.
**Kontroller:** Kanal TLS, authentication; alıcılar imzayı doğrulamalı.

---

### 8.1) Kurum: Erken Doğrulamalar

1. **BTK imzasını doğrula.**
2. **TTL kontrolü:** `now_utc <= issued_at + ttl_seconds + allowed_skew`.
3. **Used-token-store kontrolü:** token_id daha önce kullanılmamış olmalı.

**Opsiyonel:** Kurum kendi `inst_blob`'unu açabilir (Inst_priv ile) ve `inst_ch`, `peer_hash`, `document_hash` alabilir.

---

### 9–12) CA: İmza Doğrulama & Kullanıcı Onayı

* CA BTK imzasını doğrular.
* CA `ca_blob`'u açar ve `ca_ch` ile `peer_chash` ve `document_hash` alır.
* CA kullanıcıdan onay ister (token_id + belge özeti gösterilir).
* Kullanıcı onay verirse CA devam eder.

---

### 13) CA: Tek Kullanımlık İmza (sPriv) Üretimi ve İmzalama

* CA, işlem için tek kullanımlık sPriv/sPub üretir (tercihen HSM).
* CA, `document_hash` üzerinde sPriv ile imzalar.
* CA oluşturduğu sCert içinde **token_id** ve **btk_signature** referansını bulundurur.
* CA, sPriv’i işlem sonunda imha eder (veya destruction proof sağlar).

---

### 14) CA → Kurum: Şifreli Paket Gönderimi

**Paket içeriği (plaintext örneği):**

```json
{
  "token_id": "<id>",
  "signed_hash": "base64:<CA sPriv signature>",
  "sPub": "base64:<sPub>",
  "sCert": { /* sCert includes token reference */ },
  "ca_challenge": "<hex ca_ch>",
  "document_hash": "sha256:<hex>"
}
```

**Nasıl:** Paket, Kurum'un `Inst_pub` ile hybrid‑encrypt edilir (ephemeral ECDH + AEAD).

---

### 15–17) Kurum: Paket Açma & Cross‑Validation

* Kurum paketi `Inst_priv` ile açar.
* Kurum kendi `inst_blob`'u açar (eğer önceden açmadıysa) ve `inst_ch`, `peer_hash`, `document_hash` alır.
* **Cross‑validation kontrolleri:**

  * Paket içindeki `token_id` veya `btk_signature` token ile eşleşmeli.
  * Paket içindeki `document_hash` token/inst_blob içindeki `document_hash` ile aynı olmalı.
  * `sha256(ca_ch)` == `inst_blob.peer_challenge_hash` ve `sha256(inst_ch)` == `ca_blob.peer_challenge_hash`.

---

### 18) TTL & Tek Kullanım Kontrolü

* Kurum son kontrol olarak TTL ve used-token-store kontrolünü tekrarlar.
* Eğer geçerliyse `token_id` atomic olarak used‑store’a kaydedilir.

---

### 19) Tamamlama & Audit

* Kurum işlemi tamamlar ve immutable audit kaydı oluşturur: `{token_id, timestamps, CA_signature_refs, BTK_key_id, verifier_id}`.
* CA/BTK'ya opsiyonel ACK gönderilir.

---

## Kriptografik Parametreler (Önerilen)

* Signature: **Ed25519** (BTK & CA root)
* Hybrid ECDH: **X25519** (ephemeral)
* AEAD: **ChaCha20‑Poly1305** (mobil) veya **AES‑256‑GCM** (sunucu/HSM)
* Hash: **SHA‑256**
* KDF: **HKDF‑SHA256** (info strings: `BTK-hybrid`, `SUDSS-FINAL_VERIFICATION`)
* Challenge uzunluğu: **32 bytes (256 bit)**
* Nonce: **12 bytes** (AEAD)
* TTL önerisi: **30s** başlangıç (test sonrası 60s opsiyonel)

---

## Canonical Token Örneği (JSON)

Kopyala‑yapıştır için canonical örnek:

```json
{
  "token_id": "XNXXC344980CBBB",
  "issued_at": "2025-11-23T11:45:40Z",
  "ttl_seconds": 30,
  "operation": "DOCUMENT_SIGN",
  "document_hash": "sha256:3a4f...efa2",
  "requester_org": "FFTR4345",
  "target_ca_id": "CAAFR5532a",
  "ca_blob": "base64:...",
  "inst_blob": "base64:..."
}
```

`btk_signature` ve `btk_key_id` token ile beraber ayrı alan olarak taşınır.

---

## Hata Senaryolar & Yanıtlar (Kısa)

* **BTK imza invalid:** reject, log, alert BTK/CA.
* **AEAD decrypt failure:** reject, log (possible tamper).
* **Cross‑validate fail:** reject, mark token suspicious, alert.
* **TTL expired:** request new token (retry flow).

---

## Uygulama Checklist (Mühendisler için)

* [ ] Canonical serialization spec hazır.
* [ ] BTK & CA HSM entegrasyonu (PKCS#11/KMIP) planlandı.
* [ ] Algoritma seti belirlendi ve kütüphaneler seçildi.
* [ ] Token ve blob JSON şablonları dokümante edildi.
* [ ] Used‑token store (atomic mark-used) implement edildi.
* [ ] TTL, allowed clock skew ve retry policy test edildi.
* [ ] Audit & alerting mekanizmaları hazır.

---

## Son Notlar

Bu belge PoC → Prod geçişi için doğrudan kullanılabilir. İstersen PoC kodu (Python + `cryptography`/`pynacl`) veya `canonical_serialize` örneği de oluşturabilirim ve ayrı bir `README.md` ile birlikte proje klasörüne koyabilirim.

---

*Oluşturan: ChatGPT — "BTK Token Production & Verification" canonical dokümanı.*
