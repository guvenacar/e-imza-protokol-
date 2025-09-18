# Model 1: Tüm Resmi İşlemlerde E-İmza Dönemi (Tüm Vatandaşların E-İmza Sahibi Olması)

## Model 1A — Kayıt (uPub Yoksa)

1. **User → Institution:** Request
2. **Institution → E-Gov:** Request uPub
3. **E-Gov → NTA:** Request Tx
4. **NTA → E-Gov & CA:** Session token
5. **E-Gov → CA:** User Auth + Request uPub
6. **—**
7. **CA → E-Gov:** Response uPub
8. **E-Gov → Institution:** uPub

![Model 1A](sandbox:/mnt/data/cc39e65f-d065-4feb-b8cd-1c4b1b013147.png)

---

## Model 1B — Rutin İşlem (uPub Var)

1. **User → Institution:** Request
2. **Institution → NTA:** Tx Request
3. **NTA → Institution & CA:** Session Token
4. **Institution → CA:** uPub
5. **Institution → CA:** Cede Request
6. **User → CA:** “Enter Code” (manual OTP entry)
7. **CA → Institution:** Signed Document + Ephemeral Certificate
8. **CA → User:** Transaction Summary + OTP (Approval Code)

![Model 1B](sandbox:/mnt/data/5a0c8d58-0402-42ae-9938-8fd55057bdc1.png)

---

# Model 2 — İzole Çalışma Alanı Protokolü

1. **User → Institution:** Request (Two-way auth)
2. **Institution → CA:** Request uPub + CAPub
3. **Institution → User Isolated Workspace:** uPub + CAPub (provision)
4. **Institution → NTA:** Tx Request
5. **NTA → Institution & CA:** Session Token
6. **CA → Institution:** uPub + CAPub (authoritative copy)
7. **CA (Ephemeral Key Service):** Generates ephemeral nPub per request
8. **Sign Ephemeral nPub → User Isolated Workspace:** Signed ephemeral nPub delivered
9. **CA → Institution:** Ephemeral Certificate

![Model 2 — Isolated Workspace Protocol](sandbox:/mnt/data/9a7566ec-b6bf-4477-9a8b-ec088b6080d7.png)

---

# Model 3 — Hibrit (Geçiş) Modeli

1. **User → Institution:** Request
2. **Institution → User (Digital Sign):** Institution e-Sign Request
3. **User → Institution:** Signed Document
4. **Institution → NTA:** Request
5. **NTA → Institution & CA:** Session Token
6. **Institution → CA:** NTA Session Token + uPub
7. **CA → Institution:** Ephemeral Certificate
8. **Institution → User:** Success message

![Model 3 — Hybrid (Transition) Model](sandbox:/mnt/data/6b2ae5f8-54a1-4b61-b701-fbb91f3ab23d.png)

---

# Model 4 — Çevrim İçi E-İmza Kayıt Süreci (Online e-Signature Enrollment Process)

1. **User → E-Government:** Request Digital Sign
2. **E-Government → NTA:** Request Tx
3. **NTA → E-Government, CA, User:** Session token
4. **E-Government → CA:** Encrypt user data + sign with E-Gov key
5. **CA → User Isolated Workspace:** Qualified user certificate delivered

![Model 4 — Online e-Signature Enrollment Process](sandbox:/mnt/data/e903d74c-b581-4277-b3d6-3f405c365db1.png)
