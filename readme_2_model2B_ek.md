# Model 2B - İzole Alanlı E-İmza Protokolü

## Ön Hazırlık: Model 2A - İlk Kayıt Süreci

```
1. Kullanıcı → e-Devlet: Dijital imza başvurusu

2. e-Devlet: Kimlik doğrulama (biyometrik, kimlik kontrolü)

3. e-Devlet → Kullanıcı: İzole alan uygulamasını indir

4. Kullanıcı Cihazı: 
   • İzole alan oluşturulur (TEE/Secure Enclave)
   • uPriv/uPub üretilir (Hardware-backed)
   • Device binding (IMEI, Hardware ID)
   • Kullanıcı güvenlik tercihlerini ayarlar:
     - PIN belirleme (6+ haneli, kurum PIN'inden farklı)
     - Biyometrik kayıt (parmak izi + yüz tanıma)
     - Zaman kısıtlamaları (örn: 09:00-18:00)
     - Lokasyon kısıtlamaları (ofis WiFi, ev WiFi)
     - İşlem limitleri

5. Kullanıcı: CA seçimi yapar

6. e-Devlet → BTK: Kayıt işlemi başlatma

7. BTK → e-Devlet, CA, Kullanıcı: İşlem jetonu

8. e-Devlet → CA (HTTPS/TLS):
   • Kullanıcı kimlik bilgileri
   • uPub
   • Kullanıcı güvenlik tercihleri
   • e-Devlet dijital imzası

9. CA:
   • Kullanıcıyı kaydeder (uPub + tercihleri)
   • uCert oluşturur
   • CAPub hazırlar

10. CA → Kullanıcı İzole Alanı: 
    • uCert
    • CAPub (kalıcı olarak saklanır)

11. Kayıt tamamlandı
```

---

## Model 2B - İşlem Aşaması (Revize)

### Adım 1: İşlem Başlatma
```
Kullanıcı → Kurum (Banka/e-Devlet/Tapu vb.):
• İşlem talebi gönderir
```

### Adım 2: Belge Gönderimi
```
Kurum → Kullanıcı:
• İşlenecek belge
• Belge HASH'i
• İşlem detayları (tutar, tür, vb.)
```

### Adım 3: Kullanıcı Onay Talebi
```
Kullanıcı → Kurum:
• uPub
• "İşlemi onaylıyorum" talebi
```

### Adım 4: Token Talebi
```
Kurum → BTK:
• İşlem başlatma talebi
• Kurum ID
• İşlem tipi
• CA ID (kullanıcının seçtiği)
• İşlem metadata (timestamp, ip, lokasyon)
```

### Adım 5: Token Üretimi ve Dağıtımı
```
BTK → Kurum & CA:
• İşlem jetonu = {
    token_id,
    kurum_id,
    ca_id,
    timestamp,
    TTL (5 dakika),
    kullanım_durumu: false
  } + BTK_imza
```

### Adım 6: CA'ya İşlem İletimi
```
Kurum → CA (HTTPS/TLS):
• uPub
• Belge HASH'i
• token_id
• İşlem metadata (timestamp, ip, wifi_mac)
```

### Adım 7: CA Kontrolleri ve Anahtar Üretimi
```
CA İşlemleri:

a) Token Doğrulama:
   • BTK imzasını kontrol et
   • TTL geçerli mi?
   • Daha önce kullanılmış mı?

b) Kullanıcı Tercihleri Kontrolü:
   • Zaman kısıtlaması kontrol (saat uygun mu?)
   • Lokasyon kısıtlaması kontrol (WiFi/IP uygun mu?)
   • İşlem limiti kontrol (günlük limit aşılmış mı?)
   • Tutarsızlık varsa → BLOKE + Kullanıcıya bildirim

c) Kullanıcı Tanımlama:
   • uPub ile kullanıcıyı veritabanında bul
   • uCert durumu kontrol (iptal edilmiş mi?)
   • Kullanıcı blokeli mi?

d) Anahtar Üretimi:
   • Tek kullanımlık (sPriv, sPub) üret (HSM içinde)
   • sCert oluştur = {
       sPub,
       user_id,
       token_id,
       timestamp,
       TTL: 5 dakika,
       işlem_türü
     } + CAPriv_imza

e) Paketleme:
   • packet = (sPriv, sPub, sCert, token_id, timestamp)
   • encrypted_packet = RSA_Encrypt(packet, uPub)
   • signature = RSA_Sign(encrypted_packet, CAPriv)

CA → İzole Alan (HTTPS/TLS):
• encrypted_packet
• signature
```

### Adım 8: İzole Alan Kontrolleri ve İmzalama
```
İzole Alan İşlemleri:

a) Güvenlik Kontrolleri:
   • Biyometrik doğrulama (parmak izi + liveness detection)
   • PIN doğrulama (3 hatalı → bloke)
   • Zaman kısıtlaması kontrol (kullanıcı tanımlı)
   • Lokasyon kısıtlaması kontrol (WiFi MAC adresi)
   • Davranışsal biyometri kontrol

b) Paket Doğrulama:
   • CA imzasını CAPub ile doğrula
   • Timestamp kontrol (replay attack)
   • Token_id geçerliliği

c) Paket Açma:
   • packet = RSA_Decrypt(encrypted_packet, uPriv)
   • (sPriv, sPub, sCert, token_id, timestamp) çıkar

d) Sertifika Doğrulama:
   • sCert'i CAPub ile doğrula
   • TTL kontrol
   • Token_id eşleşmesi

e) Belge Gösterimi:
   • Kullanıcıya belge içeriği göster
   • "Bu belgeyi imzalamak istiyor musunuz?" onay al

f) İmzalama:
   • signature = RSA_Sign(HASH, sPriv)
   • sPriv'i bellekten secure erase (opsiyonel)

İzole Alan → Kurum (HTTPS):
• signature
• sPub
• sCert
• token_id
• işlem_metadata
```

### Adım 9: Kurum Doğrulamaları
```
Kurum İşlemleri:

a) Token Kontrolü:
   • BTK API ile token_id doğrula
   • Tek kullanım kontrolü (daha önce kullanıldı mı?)
   • TTL geçerli mi?
   • Token'ı "kullanıldı" olarak işaretle

b) Sertifika Kontrolü:
   • sCert'i CA kök sertifikası ile doğrula
   • Sertifika geçerli mi?
   • TTL dolmamış mı?
   • Token_id eşleşiyor mu?

c) İmza Kontrolü:
   • signature'ı sPub ile matematiksel doğrula
   • HASH eşleşiyor mu?

d) İşlem Tamamlama:
   • Tüm kontroller başarılı → İşlem kaydet
   • İşlem logları oluştur
   • Kullanıcıya bildirim gönder

Sonuç: İşlem Tamamlandı
```

---

## Güvenlik Katmanları Özeti

```
1. Kullanıcı Katmanı:
   ├─ Biyometrik (parmak izi + yüz tanıma)
   ├─ Liveness detection
   ├─ PIN (6+ haneli, 3 hatalı → bloke)
   ├─ Davranışsal biyometri
   └─ Kullanıcı tanımlı kısıtlamalar (zaman/lokasyon)

2. İzole Alan Katmanı:
   ├─ Hardware-backed security (TEE/Secure Enclave)
   ├─ Device binding
   ├─ Attestation
   ├─ Root/jailbreak tespiti
   └─ Secure memory (uPriv asla dışarı çıkmaz)

3. Kriptografik Katman:
   ├─ uPub şifreleme (gizlilik)
   ├─ CAPriv imzası (bütünlük + kimlik doğrulama)
   ├─ TLS/HTTPS (iletim güvenliği)
   └─ Tek kullanımlık anahtarlar (izolasyon)

4. Token Katmanı:
   ├─ BTK koordinasyonu
   ├─ Tek kullanım garantisi
   ├─ TTL (5 dakika)
   └─ Merkezi denetim

5. Kurum Katmanı:
   ├─ 2FA (SMS/e-posta)
   ├─ Kurum şifresi
   ├─ Anormallik tespiti (AI)
   └─ İşlem limitleri

6. CA Katmanı:
   ├─ Kullanıcı tercihleri kontrolü
   ├─ HSM güvenliği
   ├─ Sertifika yönetimi
   └─ Denetim logları
```

---

## Zaman Çizelgesi

```
T+0ms:    Kullanıcı işlem başlatır
T+500ms:  Kurum belge gönderir
T+1s:     Kurum BTK'dan token alır
T+1.5s:   CA anahtar üretir ve gönderir
T+3s:     Kullanıcı biyometrik onay verir
T+3.5s:   İzole alan imzalar ve gönderir
T+4s:     Kurum doğrular ve kaydeder

Toplam süre: ~4 saniye
```

---

İşlem akışı hazır. Hangi bölüme ek açıklamalar eklemek istersiniz?