Ephemeral E-İmza Protokolü: Dijital İmzada Paradigma Değişimi
Versiyon: 1.0
Tarih: Ocak 2025
Yazar: Güven Acar
ORCID: 0009-0000-4232-7405

Yönetici Özeti
Ephemeral E-İmza Protokolü, dijital imza teknolojisinde radikal bir paradigma değişimi sunuyor. Temel yenilik: Kullanıcılar artık belge imzalamaz, yalnızca işlemleri onaylar. Belgeyi kullanıcı adına imzalayan, her işlem için özel olarak üretilen tek kullanımlık anahtarlarla Sertifika Otoritesi (CA)'dir.
Temel Avantajlar

Güvenlik: 3 yıllık risk penceresi → 30 saniyelik izole işlem
Maliyet: 500-1500 TL donanım maliyeti → Sıfır
Erişim: %5 kullanım oranı → %100 potansiyel
Kolaylık: Karmaşık teknik süreç → Basit onay kodu

Kritik Fark
Geleneksel SistemEphemeral SistemAnahtar 3 yıl geçerliAnahtar 30 saniye geçerliÇalınırsa tüm geçmiş/gelecek işlemler tehlikedeSadece o tek işlem etkilenirKullanıcı belgeyi imzalarKullanıcı işlemi onaylar, CA imzalarDonanım zorunluDonanım gereksiz

1. Giriş
1.1 Mevcut Durum ve Sorunlar
Türkiye'de elektronik imza kullanım oranı %5'in altındadır. Temel engeller:

Yüksek donanım maliyetleri (500-1500 TL)
Karmaşık teknik süreçler
Anahtar yönetimi zorlukları
Uzun vadeli güvenlik riskleri

1.2 Paradigma Değişimi
Ephemeral E-İmza Protokolü, temelden farklı bir yaklaşım sunuyor:
Geleneksel: Kullanıcı → Kalıcı Anahtar → Belge İmzala → Gönder
Ephemeral: Kullanıcı → İşlem Onayla → CA → [Geçici Anahtar] → İmzala → [Anahtar Yok]
Bu değişim, dijital imzayı herkes için erişilebilir, güvenli ve pratik hale getiriyor.

2. Teknik Mimari
2.1 Temel Bileşenler

Kullanıcı: İşlemi onaylayan taraf
Kurum: Hizmet veren taraf (banka, hastane, vb.)
E-Devlet: Kimlik doğrulama otoritesi
BTK (NTA): İşlem koordinasyon merkezi
CA: Sertifika ve imza otoritesi

2.2 Ephemeral Anahtar Yaşam Döngüsü
1. Anahtar Üretimi: İşlem başlangıcında (t=0)
2. Geçerlilik Süresi: 30 saniye
3. Kullanım Limiti: Tek işlem
4. İmha Zamanı: İşlem tamamlanır tamamlanmaz (t<30s)
5. Yedekleme: YOK (tasarım prensibi)
2.3 Güvenlik Katmanları

İşlem İzolasyonu: Her işlem kriptografik olarak izole
Zaman Kısıtı: 30 saniyelik dar pencere
Token Mekanizması: Tek kullanımlık session token
Çoklu Doğrulama: BTK + CA + E-Devlet üçlü kontrolü


3. Uygulama Modelleri
Model 1A: İlk Kayıt - Kullanıcı Genel Anahtarı Temini
Henüz sisteme kayıtlı olmayan vatandaşlar için başlangıç süreci.
Show Image
Süreç:

Vatandaş kuruma başvurur
Kurum e-Devlet üzerinden uPub talebinde bulunur
E-Devlet BTK'dan işlem başlatmasını ister
BTK tüm taraflara session token dağıtır
E-Devlet CA'dan uPub üretimini talep eder
CA kalıcı uPub üretir ve e-Devlet'e iletir
E-Devlet uPub'ı saklar ve kuruma bildirir

Not: Vatandaş hiçbir teknik işlem yapmaz, sadece kimliğini doğrular.
Model 1B: Rutin İşlem - Onay ve İmza Süreci
Sisteme kayıtlı kullanıcıların günlük işlemleri.
Show Image
Süreç:

Kullanıcı kuruma işlem talebi iletir
Kurum BTK'dan işlem başlatmasını ister
BTK session token dağıtır
CA kullanıcıya onay kodu gönderir
Kullanıcı onay kodunu girer
CA ephemeral anahtar üretir, belgeyi imzalar, anahtarı yok eder
İmzalı belge kuruma iletilir

Kritik: Kullanıcı sadece onay kodu girer, belgeyi CA imzalar.
Model 2: İzole Çalışma Alanı Protokolü
Maksimum güvenlik gerektiren profesyonel kullanım.
Show Image
Özellikler:

Docker benzeri izole ortam
Kullanıcı cihazında güvenli alan
nPub üretimi ve PoP (Proof of Possession)
İşlem sonrası otomatik temizlik

Süreç:

İzole ortam oluşturulur
Kullanıcı uPriv ile nPub'ı imzalar (sahiplik kanıtı)
CA bu kanıtı doğrular
CA ephemeral anahtar ile belgeyi imzalar
İzole ortam temizlenir

Model 3: Hibrit (Geçiş) Modeli
Mevcut e-imza sahipleri için yumuşak geçiş.
Show Image
Özellikler:

Mevcut e-imza cihazları kullanılabilir
Kullanıcı hash onayı verir (belgenin kendisini değil)
CA ephemeral imza atar
Geriye dönük uyumluluk

Model 4: Çevrimiçi Kayıt Süreci
Fiziksel başvuru gerektirmeyen online kayıt.
Show Image
Süreç:

E-Devlet üzerinden başvuru
Online kimlik doğrulama
İzole ortamda anahtar üretimi
CA'dan işlem onaylama yetkisi
Sistem kullanıma hazır


4. Güvenlik Analizi
4.1 Tehdit Modeli
TehditGeleneksel RiskEphemeral ÇözümAnahtar çalınması3 yıl tüm işlemler tehlikedeSadece 30 saniye, tek işlemReplay saldırısıMümkünToken + TxID + timestamp ile imkansızMan-in-the-middleOrta riskTLS 1.3 + mutual auth ile minimalKimlik sahteciliğiYüksekE-Devlet + CA + BTK üçlü kontrol
4.2 Matematiksel Güvenlik
Collision olasılığı:

Session token: 256-bit rastgele → 2^-256
TxID: 128-bit unique → 2^-128
Zaman penceresi: 30 saniye
Toplam güvenlik: Pratik olarak imkansız

4.3 Kriptografik Standartlar

İmza Algoritması: ECDSA P-256 veya RSA-2048
Hash Fonksiyonu: SHA-256
Şifreleme: AES-256-GCM
TLS: Minimum 1.3


5. Performans ve Ölçeklenebilirlik
5.1 Hedef Metrikler
MetrikHedefMevcut Sistemlerİşlem süresi< 3 saniye15-30 saniyeEşzamanlı işlem10,000 TPS100 TPSAnahtar üretimi< 100msN/ASistem uptime%99.99%99.9
5.2 Maliyet Analizi
ParametreGelenekselEphemeralTasarrufDonanım/kullanıcı1000 TL0 TL%100İşlem maliyeti10 TL0.50 TL%95Kurulum süresi3-5 gün5 dakika%99Yenileme periyodu3 yılYok∞

6. Hukuki Uyumluluk
6.1 Ulusal Mevzuat
5070 Sayılı E-İmza Kanunu:

Madde 3: "Güvenli elektronik imza" tanımına uygun ✓
Madde 5: Elle atılan imza ile eşdeğerlik sağlanıyor ✓
Madde 15: Zaman damgası entegre ✓

BTK Düzenlemeleri:

Nitelikli Elektronik Sertifika şartları karşılanıyor
ESHS standartlarına uyumlu

6.2 Uluslararası Standartlar

eIDAS: AB elektronik kimlik düzenlemelerine uyumlu
Common Criteria: EAL4+ seviyesi hedefleniyor
ISO 27001: Bilgi güvenliği yönetim sistemi uyumlu


7. Kullanım Senaryoları
Senaryo 1: Emekli Maaşı - Yaşlı Dostu
1. ATM'ye TC kimlik ile giriş
2. "Maaş çek" seçeneği
3. Telefona gelen 6 haneli kodu ATM'ye girme  
4. İşlem tamamlandı
   (Arka planda: CA ephemeral imza atmış)
Kullanıcı deneyimi: Bugünkü ATM işleminden farksız
Senaryo 2: Kurumsal Transfer - Yüksek Güvenlik
1. Şirket yöneticisi transfer talebi oluşturur
2. Sistem belge hash'ini gösterir
3. Yönetici mobil uygulamadan hash'i onaylar
4. CA ephemeral anahtar ile imzalar
5. Transfer gerçekleşir
Güvenlik: Her transfer izole, replay imkansız
Senaryo 3: Hastane Randevusu - Evrensel Erişim
1. Hasta MHRS'den randevu alır
2. SMS ile onay kodu gelir
3. Kodu sisteme girer
4. Randevu kesinleşir ve imzalanır
Kapsayıcılık: Akıllı telefonu olan herkes kullanabilir

8. Geçiş Stratejisi
Faz 1: Pilot (0-6 ay)

3 kamu kurumu
10,000 kullanıcı
Model 1 testi

Faz 2: Genişleme (6-12 ay)

Bankalar dahil
100,000 kullanıcı
Model 2 ve 3 devreye

Faz 3: Yaygınlaştırma (12-24 ay)

Tüm kamu kurumları
1 milyon+ kullanıcı
Model 4 aktif

Faz 4: Evrensel Kapsam (24+ ay)

80 milyon vatandaş hedefi
Uluslararası entegrasyon


9. Rekabet Analizi
ÖzellikGeleneksel PKIBlockchainEphemeralMaliyetYüksekÇok yüksekMinimalHızOrtaYavaşÇok hızlıÖlçeklenebilirlikSınırlıÇok sınırlıSınırsızEnerji tüketimiDüşükÇok yüksekMinimalRegülasyon uyumuTamBelirsizTamKullanım kolaylığıZorÇok zorÇok kolay

10. Sonuç ve Vizyon
Ephemeral E-İmza Protokolü, dijital imza teknolojisinde gerçek bir paradigma değişimi sunuyor. "Kullanıcı onaylar, CA imzalar" prensibi ile:

Güvenlik maksimize ediliyor (işlem izolasyonu)
Maliyet minimize ediliyor (sıfır donanım)
Erişim evrensel hale geliyor (herkes için)

Türkiye İçin Fırsat
Bu protokolü ilk uygulayan ülke olarak Türkiye:

Dijital dönüşümde öncü konuma gelir
Teknoloji ihracatçısı olur
Vatandaş memnuniyetini artırır
Bürokrasiyi azaltır

Gelecek Vizyonu
2030 yılında her Türk vatandaşı, farkında olmadan günde onlarca işlemi ephemeral e-imza ile onaylıyor olacak. Dolandırıcılık, sahtecilik ve kimlik hırsızlığı tarihe karışacak.

Referanslar

5070 Sayılı Elektronik İmza Kanunu
BTK Elektronik Sertifika Hizmet Sağlayıcıları Tebliği
eIDAS Regulation (EU) 910/2014
ETSI EN 319 401 - Electronic Signatures Standards


İletişim
Proje Sahibi: Güven Acar
E-posta: guvenacar@gmail.com
GitHub: github.com/guvenacar/ephemeral-e-signature
ORCID: 0009-0000-4232-7405

"Dijital imzada paradigma değişimi: Kullanıcı onaylar, CA imzalar."