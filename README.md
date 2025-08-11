# Tek kullanımlık Geçici Anahtar ile E-İmza Güvenlik Modeli

**Hazırlayan:** Güven Acar
**Tarih:** 11.08.2025 
---
Bu belge üç tür E-imza oluşturma protokolü önerisi içermektedir.
**1- Tüm sürecin E-devlet tarafından yönetildiği merkeziyetçi model**
**2- Docker Tabanlı Geçici E-İmza Protokolü**
**3- Hibrit Model**
---


## Tüm sürecin E-devlet tarafından yönetildiği merkeziyetçi model.

**1. Özet**
Bu öneri, e-imza süreçlerinde **kalıcı özel anahtar (private key)** saklama riskini ortadan kaldırmak ve imza güvenliğini artırmak amacıyla geliştirilmiştir.  
Temel fikir: Onay anahtarı yalnızca tek kullanımlı olarak e-devlet tarafından üretilir. Onay anahtarı bir private key değildir ancak aynı işleve sahiptir. Tek kullanımlıktır.

---

**2. Amaç**
- Kalıcı anahtar hırsızlığı riskini ortadan kaldırmak
- Kimlik sahteciliğini önlemek
- Kullanıcı güvenini artırmak
- Yasal altyapıya uyumlu, pratik ve hızlı bir çözüm sağlamak

---

**3. Sistem Bileşenleri**
1. **E-Devlet Sunucusu**
   - Kullanıcı giriş doğrulaması
   - Geçici Onay/Genel (public) anahtar üretimi. (Onay anahtarı bir private key değildir ancak benzer bir işlev görür)
   - Anahtar süresi takibi ve imhası
2. **BTK**
   - Kişiye özel 30 saniyelik jeton (token) üretimi
   - Sertifika ve imza sürecinde köprü otorite
3. **E-İmza Sağlayıcı Firma**
   - Sertifika üretimi (CSR modelinde)
   - HSM üzerinde imzalama
4. **Kullanıcı Cihazı**
   - Web veya mobil erişim
   - İki faktörlü onay (mobil doğrulama)

---

**4. İşleyiş Adımları**

**1. Kullanıcı Talebi**  
Kullanıcı e-Devlet hesabına giriş yapar ve "E-imza oluştur" butonuna tıklar.

**2. Token Üretimi**  
E-Devlet sunucusu BTK sunucusunu bilgilendirir. BTK sunucusu kişiye özel 30 saniyelik bir jeton üretir ve bunu hem E-devlet sunucusuna hem de 
E-imza sağlayıcı firmaya gönderir.

**3. Geçici Anahtar Üretimi**  
E-Devlet sunucusu:
- Kullanıcı için geçici Onay/Genel anahtar çifti üretir.
- Onay anahtarı sadece, genel anahtar ile paketlenmiş e-imza içeriğini açmak ve teyit etmek için kullanılır. (Private key işlevi görür)
- Tek kullanımlıktır. Bir saldıranın eline geçse bile bir işine yaramaz.

**4. Sertifika Talebi**  
Kullanıcının genel anahtarı ve kimlik bilgileri, BTK jetonu ile birlikte BTK’ya iletilir. BTK elindeki bilgilerle E-imza veren firmaya
bir istek yollar, firma kişiye özel üretilmiş BTK jetonunu, BTK'ye iletir. BTK her iki jetonu karşılaştırıp doğruladıktan sonra elindeki bilgileri
E-imza firmasına verir.

**5. Sertifika Üretimi**  
E-imza firması:
- BTK üzerinden bilgileri alır.
- HSM kullanarak sertifikayı üretir.
- Sertifikayı kullanıcıya ait genel imza ile şifreleyerek BTK’ya verir.

**6. İmza Oluşturma**  
E-Devlet:
- Elindeki geçici onay anahtarı ile şifrelenmiş bilginin doğrulunu kanıtlar ve sertifika veren firmanın sertifika bilgileri açığa çıkar.
- Kullanıcıya sertifika bilgilerini iletir.

---

**7. Güvenlik Avantajları**
- **Anahtar sızıntısı riski yok**  
- **Kalıcı veri tutulmuyor** → saldırganın çalabileceği bir şey yok  
- **Ek mobil doğrulama ile yetkisiz kullanım engellenir**  
- **Revocation (Onay anahtarı, özel anahtar imhası) gerektirmez** → süre dolunca otomatik geçersiz

---

**6. Sonuç**
Geçici anahtar yaklaşımı, hem vatandaş hem devlet için maksimum güvenlik ve minimum operasyonel yük sağlar.

---------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------



## 2- Docker Tabanlı Geçici E-İmza Protokolü


**1. Giriş: Mevcut E-İmza Sisteminin Problemleri**

Fiziksel Bağımlılık: USB token taşıma ve kaybetme riski.

Merkezi Zafiyetler: Kurumların insan veya süreç hatalarından kaynaklanan güvenlik skandalları.

Yüksek Risk: Kalıcı anahtar hırsızlığı ve sahtecilik potansiyeli.

Kullanıcı Deneyimi: Kurulum ve kullanım süreçlerinin karmaşıklığı.

**2. Çözüm Önerisi: Geçici Anahtar Mimarisi**
Bu protokol, e-imza süreçlerinde kalıcı anahtar saklama riskini ortadan kaldırmak için, anahtarın merkezi sunucular yerine kullanıcının kendi cihazında güvenli bir ortamda (Docker) üretilmesini ve kullanılmasını önerir.

Temel Fikir: Kullanıcının özel anahtarı, sadece işlem anı için, izole bir Docker ortamında üretilir ve işlem biter bitmez geri döndürülemez şekilde imha edilir.

**3. Protokolün Temel İlkeleri**
Dağıtık Anahtar Kontrolü: Özel anahtar, kullanıcının cihazında üretilir. Bu, yasal olarak zorunlu olan "anahtarın tek sahibi kullanıcıdır" ilkesine tam uyum sağlar.

Geçici Anahtar Ömrü: Anahtarlar, 1-2 dakikalık kısa bir ömre sahiptir.

Merkeziyetten Arındırılmış Güven: Anahtar üretim süreci, tek bir merkezi sunucunun değil, milyonlarca dağıtık cihazın sorumluluğundadır.

**4. Sistem Bileşenleri ve Rolleri**
Bileşen	Rolü
Kullanıcı Cihazı (Docker) Özel anahtarın üretildiği, saklandığı ve imza işleminin gerçekleştiği güvenli ve izole ortam.

**BTK:** Güvenilir bir zaman ve token otoritesi. Tüm sistem için tek kullanımlık, zaman damgalı token'lar üretir.
**E-İmza Sağlayıcı** (CA) Sertifika taleplerini işler, doğrulanmış özel anahtarlar için sertifika üretir.
**E-Devlet** Kullanıcının kimliğini doğrulayan ve imza sürecini başlatan hizmet platformu.


**5. Protokolün İşleyiş Adımları**
1. İşlem ve Token Talebi

Kullanıcı E-Devlet'e girer ve bir e-imza işlemi başlatır.

E-Devlet, BTK'dan işlem için geçerli bir token talep eder.

2. Token Üretimi ve İletimi

BTK, zaman damgalı, tek kullanımlık bir token üretir ve bunun bir kopyasını E-Devlet'e diğer kopyasını E-imza veren firmaya iletir.

E-Devlet, bu token'ı kullanıcı cihazına gönderir.

3. Docker’da Anahtar Üretimi

Kullanıcı cihazı, aldığı token ile izole bir Docker konteyneri başlatır.

Bu konteyner içinde geçici özel/genel anahtar çifti üretilir.

4. Sertifika ve İmza Talebi

Konteyner, ürettiği genel anahtarı, kimlik bilgilerini ve BTK token'ını, BTK kanalıyla E-İmza sağlayıcısına iletir.

Bu iletişim, BTK üzerinden güvenli bir şekilde gerçekleşir.

5. Sertifika Üretimi ve İmza

E-İmza firması, BTK'dan gelen token ve genel anahtar bilgilerini doğrulayarak sertifikayı üretir.

üretilmiş sertifika kullanıcıya ait genel anahtar ile şifrelenir. Tekrar BTK tarafından kullanıcının konteyner alanına gönderilir.

Konteyner alanında kullanıcının özel imzasıyla şifreli paket açılır ve E-imza ortaya çıkar

6. Anahtarın ve Konteynerin İmhası

İmza işlemi tamamlandıktan sonra, Docker konteyneri ve içindeki tüm veriler (özel anahtar dahil) anında ve geri döndürülemez şekilde silinir.

6. Güvenlik Avantajları ve Yasal Uygunluk
Merkezi Zafiyet Riskinin Yok Edilmesi: En yıkıcı saldırı türü olan merkezi sunucu hackleme riski ortadan kalkar.

Fiziksel Token Zorunluluğu Yok: Kullanıcılar için yüksek pratiklik ve maliyet avantajı sunar.

Hukuki Uyumluluk: Özel anahtar kullanıcı cihazında üretildiği için, 5070 sayılı Kanun'un temel ilkelerine uygundur.

Sahte İmza Skandallarına Çözüm: Bu model, bir anahtarın sahte olarak üretilip verilmesi gibi süreç zafiyetlerini ortadan kaldırır.

7. Sonuç ve Öneri
Bu protokol, İsveç ve Estonya gibi dijitalleşme konusunda lider ülkelerin benimsediği "anahtarı kullanıcının cihazında tutma" prensibiyle örtüşmektedir.

Mevcut e-imza sisteminin güvenlik ve pratiklik zafiyetlerini gideren, hukuki zemini sağlam ve geleceğe dönük bir çözümdür. Bu nedenle, değerlendirilmek üzere pilot bir proje olarak sunulması önerilmektedir.

---------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------

## 3- Hibrit model

bu model her iki modelin avantajları kullanılmak üzere önerilmiştir. 

Hibrit modelde tüm süreç konteyner (docker) modeline paralel yürütülür. konteyner modeline ek olarak kullanıcı isterse işlemi 
kendi özel ve genel anahtarları ile başlatabilir. Konteynerde onaylama kullanıcının özel anahtarı ve genel anahtarları ile yapılır. 

---------------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------

## E-İmza Sistemleri: Docker, E-Devlet ve Hibrit Modeller Karşılaştırması


---

**1. Giriş**  
Elektronik imza süreçlerinde güvenlik, kullanım kolaylığı ve yasal uygunluk büyük önem taşımaktadır.  
Bu sunumda üç farklı model incelenmiş ve karşılaştırılmıştır:

- Docker Tabanlı Geçici Anahtar Modeli  
- E-Devlet Tabanlı Merkezi Anahtar Modeli  
- Hibrit Model (Docker + E-Devlet)

---

**2. Docker Tabanlı Geçici Anahtar Modeli**  

**Özellikler** 
- Özel anahtar kullanıcının cihazında, izole Docker ortamında üretilir.  
- Anahtar sadece işlem süresince var olur, işlem sonrası anında imha edilir.  
- Merkezi risk ortadan kalkar, yasal uygunluk tamdır.  
- Kullanıcı anahtarı tamamen kendi kontrolündedir.

**Avantajlar** 
- Merkezi sunucu saldırılarına karşı yüksek direnç.  
- Fiziksel token taşıma zorunluluğu yok.  
- Yasal mevzuata tam uyum sağlar.

**Dezavantajlar** 
- Docker kurulumu ve işletimi bazı kullanıcılar için teknik olabilir.  
- Merkezi token/timestamp otoritesi gerektirir.

---

**3. E-Devlet Tabanlı Merkezi Anahtar Modeli**

**Özellikler**  
- Özel anahtar E-Devlet sunucusunda üretilir ve kullanılır.  
- Kullanıcı anahtar kontrolüne sahip değildir.  
- İşlem sonrasında anahtar sunucu tarafından imha edilir.

**Avantajlar** 
- Kullanıcı için basit ve hızlı kullanım.  
- Fiziksel cihaz veya özel ortam gerektirmez.

**Dezavantajlar**  
- Merkezi sunucu tekil zafiyet oluşturur.  
- Kullanıcı anahtar kontrolü yok, yasal risk taşır.  
- Sunucu ele geçirilirse sistem tehlikeye girer.

---

**Hibrit Model (Docker + E-Devlet)**  

**Yapı**  
- E-Devlet modeli işlem başlatmada ve token yönetiminde kullanılır.  
- Gerçek imzalama işlemi Docker konteynerinde, kullanıcı cihazında yapılır.  
- Kullanıcı isterse kendi kalıcı private key’ini sisteme ekleyebilir.

**Avantajlar**  
- E-Devlet modelinin pratikliği ve Docker modelinin güvenliği birleşir.  
- Esnek ve kullanıcı tercihlerine uygun.  
- Yasal ve güvenlik riskleri minimize edilir.

**Dezavantajlar**  
- İki sistemin entegrasyonu karmaşık olabilir.  
- Kullanıcı eğitimi ve sistem desteği gerekir.

---

**5. Model Karşılaştırma Tablosu**

| Özellik                      | Docker Modeli                  | E-Devlet Modeli              | Hibrit Model                  |
|------------------------------|--------------------------------|------------------------------|-------------------------------|
| Özel Anahtar Üretimi         | Kullanıcı cihazında (Docker)   | Merkezi sunucuda (E-Devlet)  | Docker + E-Devlet ortak       |
| Anahtarın Kullanıcı Kontrolü | Tam kontrol                    | Yok                          | Tam / kısmi kontrol           |
| Güvenlik Seviyesi            | Çok yüksek                     | Düşük - Orta                 | Yüksek                        |
| Kullanıcı Deneyimi           | Teknik bilgi gerekebilir       | Çok kolay                    | Orta                          |
| Yasal Uyumluluk              | Tam uyum                       | Riskli                       | Yüksek                        |
| Merkezi Risk                 | Yok                            | Var                          | Azaltılmış                    |
| Fiziksel Cihaz Gereksinimi   | Yok                            | Yok                          | Opsiyonel                     |

---

**6. Öneriler ve Sonuç**

- **Kurumsal güvenlik öncelikli ise:** Docker modeli tercih edilmeli.  
- **Hızlı ve kolay kullanım gerekirse:** E-Devlet modeli değerlendirilebilir, ancak yasal riskler göz önünde bulundurulmalı.  
- **En esnek ve güvenli yaklaşım:** Hibrit model, hem güvenlik hem kullanım kolaylığı sağlar.  
- Pilot projede tüm modeller denenip, kullanıcı geri bildirimi ile nihai karar alınabilir.

---

**7. İletişim**  
Sorularınız ve detaylı bilgi için:  

## Güven Acar  
guvenacar@gmail.com
