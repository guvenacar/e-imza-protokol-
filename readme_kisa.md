# Ephemeral E-İmza Projesi


**Dijital imzalarda paradigma değişimi: Kullanıcılar işlemi onaylar, CA onlar adına imzalar.**  
Ephemeral E-İmza Sistemi, dünyada henüz hiçbir ülkede uygulanmayan, Türkiye’yi dijital imza teknolojisinde takip eden değil takip edilen ülke konumuna taşıyacak devrimsel bir protokoldür. Bugün Türkiye’de e-imza kullanım oranı %5’in altında olup, vatandaşlar yüksek donanım maliyetleri (500-1500 TL) ve karmaşık süreçler nedeniyle e-imzadan uzak durmaktadır. Mevcut modelde yıllarca geçerli anahtarların çalınması hâlinde geçmiş ve gelecek tüm işlemler tehlikeye girebilmektedir. Ephemeral E-İmza ile bu paradigma tamamen değişmektedir: Kullanıcılar artık belgeleri imzalamak zorunda değildir, yalnızca işlemi onaylar; belgeyi bu işleme özel tek kullanımlık anahtar ile kullanıcı adına imzalayan CA’dır. Böylece her işlem birbirinden izole edilir, güvenlik ihlalleri yalnızca ilgili işlemi etkiler ve donanım maliyeti tamamen ortadan kalkar. Bu yaklaşım, mevcut kamu altyapılarıyla ek maliyet oluşturmadan 80 milyon vatandaşın e-imza sahibi olmasını mümkün kılar, e-Devlet ile entegre bir şekilde hızla devreye alınabilir ve Türkiye’yi bu alanda dünya lideri yapabilecek bir fırsat sunar.  


![Protokol Akış Diyagramı](images/model_1B_diyagram_TR.png)

## 🚀 Devrimsel Yaklaşım

**Bu geleneksel bir e-imza sistemi DEĞİLDİR.** Temel bir paradigma değişimi sunuyoruz:

- **Kullanıcılar belge imzalamaz** — işlemleri onaylar
- **CA kullanıcı adına imzalar** — işleme özel geçici anahtarlarla  
- **Her imza benzersizdir** — sadece TEK bir işlem için geçerli
- **Sıfır donanım yükü** — taşınacak, korunacak veya kaybedilecek cihaz yok
- **Mükemmel izolasyon** — ele geçirilme sadece o tek işlemi etkiler

## ✨ Temel Paradigma Değişimleri

### Geleneksel E-İmza
- Kullanıcı kalıcı özel anahtara sahip
- Kullanıcı doğrudan belge imzalar  
- Bir anahtar ele geçirilmesi = TÜM işlemler risk altında
- Donanım cihazı gerekli (500-1.500 TL maliyet)
- Teknik bilgi gerekli

### Bizim Ephemeral Yaklaşımımız  
- **Belge imzalama değil, işlem onaylama**
- CA her işlem için anahtar üretir
- Kullanıcı itiraz edilemez onay verir
- CA kullanıcı adına ephemeral anahtar ile imzalar
- Geçmiş ve gelecek işlemler güvende kalır
- Sıfır donanım yatırımı

## 🎯 Temel Yenilik

```
Geleneksel Akış:
Kullanıcı → [Kalıcı Anahtar] → Belge İmzala → Gönder

Ephemeral Akış:  
Kullanıcı → İşlemi Onayla → CA → [Geçici Anahtar Üret] → İmzala → [Anahtarı Yok Et]
```

**Kullanıcının onayı kriptografik olarak bağlayıcı ve itiraz edilemezdir**, ancak imzalama anahtarına hiç dokunmaz. Bu, yasal geçerliliği korurken anahtar yönetimi yükünü ortadan kaldırır.

## 🔒 Güvenlik Garantileri

- ✅ **İşleme bağlı imzalar** — her imza belirli belge + kurum + zaman damgasına kilitli
- 🚫 **Tekrar saldırısı yok** — tokenler, nonce'lar ve geçici anahtarlar yeniden kullanımı önler
- ⏱️ **Otomatik süre sonu** — anahtarlar ve sertifikalar dakikalar içinde sona erer
- 🔐 **İleri güvenlik** — mevcut işlem ele geçirilse bile geçmiş işlemler güvende kalır
- 📱 **Vatandaş dostu** — karmaşık kriptografi kullanıcılardan gizlenir

## 📋 Uygulama Modelleri

### 🏛️ [Model 1: Merkezi E-Devlet](docs/centralized-model.md)
- Tüm işlemler devlet altyapısı tarafından yönetilir
- Yaşlı, teknik bilgisi olmayan kullanıcılar için mükemmel
- Kullanıcı sadece mevcut kimlik bilgileriyle giriş yapar

### 🖥️ [Model 2: İzole Çalışma Alanı](docs/isolated-workspace.md)  
- Onay için yerel sandbox ortamı
- Sıfır karmaşıklıkla maksimum güvenlik
- CA yine de gerçek imzalamayı yapar

### 🔄 [Model 3: Hibrit Geçiş](docs/hybrid-model.md)
- Eski ve yeni sistemler arasında köprü
- Mevcut e-imza sahipleri geride bırakılmaz
- Kademeli geçiş yolu

### 🌐 [Model 4: Çevrimiçi Kayıt](docs/online-enrollment.md)
- Fiziksel varlık olmadan e-imza edinin
- Dakikalar içinde süreç tamamlanır
- Seyahat dostu çözüm

## 💡 Gerçek Dünya Etkisi

### Vatandaşlar İçin
- **Donanım maliyeti yok** — kişi başı 500-1.500 TL tasarruf
- **Taşınacak bir şey yok** — USB token veya akıllı kart yok
- **Kaybedilecek bir şey yok** — korunacak fiziksel cihaz yok
- **Evrensel erişim** — yaşlıdan teknoloji meraklısına herkes dahil

### Toplum İçin  
- **80+ milyon vatandaş** anında e-imza yeteneğine sahip
- **İmzasız işlem döneminin sonu** — her resmi işlem kriptografik olarak güvenli
- **Dolandırıcılığın ortadan kalkması** — işlem inkârı imkansız hale gelir
- **Gerçek dijital kapsayıcılık** — kimse geride kalmaz

## 🛠️ Teknik Mimari

```
Kullanıcı Cihazı     Kurum              NTA              CA (Kullanıcı Adına İmzalar)
     │                 │                 │                        │
     ├─İşlemi Onayla───►                 │                        │
     │                 ├──İşlem Talebi───►                        │
     │                 │                 ├──Oturum Belirteci──────►
     │                 │◄────────────────┤                        │
     │                 ├──uPub+Token─────────────────────────────►
     │                 │                                          │
     │◄────────────────┼──────────Onay Sorgusu───────────────────┤
     ├─Onay Kodu───────┼──────────────────────────────────────────►
     │                 │                                          │
     │                 │                           [Geçici Anahtar Üret]
     │                 │                           [Belgeyi Kullanıcı Adına İmzala]
     │                 │                           [Anahtarı Hemen Yok Et]
     │                 │                                          │
     │                 │◄────────İmzalı Belge + Sertifika─────────┤
     │◄──Başarılı──────┤                                          │
```

## 🔑 Neden Bu Önemli

**"Kullanıcı onaylar, CA imzalar"** — Bu temel değişim:

1. **Vatandaşlardan anahtar yönetimi yükünü kaldırır**
2. **İtiraz edilemez onay ile yasal geçerliliği korur**
3. **İşlem izolasyonu ile üstün güvenlik sağlar**  
4. **Teknik engeller olmadan evrensel benimsemeyi sağlar**
5. **Kuantum bilgisayar tehditlerine karşı geleceği güvence altına alır**

## 📚 Dokümantasyon

- 📜 [Yasal Çerçeve ve Uyumluluk](docs/legal-framework.md)
- 🔒 [Güvenlik Analizi ve Tehdit Modeli](docs/security-analysis.md)  
- 🔧 [Tek Kullanımlık Anahtar Protokolü](docs/single-use-key.md)
- 🌍 [Uluslararası Standartlar Uyumu](docs/standards.md)

## 🎯 Vizyon

Şöyle bir dünya hayal ediyoruz:
- Her vatandaş varsayılan olarak e-imza yeteneğine sahip
- Kimsenin donanım tokeni satın alması, taşıması veya koruması gerekmiyor  
- İşlem güvenliği izolasyon sayesinde mutlak
- Dijital kapsayıcılık gerçekten evrensel

**Bu sadece bir iyileştirme değil — dijital imzaların nasıl çalışması gerektiğinin tamamen yeniden tasarlanmasıdır.**

---

**Yazar:** Güven Acar  
**İletişim:** guvenacar@gmail.com  
**ORCID:** [0009-0000-4232-7405](https://orcid.org/0009-0000-4232-7405)  
**Lisans:** MIT

---

*"Paradigmayı 'kullanıcılar belge imzalar'dan 'kullanıcılar onaylar, CA imzalar'a kaydırıyoruz — e-imzaları gerçekten evrensel hale getiriyoruz."*