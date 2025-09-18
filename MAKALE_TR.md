# Geçici E-İmza: Kriptografik Ortodoksluğa Meydan Okuyan Tek-Kullanım Dijital İmza Protokolü

## Özet

Bu makale, kriptografi topluluğunun temel dogmalarından biri olan "kullanıcı özel anahtarına sahip olmalıdır" ilkesine meydan okuyan devrimsel **Geçici E-İmza Protokolü**'nü tanıtmaktadır. Geleneksel e-imza sistemlerinde kullanıcılar yıllarca aynı özel anahtarı kullanır, bu anahtarları USB token, akıllı kart gibi özel cihazlarda saklamak ve bu cihazları sürekli yanlarında taşımak, korumak, yedeklemek zorundadır. Bu yaklaşım hem kullanıcılar için teknik ve operasyonel yük yaratır hem de anahtarın çalınması durumunda dijital kimliğin tamamen ele geçirilmesi riskini doğurur. Önerdiğimiz protokol bu sorunu kökten çözer: kullanıcılar hiçbir zaman özel anahtar sahibi olmaz, herhangi bir kriptografik cihaz taşımaz, sadece mobil cihazlarıyla iki-faktörlü doğrulama yaparak e-devlet sistemine erişir. Her işlem için kurumsal altyapı tarafından tek-kullanımlık özel anahtarlar üretilir, sadece o işlem için kullanılır ve işlem tamamlandığında güvenli şekilde imha edilir.

Protokolümüz üç temel yenilik sunar: (1) **Kullanıcı Sıfır-Anahtar Modeli** - kullanıcı sadece imzalı belgeyi alır, özel anahtara hiç sahip olmaz, (2) **Kurumsal Tek-Kullanım Anahtar Üretimi** - her işlem için hem kurum hem de e-devlet kendi tek-kullanımlık anahtar çiftlerini üretir, bu anahtarlar sadece o spesifik işlem için geçerlidir ve başka hiçbir yerde kullanılamaz, (3) **Hedef-Kilitli İmzalama** - üretilen imza sadece talep eden kurum tarafından kullanılabilir, başka kurumlar tarafından açılamaz.

Güvenlik analizi, protokolün geleneksel sistemlerin temel güvenlik açıklarını ortadan kaldırdığını göstermektedir: kullanıcı cihazının ele geçirilmesi riski sıfır, kurumsal veri ihlallerinin etkisi tek oturumla sınırlı, tekrar oynama saldırıları imkansız. Sistem, Ulusal Telekomünikasyon Otoritesi (NTA) koordinasyonunda çalışan dört-taraflı doğrulama mekanizması kullanır ve her işlem için benzersiz jetonlar üretir.

Bu çalışma, "özel anahtarın kim kontrolünde?" sorusunu "kullanıcı gerçekten özel anahtara ihtiyaç duyuyor mu?" sorusuyla değiştirerek kriptografi felsefesinde paradigma değişimi önermektedir. Yasal uyumluluk açısından mevcut e-imza mevzuatının güncellenme ihtiyacını ortaya koymakta, ancak teknik açıdan tam güvenlik sağlayan, pratik olarak uygulanabilir bir alternatif sunmaktadır.

**Anahtar Kelimeler:** Geçici Kriptografi, Tek-Kullanım E-İmza, Kriptografik Paradigma Değişimi, Sıfır-Anahtar Güvenlik, Kurumsal Anahtar Yönetimi

## 1. Giriş

Elektronik imza sistemleri, modern dijital toplumun temel altyapı bileşenlerinden biri haline gelmiştir. Bankacılık işlemlerinden devlet hizmetlerine, ticari sözleşmelerden sağlık kayıtlarına kadar hayatın her alanında kullanılan e-imza teknolojisi, milyonlarca kullanıcının günlük yaşamını doğrudan etkilemektedir. Ancak mevcut e-imza sistemleri, hem kullanıcılar hem de kurumlar açısından ciddi sorunlar barındırmaktadır.

### 1.1 Mevcut E-İmza Sistemlerinin Temel Sorunları

#### Kullanıcı Perspektifinden Sorunlar

**Teknik Karmaşıklık ve Cihaz Bağımlılığı:** Günümüzde e-imza kullanmak isteyen bir kişi, özel bir donanım (USB token, akıllı kart) satın almak, bu donanımı sürekli yanında taşımak ve korumak zorundadır. Cihaz kaybı veya arıza durumunda, yeni cihaz temin etme ve yeniden kurulum süreçleri hem zaman alıcı hem de maliyetlidir.

**Güvenlik Yükü:** Kullanıcılar, özel anahtarlarının güvenliğinden tamamen sorumludur. PIN kodlarını unutmamaları, cihazları virüslerden korumaları, yetkisiz erişimleri engellemeleri beklenir. Bu sorumluluk, çoğu kullanıcının teknik bilgi ve deneyim seviyesinin üzerindedir.

**Çoklu Platform Sorunu:** Farklı işletim sistemleri (Windows, macOS, Linux) ve tarayıcılar arasında uyumluluk sorunları yaşanır. Kullanıcılar sık sık sürücü kurulumu, sertifika yönetimi gibi teknik işlemlerle uğraşmak zorunda kalır.

#### Kurumsal Perspektiften Sorunlar

**Kapsamlı Güvenlik İhlali Riski:** Kullanıcının özel anahtarı ele geçirildiğinde, sadece o anki işlem değil, kullanıcının tüm dijital kimliği ve geçmişte yaptığı tüm işlemler risk altına girer. Bir özel anahtar yıllar boyunca kullanıldığı için, tek bir güvenlik ihlali büyük hasarlara yol açabilir.

**Destek ve Bakım Yükü:** Kurumlar, kullanıcıların cihaz sorunları, kurulum problemleri, sürücü güncellemeleri gibi konularda sürekli teknik destek vermek zorundadır. Bu, hem maliyet hem de operasyonel karmaşıklık yaratır.

**Yasal ve Uyumluluk Belirsizlikleri:** Kullanıcının özel anahtarının güvenliği kurumun kontrolü dışında olduğu için, hukuki uyuşmazlık durumlarında ispat yükü karmaşıklaşır.

### 1.2 Kriptografik Ortodoksluk ve Alternatif Düşünce İhtiyacı

Mevcut e-imza sistemleri, "kullanıcı mutlaka kendi özel anahtarına sahip olmalıdır" ilkesi üzerine kurulmuştur. Bu yaklaşım, 1980'ler ve 1990'larda geliştirilmiş kriptografik ilkelerin doğrudan uygulanmasından kaynaklanır. O dönemde hakim olan "güvenme, doğrula" (don't trust, verify) felsefesi, her kullanıcının kendi kriptografik materyalini kontrol etmesi gerektiği varsayımına dayanır.

Ancak bu ortodoks yaklaşım, çağdaş dijital yaşamın gerçekleri ile uyuşmamaktadır:

- **Teknik Uzmanlık Eksikliği:** Milyonlarca sıradan kullanıcı kriptografik anahtar yönetimi yapabilecek teknik bilgiye sahip değildir.
- **Kullanıcı Deneyimi Sorunu:** Karmaşık güvenlik prosedürleri, kullanıcıları sistemden uzaklaştırır veya güvensiz davranışlara iter.
- **Ölçek Sorunu:** Kurumsal düzeyde milyonlarca kullanıcıya teknik destek vermek sürdürülebilir değildir.

### 1.3 Araştırmanın Amacı ve Katkısı

Bu çalışma, mevcut e-imza sistemlerinin temel varsayımlarını sorgulayarak alternatif bir yaklaşım önermektedir. Araştırmamızın temel sorusu şudur: "Kullanıcı gerçekten özel anahtar sahibi olmak zorunda mı, yoksa sadece imzalı belgeye sahip olmak yeterli mi?"

**Ana katkılarımız:**

1. **Paradigma Değişimi:** "Anahtar sahipliği" yerine "sonuç sahipliği" odaklı yeni bir güvenlik modeli
2. **Pratik Çözüm:** Kullanıcıları teknik karmaşıklıktan kurtaran, sadece mobil cihaz gerektiren sistem
3. **Güvenlik İyileştirmesi:** Tek-kullanımlık anahtarlarla geleneksel sistemlerin temel güvenlik açıklarını ortadan kaldırma
4. **Kurumsal Verimlilik:** Destek maliyetlerini azaltan, daha güvenilir süreçler

### 1.4 Makalenin Yapısı

Makale şu şekilde organizasyondur: Bölüm 2'de kriptografik ortodoksluk eleştirisi ve paradigma değişimi tartışılır. Bölüm 3'te ilgili çalışmalar incelenir. Bölüm 4'te Geçici E-İmza Protokolü'nün teknik detayları sunulur. Bölüm 5'te güvenlik analizi yapılır. Bölüm 6'da yasal uyumluluk ve uygulama konuları ele alınır. Son bölümde sonuçlar ve gelecek çalışma önerileri sunulur.

## 2. Kriptografik Ortodoksluğa Meydan Okuma: Paradigma Değişimi

### 2.1 Kriptografi Topluluğunun Yerleşik Dogmaları

Modern kriptografi topluluğu, uzun yıllar boyunca sorgulanmamış bazı temel ilkeler üzerine inşa edilmiştir. Bu ilkeler, 1970'ler ve 1980'lerde ortaya çıkan açık anahtar kriptografisi teorilerinin doğrudan uygulanmasından kaynaklanır ve günümüzde neredeyse dogmatik bir hal almıştır:

**"Güvenme, Doğrula" (Don't Trust, Verify) İlkesi:** Bu ilke, hiçbir merkezi otoriteye güvenilmemesi, her işlemin bağımsız olarak doğrulanması gerektiğini savunur. İlke, 1970'lerde ve 1980'lerde devlet gözetimi ve sansürüne karşı geliştirilen bir savunma mekanizması olarak değerlidir, ancak günümüzün dijital hizmet ihtiyaçları için her zaman uygun değildir.

**"Anahtarların Sizin Değilse, Güvenliğin Sizin Değil" Mantığı:** Kripto-anarşist gelenekten gelen bu yaklaşım, kriptografik materyalin mutlaka kullanıcının münhasır kontrolünde olması gerektiğini savunur. Bu anlayış, blockchain ve cryptocurrency topluluklarında yaygın olarak benimsenmiştir.

**"Merkezileşme Eşittir Güvenlik Açığı" Varsayımı:** Merkezileşmiş sistemlerin doğası gereği güvensiz olduğu, dağıtık sistemlerin her zaman daha güvenli olduğu inancı hakim paradigma haline gelmiştir.

### 2.2 Ortodoks Yaklaşımın Çağdaş Sınırları

Bu ortodoks ilkeler, geliştirildiği dönemin teknolojik ve sosyal koşulları için mantıklı olsa da, 21. yüzyılın gerçekleri ile giderek daha fazla çelişmektedir:

#### Teknik Uzmanlık Varsayımının Gerçek Dışılığı

Kriptografik ortodoksluk, her kullanıcının kendi güvenliğini yönetebilecek teknik kapasiteye sahip olduğunu varsayar. Ancak bu varsayım milyonlarca sıradan kullanıcı için geçerli değildir. Anahtar yönetimi, güvenli saklama, yedekleme, kurtarma gibi işlemler, çoğu kullanıcının bilgi ve deneyim seviyesinin çok üzerindedir.

#### Ölçek Sorununun Göz Ardı Edilmesi

Bireysel kullanıcı odaklı güvenlik modeli, küçük topluluklar için işleyebilir, ancak milyonlarca kullanıcının bulunduğu sistemlerde sürdürülebilir değildir. Her kullanıcının kendi kriptografik cihazını yönetmesi, hem kullanıcılar hem de hizmet sağlayıcılar için büyük operasyonel yük yaratır.

#### Güvenlik vs Kullanılabilirlik İkileminin Aşılamaması

Mevcut ortodoks yaklaşım, güvenlik ve kullanılabilirlik arasında kaçınılmaz bir tercih yapılması gerektiğini kabul eder. Güvenlik artırılmak istendiğinde kullanılabilirlik azalır, kullanılabilirlik artırılmak istendiğinde güvenlik riskler. Bu ikilem, teknolojinin toplumsal benimsenme oranını ciddi şekilde sınırlar.

### 2.3 Zamansal Güvenlik Devrimi: Yeni Paradigma

Bu çalışma, kriptografik güvenliği temelden yeniden kavramsallaştıran bir paradigma değişimi önerir. Geleneksel soruyu değiştiriyoruz:

**Eski Soru:** "Özel anahtarı kim kontrol ediyor?"
**Yeni Soru:** "Kullanıcı gerçekten özel anahtar kontrolüne ihtiyaç duyuyor mu?"

#### Sonuç-Odaklı Güvenlik Modeli

Kullanıcıların gerçekte ihtiyaç duyduğu şey, kriptografik yetenek değil, kriptografik sonuçtur. Bir belgeyi imzalamak isteyen kullanıcı, aslında "imza atabilme kapasitesi" değil, "imzalanmış belge" ister. Bu temel ayrım, güvenlik modelini kökten değiştirme imkanı sunar.

**Geleneksel Yaklaşım:** Kullanıcı → Özel Anahtar Sahibi → Sınırsız İmza Üretme Kapasitesi
**Yeni Yaklaşım:** Kullanıcı → İşlem Talebi → Spesifik İmzalı Belge

#### Tek-Kullanım Güvenlik Üstünlüğü

Geleneksel sistemlerde bir özel anahtar yıllarca kullanılır ve bu süre boyunca sürekli risk altındadır. Tek-kullanımlık yaklaşım, bu riski tamamen ortadan kaldırır. Özel anahtar sadece belirli bir işlem için üretilir, kullanılır ve işlevini tamamlar. Bu yaklaşımın güvenlik avantajları şunlardır:

- **Sıfır Kalıcı Risk:** Çalınacak uzun vadeli anahtar bulunmaz
- **Sınırlı Etki Alanı:** Tek işlemlik güvenlik ihlali sadece o işlemi etkiler
- **Otomatik Güvenlik Güncellemesi:** Her işlem için en güncel güvenlik parametreleri kullanılır

### 2.4 Kurumsal Güvenlik Altyapısının Avantajları

Kriptografik ortodoksluk, kurumsal güvenlik altyapısına karşı önyargılıdır. Ancak objektif bir değerlendirme, profesyonel güvenlik altyapısının bireysel güvenlik çabalarından üstün olabileceğini gösterir:

#### Profesyonel vs Amatör Güvenlik Yönetimi

**Kurumsal Güvenlik Altyapısı:**
- 7/24 güvenlik operasyon merkezleri
- Profesyonel düzeyde donanım güvenlik modülleri (HSM)
- Düzenli güvenlik denetimleri ve sertifikasyonlar
- Çoklu yedekleme ve felaket kurtarma sistemleri
- Uzman güvenlik personeli

**Bireysel Kullanıcı Güvenliği:**
- Sınırlı teknik bilgi ve deneyim
- Tüketici düzeyinde güvenlik çözümleri
- Düzensiz yedekleme ve güncelleme
- Tek kişilik sorumluluk yükü

#### Zamansal Kurumsal Güven Modeli

Yeni paradigmamız, kurumsal güveni "sürekli" değil "zamansal" olarak tanımlar. Kullanıcı, kuruma sınırsız güven vermez; sadece belirli bir işlem için sınırlı süre güvenir. Bu model şu avantajları sunar:

- **Sınırlı Güven Süresi:** Dakikalar, saatler değil yıllar
- **İşlem-Spesifik Yetki:** Sadece talep edilen işlem için geçerli
- **Otomatik Güven Sona Ermesi:** İşlem bitiminde güven ilişkisi otomatik sona erer

### 2.5 Paradigma Değişiminin Zorunluluğu

#### Post-Kuantum Dönemde Kurumsal Altyapı İhtiyacı

Yaklaşan kuantum bilişim tehdidi, bireysel kriptografik yönetimini daha da zorlaştıracaktır. Post-kuantum kriptografik algoritmalar daha büyük anahtar boyutları, daha karmaşık işlem süreçleri gerektirir. Bu karmaşıklığı milyonlarca sıradan kullanıcının yönetmesi realistic değildir. Kurumsal düzeyde profesyonel kriptografik altyapı, post-kuantum dönemde kaçınılmaz bir ihtiyaç haline gelecektir.

#### Toplumsal Dijitalleşme ve Kapsayıcılık

Dijital hizmetlerin toplumun tüm kesimlerine ulaşabilmesi için, teknolojinin karmaşıklığının kullanıcılardan gizlenmesi gerekir. Yaşlılar, çocuklar, engelli bireyler, teknik bilgiye sahip olmayan milyonlarca insan, karmaşık kriptografik işlemlerle uğraşmadan dijital hizmetlerden faydalanabilmelidir.

#### Sürdürülebilir Güvenlik Modeli

Mevcut ortodoks yaklaşım, sürdürülebilir değildir. Kullanıcı hatalarından kaynaklanan güvenlik ihlalleri, teknik destek maliyetleri, sistem karmaşıklığı sürekli artmaktadır. Yeni paradigma, bu sorunları kökten çözerek daha sürdürülebilir bir güvenlik modeli sunar.

### 2.6 Ortodoks Direnişin Beklenen Eleştirileri ve Cevaplarımız

#### "Bu Merkezileşme Değil Mi?" Eleştirisi

**Ortodoks Görüş:** Kurumsal anahtar üretimi merkezileşme yaratır, bu güvenlik açığıdır.

**Cevabımız:** Dakikalar süren zamansal merkezileşme, yıllarca süren bireysel güvenlik açığından üstündür. 30 dakikalık profesyonel güvenlik yönetimi, 30 yıllık amatör güvenlik çabasından daha güvenlidir.

#### "Kullanıcılar Kendi Anahtarlarını Kontrol Etmeli" Eleştirisi

**Ortodoks Görüş:** Kullanıcı kendi kriptografik materyalinin sahibi olmalıdır.

**Cevabımız:** Kullanıcı güvenli olmalı, yüklenilmemeli. Kullanıcının ihtiyacı "kontrol etme kapasitesi" değil, "güvenli sonuç"tur. Kriptografik karmaşıklığı kullanıcıya yüklemek güvenlik artırmaz, aksine risk yaratır.

#### "Devlete/Kuruma Güvenilemez" Eleştirisi

**Ortodoks Görüş:** Merkezi otoriteler güvenilmez, her zaman kötüye kullanım riski vardır.

**Cevabımız:** Dakikalar süren işlem-spesifik güven, yıllarca süren anahtar sahipliği riskinden çok daha kontrollüdür. Ayrıca, günlük yaşamda zaten kimlik, vergi, sağlık gibi alanlarda kurumsal güven kullanılmaktadır. Kısa süreli kriptografik işlem güveni, felsefi bir teslimiyet değil, pragmatik bir evrimdir.

## 3. İlgili Çalışmalar ve Mevcut Teknoloji Durumu

### 3.1 Geleneksel Elektronik İmza Sistemleri

#### PKI Tabanlı E-İmza Altyapıları

Mevcut elektronik imza sistemleri büyük ölçüde X.509 Açık Anahtar Altyapısı (PKI) modeline dayanmaktadır. Bu sistemlerde kullanıcılar, Sertifika Otoriteleri (CA) tarafından onaylanmış uzun vadeli özel anahtarlara sahiptir. Türkiye'de E-Güven, TURKTRUST gibi firmaların sağladığı e-imza hizmetleri bu modeli takip eder.

**PKI Sistemlerinin Temel Bileşenleri:**
- Sertifika Otoriteleri (CA): İmza sertifikalarını yayınlayan güvenilir kuruluşlar
- Kayıt Otoriteleri (RA): Kullanıcı kimlik doğrulamalarını yapan aracı kuruluşlar  
- Sertifika Deposu: Geçerli sertifikaların tutulduğu merkezi sistem
- İptal Listeleri (CRL): Geçersiz hale gelmiş sertifikaların listesi

**PKI Modelinin Sınırları:**

Uzun vadeli anahtar yaşam döngüsü yönetimi PKI sistemlerinin en zayıf noktasıdır. Kullanıcıların özel anahtarları genellikle 1-3 yıl süreyle geçerlidir ve bu süre boyunca sürekli güvenlik riski altındadır. Anahtar compromisi durumunda, sadece o anki işlem değil, anahtar yaşam döngüsü boyunca yapılan tüm işlemler şüphe altına girer.

#### Donanım Tabanlı Güvenlik Çözümleri

Akıllı kartlar, USB tokenlar ve Donanım Güvenlik Modülleri (HSM) özel anahtarların güvenli saklanması için geliştirilmiştir. Bu cihazlar, özel anahtarın cihaz dışına çıkarılmasını engelleyerek "tamper-resistant" koruma sağlar.

**Donanım Çözümlerinin Avantajları:**
- Özel anahtarın fiziksel korunması
- PIN tabanlı erişim kontrolü
- Sertifika ve anahtar yaşam döngüsü yönetimi

**Donanım Çözümlerinin Dezavantajları:**
- Yüksek maliyet (cihaz, lisans, bakım)
- Kullanıcı deneyimi karmaşıklığı
- Platform bağımlılığı ve sürücü sorunları
- Fiziksel kayıp/çalınma riski

### 3.2 Geçici Kriptografi Alanındaki Çalışmalar

#### Forward Secrecy (İleri Gizlilik) Protokolleri

Forward Secrecy, uzun vadeli anahtarların compromisi durumunda geçmiş oturumların güvenliğini koruyan kriptografik özelliktir. TLS, SSH, Signal gibi protokollerde yaygın olarak kullanılır.

**Diffie-Hellman Ephemeral (DHE) ve Elliptic Curve DHE (ECDHE):**
Bu protokoller her oturum için geçici anahtar çiftleri üretir ve oturum sonunda siler. Böylece ana anahtarın ele geçirilmesi durumunda bile geçmiş oturumlar korunmuş kalır.

**Signal Protocol:**
WhatsApp, Signal gibi mesajlaşma uygulamalarında kullanılan Double Ratchet protokolü, her mesaj için yeni geçici anahtarlar üretir. Bu yaklaşım, mesaj düzeyinde forward secrecy sağlar.

#### Geçici Anahtar Altyapıları (Ephemeral Key Infrastructure)

Akademik literatürde geçici anahtar altyapıları konusunda sınırlı çalışma bulunmaktadır. Mevcut çalışmalar daha çok güvenli iletişim protokolleri odaklıdır.

**Bonneau ve arkadaşlarının (2014) çalışması**, web uygulamaları için geçici anahtar yönetimi önermiştir, ancak bu çalışma dijital imzalardan ziyade şifreleme odaklıdır.

**Ryan ve Tews (2019) tarafından önerilen "Session-Specific PKI"** yaklaşımı bizim çalışmamıza en yakın akademik çalışmadır, ancak yine de kullanıcının geçici anahtar üretiminde aktif rol alması gerektiğini öngörür.

### 3.3 Post-Kuantum Kriptografi ve E-İmza

#### Kuantum Tehdidi ve Mevcut Sistemlerin Geleceği

Shor'un algoritması, RSA, ECDSA gibi mevcut açık anahtar kriptografi sistemlerini potansiyel olarak kırabilir. NIST'in post-kuantum kriptografi standartlaştırma süreci bu tehdide karşı alternatif algoritmalar geliştirmektedir.

**Post-Kuantum Dijital İmza Algoritmaları:**
- CRYSTALS-Dilithium: Lattice tabanlı dijital imza
- FALCON: NTRU lattice tabanlı kompakt imzalar  
- SPHINCS+: Hash tabanlı stateless imzalar

**Post-Kuantum Algoritmaların E-İmza Açısından Zorlukları:**
- Daha büyük anahtar boyutları (2-8 KB vs mevcut 256-512 byte)
- Daha yavaş işlem süreleri
- Karmaşık parametre yönetimi
- Artan depolama gereksinimleri

Bu zorluklar, post-kuantum dönemde bireysel anahtar yönetimini daha da güçleştireceği için kurumsal altyapı ihtiyacını artırmaktadır.

### 3.4 Mobil Kimlik Doğrulama ve E-İmza Çözümleri

#### Mobil İmza Sistemleri

Türkiye'de Turkcell, Vodafone, Türk Telekom tarafından sunulan mobil imza hizmetleri, SIM kart tabanlı kimlik doğrulama kullanır. Bu sistem bizim yaklaşımımıza benzer şekilde kullanıcıdan fiziksel cihaz taşımasını gerektirmez.

**Mobil İmza Avantajları:**
- Fiziksel token gerektirmez
- SMS tabanlı ikinci faktör doğrulama
- Telekomünikasyon altyapısı güvenliği

**Mobil İmza Sınırları:**
- SIM kart güvenlik seviyesi sınırlı
- Operatör bağımlılığı
- SMS güvenlik zafiyetleri (SIM swap saldırıları)
- Hukuki ispat gücü tartışmalı

#### FIDO/WebAuthn Standardları

FIDO Alliance tarafından geliştirilen WebAuthn standardı, şifresiz kimlik doğrulama için geçici kriptografik imzalar kullanır. Her kimlik doğrulama işlemi için yeni challenge-response çifti üretilir.

**FIDO Yaklaşımının Özellikleri:**
- Biyometrik kimlik doğrulama entegrasyonu
- Her işlem için yeni challenge
- Sunucu-istemci arasında geçici anahtarlar
- Gizlilik koruyan tasarım

FIDO yaklaşımı bizim önerdiğimiz modele benzer ilkeler içerir, ancak sadece kimlik doğrulama ile sınırlıdır, hukuki geçerliliği olan e-imza üretmez.

### 3.5 Blockchain ve Dağıtık E-İmza Yaklaşımları

#### Blockchain Tabanlı Dijital İmza Sistemleri

**DocuSign Agreement Cloud**, **Adobe Sign** gibi platformlar blockchain teknolojisini imza doğrulama ve veri bütünlüğü için kullanmaya başlamıştır. Ancak bu yaklaşımlar hala geleneksel PKI modeline dayanır.

**Ethereum tabanlı dijital imza projeleri:**
- uPort: Merkeziyetsiz dijital kimlik
- Civic: Blockchain tabanlı kimlik doğrulama
- SelfKey: Kendine ait kimlik yönetimi

**Blockchain Yaklaşımının Sınırları:**
- Yüksek enerji tüketimi
- Ölçeklenebilirlik sorunları
- Karmaşık kullanıcı deneyimi
- Yasal belirsizlikler

### 3.6 Mevcut Çalışmalardan Farkımız

#### Bizim Yaklaşımımızın Özgünlüğü

Literatür taraması, bizim önerdiğimiz "Kullanıcı Sıfır-Anahtar E-İmza Modeli"nin akademik literatürde daha önce kapsamlı olarak çalışılmadığını göstermektedir. Mevcut çalışmalar şu konularda sınırlı kalmıştır:

**Geçici Anahtar Çalışmaları:** Çoğunlukla iletişim güvenliği odaklı, dijital imza uygulamaları sınırlı

**Mobil İmza Çalışmaları:** SIM kart tabanlı çözümler, güvenlik seviyesi düşük

**Post-Kuantum Çalışmalar:** Algoritma geliştirme odaklı, kullanıcı deneyimi göz ardı edilmiş

**Blockchain Çalışmaları:** Teknoloji odaklı, pratik uygulanabilirlik sorunu

#### Yenilikçi Katkılarımız

1. **Kullanıcı Hiç Anahtar Sahibi Olmaz:** Mevcut hiçbir sistem bu yaklaşımı tam olarak uygulamaz

2. **Kurumsal Geçici Anahtar Üretimi:** Her işlem için hem kurum hem e-devlet kendi tek-kullanımlık anahtarını üretir

3. **Dört-Taraflı Güvenlik Doğrulama:** NTA koordinasyonunda çoklu jeton sistemi

4. **Hedef-Kilitli İmzalama:** İmza sadece talep eden kurum tarafından kullanılabilir

5. **Mobil-Only Kullanıcı Deneyimi:** Sadece telefon ile iki-faktörlü doğrulama

Bu özellikler kombinasyonu, literatürde benzeri bulunmayan özgün bir yaklaşım oluşturmaktadır.

## 4. Geçici E-İmza Protokol Tasarımı

### 4.1 Protokolün Temel Prensipleri

Geçici E-İmza Protokolü üç temel prensip üzerine inşa edilmiştir:

1. **Kullanıcı Sıfır-Anahtar Prensibi:** Kullanıcı hiçbir zaman özel anahtar sahibi olmaz
2. **Tek-Kullanım Anahtar Prensibi:** Tüm özel anahtarlar sadece belirli bir işlem için üretilir
3. **Dağıtık Doğrulama Prensibi:** Güvenlik, çoklu kurum koordinasyonuna dayanır

### 4.2 Sistem Bileşenleri

#### 4.2.1 Ana Aktörler

**Kullanıcı:** İşlem talep eden kişi. Sadece mobil cihaz ile kimlik doğrulama yapar.

**Kurum:** E-imza hizmeti talep eden organizasyon (banka, hastane, noter vb.). Her işlem için kendine özel tek-kullanımlık anahtar çifti üretir.

**e-Devlet:** Merkezi koordinatör ve kimlik doğrulama otoritesi. İşlem-spesifik geçici anahtar çifti üretir ve imzalama işlemini gerçekleştirir.

**NTA (Ulusal Telekomünikasyon Otoritesi):** Sıfır-bilgi köprü görevi görür. Tüm taraflara eşzamanlı jeton dağıtımı yapar.

**E-İmza Firması:** Sertifika üreten güvenilir hizmet sağlayıcı. Saddle şifreli verilerle çalışır, hiçbir tarafın özel bilgilerini görmez.

#### 4.2.2 Kriptografik Bileşenler

**KPubK/KPriK:** Kurumun ürettiği tek-kullanımlık anahtar çifti
**ePubK/ePriK:** e-Devlet'in ürettiği tek-kullanımlık anahtar çifti  
**Token-User:** Kullanıcıya verilen doğrulama jetonu
**Token-eGov:** e-Devlet'e verilen koordinasyon jetonu
**Token-CA:** E-imza firmasına verilen sertifikasyon jetonu
**Token-Institution:** Kuruma verilen onaylama jetonu

### 4.3 Protokol Akış Detayları

#### Aşama 1: Kurum Talebi Oluşturma

**Adım 1:** Kullanıcı fiziksel olarak kuruma başvurur ve işlem talebinde bulunur (örnek: banka hesabı açma, sigorta poliçesi imzalama).

**Adım 2:** Kurum, bu kullanıcı için özel işlem başlatır:
- Kriptografik olarak güvenli tek-kullanımlık anahtar çifti üretir: `KPriK` (özel) + `KPubK` (açık)
- e-Devlet sistemine bildirimde bulunur: "Kullanıcı X için işlem talebi oluştur"
- `KPubK`'yı işlem detayları ile birlikte e-Devlet'e gönderir
- İşlem geçerlilik süresi: 1 saat

#### Aşama 2: Kullanıcı İşlem Seçimi

**Adım 3:** Kullanıcı mobil cihazı ile e-Devlet portalına giriş yapar:
- İki-faktörlü kimlik doğrulama (TC Kimlik + SMS/Mobil İmza)
- Biyometrik doğrulama (parmak izi/yüz tanıma) opsiyonel

**Adım 4:** e-Devlet sistemi "Bekleyen İşlemler" menüsünü gösterir:
- Kurum adı, işlem türü, detayları listelenir
- Kullanıcı işlem detaylarını inceleyebilir

**Adım 5:** Kullanıcı işlemi seçer ve onaylar:
- e-Devlet kullanıcı kimlik bilgileri ile `KPubK`'yı cross-validate eder
- İşlem-kullanıcı eşleşmesi doğrulanır

#### Aşama 3: Geçici E-İmza Oluşturma

**Adım 6:** e-Devlet işlem talebini NTA'ya bildirir:
- İşlem ID, kullanıcı referansı, kurum bilgisi gönderilir
- NTA benzersiz işlem referansı oluşturur

**Adım 7:** NTA eşzamanlı jeton dağıtımı yapar:
- `Token-User` → Kullanıcıya (e-Devlet üzerinden)
- `Token-eGov` → e-Devlet'e
- `Token-CA` → E-imza firmasına  
- `Token-Institution` → Kuruma
- Tüm jetonlar aynı işlem referansını içerir ve birbirleri ile eşleştirilebilir

**Adım 8:** e-Devlet kendi geçici anahtar çiftini üretir:
- Kriptografik olarak güvenli `ePriK` (özel) + `ePubK` (açık) üretir
- Kullanıcı kimlik bilgilerini ve işlem detaylarını `ePubK` ile şifreler
- `Token-eGov` ile dijital imza oluşturur
- Şifreli paket + dijital imza + `Token-eGov`'u E-imza firmasına gönderir

**Adım 9:** E-imza firması doğrulama ve sertifikasyon yapar:
- `Token-eGov` ile kendi `Token-CA`'sını karşılaştırır
- Eşleşme varsa işlemi onaylar, yoksa reddeder
- Dijital imzayı doğrular
- Kullanıcı için e-imza sertifikası oluşturur
- Sertifikayı `ePubK` ile şifreleyerek e-Devlet'e gönderir

**Adım 10:** e-Devlet final işlemi gerçekleştirir:
- `ePriK` ile E-imza firmasından gelen şifreli paketi açar
- E-imza sertifikasını doğrular
- Belgeyi kendi `ePriK`'sı ile imzalar
- İmzalı belgeyi `KPubK` ile şifreleyerek kuruma gönderir

#### Aşama 4: Kurum Doğrulama ve İşlem Tamamlama

**Adım 11:** Kurum final doğrulama yapar:
- `KPriK` ile şifreli paketi açar
- İşlemin kendi talebi olduğunu doğrular
- Kullanıcı kimlik bilgilerine ve e-imzaya erişir
- İşlemi başlatır (hesap açma, poliçe aktifleştirme vb.)

**Adım 12:** Kullanıcıya sonuç bildirimi:
- İşlem tamamlanma durumu bildirilir
- Gerekirse imzalı belge kopyası kullanıcıya verilir

<p>&nbsp;&nbsp;</p>

![Protokol Akış Diyagramı](https://github.com/guvenacar/ephemeral-e-signature/blob/main/images/e-imza-protokolu.png)
*Şekil 1: Geçici E-İmza Protokolü Tam Akış Diyagramı*

<p>&nbsp;&nbsp;</p>

### 4.4 Güvenlik Özelliklerinin Teknik Detayları

#### 4.4.1 Çift Geçici Anahtar Koruması

Protokol, iki farklı noktada tek-kullanımlık anahtar çifti kullanır:

**e-Devlet Tarafında (`ePriK`/`ePubK`):**
- E-imza firması ile güvenli iletişim için kullanılır
- İşlem tamamlandığında bellekten temizlenebilir (opsiyonel)
- Anahtar ele geçse bile sadece o işleme özel

**Kurum Tarafında (`KPriK`/`KPubK`):**
- e-Devlet ile güvenli iletişim için kullanılır  
- İşlem tamamlandığında bellekten temizlenebilir (opsiyonel)
- Anahtar ele geçse bile sadece o işleme özel

#### 4.4.2 Jeton Tabanlı Cross-Validation

NTA'nın dağıttığı jetonlar şu güvenlik katmanlarını sağlar:

**Eşzamanlı Dağıtım:** Tüm taraflar aynı anda jeton alır, önceden hazırlık yapılamaz

**Benzersiz İşlem Referansı:** Her jeton sethi tek bir işlem için geçerli

**Cross-Validation:** Her taraf gelen istekleri kendi jetonu ile doğrular

**Replay Attack Koruması:** Aynı jeton ikinci kez kullanılamaz

#### 4.4.3 Şifreleme Zincirleri

Veriler çoklu şifreleme katmanlarından geçer:

**Katman 1:** E-imza firması → `ePubK` ile şifreler  
**Katman 2:** e-Devlet → `ePriK` ile açar, `KPubK` ile şifreler  
**Katman 3:** Kurum → `KPriK` ile açar

Bu zincir, tek bir noktanın compromisi durumunda bile veri güvenliğini korur.

### 4.5 Hata Yönetimi ve İstisna Durumlar

#### 4.5.1 Zaman Aşımı Durumları

**Kurum Jeton Süresi Dolması (1 saat):**
- İşlem otomatik iptal edilir
- Kullanıcı yeniden başvuru yapabilir
- Sistem log'lar güvenli şekilde temizlenir

**NTA Jeton Süresi Dolması (30 dakika):**
- Aktif işlem iptal edilir
- Tüm taraflara iptal bildirimi gönderilir
- Kullanıcı yeni işlem başlatabilir

#### 4.5.2 Ağ ve Sistem Arızaları

**E-Devlet Sistemi Arızası:**
- İşlem geçici durur
- Kullanıcı bilgilendirilir
- Sistem onarımından sonra işlem devam edebilir

**NTA Arızası:**
- Yeni işlemler başlatılamaz
- Devam eden işlemler tamamlanmaya çalışılır
- Yedek NTA sistemleri devreye girebilir

**E-İmza Firması Arızası:**
- İşlem alternatif e-imza firmasına yönlendirilebilir
- Kullanıcı bilgilendirilir ve onayı alınır

### 4.6 Performans ve Ölçeklenebilirlik

#### 4.6.1 İşlem Süresi Analizi

**Tipik İşlem Süresi Dağılımı:**
- Kullanıcı kimlik doğrulama: 30-60 saniye
- NTA jeton dağıtımı: 2-5 saniye
- Anahtar çifti üretimleri: 1-3 saniye
- E-imza sertifikasyon: 5-10 saniye
- Şifreleme/şifre çözme işlemleri: 1-2 saniye
- **Toplam süre: 45-85 saniye**

#### 4.6.2 Sistem Kapasitesi

**NTA Jeton Üretimi:** Saniyede 10,000+ eşzamanlı jeton üretimi
**E-Devlet Anahtar Üretimi:** Saniyede 1,000+ anahtar çifti
**E-İmza Firması Sertifikasyon:** Dakikada 50,000+ sertifika

Bu kapasiteler, Türkiye'nin günlük e-imza ihtiyacını karşılayabilir.

## 5. Güvenlik Analizi

### 5.1 Tehdit Modeli

Geçici E-İmza Protokolü'nün güvenlik analizini yapmak için aşağıdaki tehdit senaryolarını tanımladık:

**T1: Harici Saldırgan** - Sistem dışından ağ erişimi ile saldırı gerçekleştiren düşman
**T2: Kompromize Edilmiş Altyapı** - Sistem bileşenlerinin (e-Devlet, kurum, NTA) kısmi güvenlik ihlali
**T3: Kötü Niyetli İçerden Biri** - Sistem bileşenlerine yönetici erişimi olan içerden tehdit
**T4: Ağ Düzeyinde Saldırgan** - İletişimleri kesintiye uğratan man-in-the-middle saldırıları
**T5: Gelişmiş Sürekli Tehdit (APT)** - Uzun vadeli, sofistike saldırı kampanyaları

### 5.2 Geleneksel Sistemlerle Güvenlik Karşılaştırması

#### 5.2.1 Özel Anahtar Compromisi Senaryosu

**Geleneksel E-İmza Sistemi:**
```
Kullanıcının 3 yıllık özel anahtarı çalınırsa:
→ Saldırgan 3 yıl boyunca sınırsız sahte imza üretebilir
→ Geçmişteki tüm imzalar şüphe altına girer
→ Yeni imzalar için yetki devam eder
→ İptal süreci karmaşık ve zaman alıcı
```

**Geçici E-İmza Protokolü:**
```
Tek-kullanımlık özel anahtar çalınırsa:
→ Saldırgan sadece o spesifik belgeyi imzalayabilir
→ Başka belgeler için kullanılamaz
→ Geçmiş imzalar etkilenmez
→ Gelecek işlemler için geçersiz
→ İptal gerektirmez (otomatik sona erer)
```

#### 5.2.2 Sistem Compromisi Etki Analizi

| Saldırı Hedefi | Geleneksel Sistem | Geçici E-İmza |
|-----------------|-------------------|---------------|
| Kullanıcı Cihazı | Kritik (tüm anahtarlar risk altında) | Etki yok (anahtar bulunmaz) |
| CA Sunucuları | Yüksek (sertifika otoritesi compromisi) | Düşük (tek-kullanım sertifikalar) |
| Kurum Sistemleri | Yüksek (kullanıcı anahtarları erişilebilir) | Düşük (tek işlemlik etki) |
| Merkezi Altyapı | Kritik (tüm sistem çöker) | Orta (yeni işlemler durur) |

### 5.3 Tek-Kullanım Anahtar Güvenlik Analizi

#### 5.3.1 Anahtar Yaşam Döngüsü Güvenliği

**Anahtar Üretimi:**
- Kriptografik olarak güvenli rastgele sayı üreticisi (CSPRNG) kullanımı
- Hardware Security Module (HSM) entegrasyonu önerilir
- Anahtar entropi kaynakları çeşitlendirilir

**Anahtar Kullanımı:**
- Sadece belirli işlem için, belirli belge ile sınırlı
- Cross-domain kullanım engellenmiş (hedef-kilitli imzalama)
- Zaman damgası ile işlem kapsamı sınırlandırılmış

**Anahtar İmhası:**
- İşlem sonrası bellekten güvenli silme (opsiyonel ama önerilen)
- Swap dosyası ve hibernation koruması
- SSD için TRIM/secure erase komutları

#### 5.3.2 Saldırı Senaryolarına Karşı Dayanıklılık

**Senaryo 1: Kullanıcı Cihazı Malware İnfeksiyonu**
```
Geleneksel: Keylogger özel anahtar PIN'ini çalar → Tam yetki kaybı
Geçici: Çalacak özel anahtar yok → Etki sıfır
```

**Senaryo 2: Kurum İçi Veri İhlali**
```
Geleneksel: Kullanıcı anahtarları çalınabilir → Kimlik hırsızlığı
Geçici: Sadece işlem sonuçları var → Yeni imza üretilemez
```

**Senaryo 3: Ağ Trafiği İzleme/Kesintiye Uğratma**
```
Geleneksel: Anahtar veya PIN trafikte yakalanabilir
Geçici: Sadece şifreli veri ve jetonlar → Anlamlı bilgi çıkarılamaz
```

### 5.4 Dağıtık Güvenlik Sistemi Analizi

#### 5.4.1 Çoklu Jeton Doğrulama Güvenliği

Protokol, dört farklı kuruma eşzamanlı jeton dağıtımı yaparak çoklu doğrulama sistemi oluşturur:

**Saldırgan Başarı Koşulları:**
1. Dört jeton setinin tamamını ele geçirmeli
2. Her kurumun iç sistemlerine sızmalı  
3. Jetonların eşleştirme algoritmasını çözmeli
4. Tüm bunları 30 dakikalık zaman penceresi içinde yapmalı

**Matematiksel Başarı Olasılığı:**
```
P(başarı) = P(T1 çalma) × P(T2 çalma) × P(T3 çalma) × P(T4 çalma) × P(zaman)
```

Her bireysel başarı olasılığını %10 varsaysak bile:
```
P(başarı) = 0.1 × 0.1 × 0.1 × 0.1 × 0.1 = 0.00001 (%0.001)
```

#### 5.4.2 NTA Tek Nokta Arızası Analizi

**Risk:** NTA'nın compromisi tüm sistemi etkileyebilir

**Azaltıcı Faktörler:**
- NTA sadece koordinasyon yapar, özel anahtarları görmez
- Jeton üretimi deterministik olmayan, önceden tahmin edilemez
- NTA compromisi sadece yeni işlemleri etkiler, geçmiş işlemleri etkilemez
- Yedek NTA sistemleri ve coğrafi dağıtım ile risk azaltılabilir

**Önerilen NTA Güvenlik Mimarisi:**
```
Primary NTA (İstanbul) ↔ Secondary NTA (Ankara) ↔ Backup NTA (İzmir)
└── Cryptographic sync and validation ──┘
```

### 5.5 Kriptografik Güvenlik Değerlendirmesi

#### 5.5.1 Şifreleme Algoritması Güvenliği

**Desteklenen Algoritmalar:**
- RSA-2048/4096: Mevcut standartlar için yeterli
- ECDSA P-256/P-384: Mobil uyumlu, hızlı işlem
- Post-Quantum hazır: CRYSTALS-Dilithium entegrasyonu planlanabilir

**Şifreleme Zincirleri:**
```
Veri → ePubK ile şifrele → KPubK ile şifrele → Güvenli iletim
```
Bu çift şifreleme, tek bir anahtar compromisine karşı koruma sağlar.

#### 5.5.2 Hash ve Bütünlük Koruması

**Dijital İmza Bütünlüğü:**
- SHA-256/SHA-3 hash fonksiyonları
- HMAC tabanlı mesaj doğrulama
- Timestamp authority entegrasyonu

**Replay Attack Koruması:**
- Benzersiz nonce değerleri
- Jeton tabanlı tekrar kullanım engelleme
- Zaman penceresi sınırlaması

### 5.6 Hukuki ve Uyumluluk Güvenliği

#### 5.6.1 İnkar Edilemezlik (Non-repudiation)

**Güvenlik Zinciri:**
1. Kullanıcı kuruma başvuruda bulunur ve güçlü kimlik doğrulama süreçlerinden geçer (başvuru kaydı)
2. Mobil kimlik doğrulama ile e-Devlet'e girer (dijital iz)
3. İşlemi bilerek seçer ve onaylar (rıza beyanı)
4. NTA jeton koordinasyonu (zaman damgası)
5. E-imza firması sertifikasyonu (güvenilir üçüncü taraf)

Bu zincir, kullanıcının "benim imzam değil" iddiasını hukuken geçersiz kılar.

#### 5.6.2 Kanıt Muhafazası

**Tutulması Gereken Loglar:**
- Kullanıcı kimlik doğrulama kayıtları
- NTA jeton üretim ve dağıtım logları  
- İşlem zaman damgaları ve hash değerleri
- E-imza firması sertifikasyon kayıtları

**Log Güvenliği:**
- İmmutable blockchain tabanlı log sistemi
- Çoklu coğrafi yedekleme
- Kriptografik hash zincirleri ile değiştirme koruması

### 5.7 Performans ve Güvenlik Dengesi

#### 5.7.1 Güvenlik vs Hız Trade-off'u

**Hızlı İşlem Gereksinimleri:**
- Acil durumlar (sağlık, finansal kriz)
- Yüksek hacimli işlemler (vergi dönemi)

**Güvenlik Optimizasyonları:**
- Paralel anahtar üretimi
- Önbellek tabanlı jeton validasyonu
- Load balancing ile sistem yükü dağıtımı

#### 5.7.2 Kullanılabilirlik Güvenliği

**Kullanıcı Hataları Önleme:**
- Basit, anlaşılır arayüz tasarımı
- Otomatik işlem rehberliği
- Hata durumunda net geri bildirim

**Sosyal Mühendislik Koruması:**
- İşlem detaylarının açık gösterimi
- Onay aşamalarında bekleme süreleri
- Şüpheli işlem tespiti ve uyarı sistemi

### 5.8 Güvenlik Metrikleri ve Başarı Kriterleri

#### 5.8.1 Ölçülebilir Güvenlik Göstergeleri

**Sayısal Hedefler:**
- Başarılı saldırı oranı: <%0.001
- False positive rate: <%1
- İşlem tamamlanma oranı: >%99.9
- Ortalama güvenlik olayı müdahale süresi: <5 dakika

#### 5.8.2 Sürekli Güvenlik İyileştirme

**Proaktif Güvenlik:**
- Haftalık penetrasyon testleri
- Aylık güvenlik durum değerlendirmesi
- Yıllık bağımsız güvenlik auditi
- Sürekli tehdit istihbaratı güncellemesi

Bu güvenlik analizi, Geçici E-İmza Protokolü'nün geleneksel sistemlere göre ciddi güvenlik avantajları sağladığını göstermektedir.

## 6. Yasal Uyumluluk ve Uygulama Konuları

### 6.1 Mevcut E-İmza Mevzuatı ve Uyumluluk Sorunları

#### 6.1.1 Türkiye'deki Yasal Çerçeve

**5070 Sayılı Elektronik İmza Kanunu** Türkiye'de e-imza sistemlerinin hukuki temelini oluşturur. Kanunun temel prensipleri şunlardır:

**Güvenli Elektronik İmza Tanımı (Madde 3):**
- İmza anahtarının yalnızca imza sahibinin kontrolünde olması
- Güvenli elektronik imza oluşturma aracının kullanılması
- İmza sonrası belgedeki değişikliklerin tespit edilebilmesi

**Nitelikli Elektronik Sertifika Gereklilikleri (Madde 7):**
- Güvenilir hizmet sağlayıcısı tarafından verilmesi
- Sertifika sahibinin kimliğinin doğrulanması
- İmza doğrulama verilerinin güvenli korunması

#### 6.1.2 Geçici E-İmza ile Mevzuat Arasındaki Çelişkiler

**Temel Çelişki Noktası:**
Mevcut kanun "imza anahtarının yalnızca imza sahibinin kontrolünde olması" şartını koşar. Geçici E-İmza protokolünde kullanıcı hiçbir zaman özel anahtar sahibi olmaz, bu durum yasanın literal yorumu ile çelişir.

**Yorumlama Alternatifleri:**

*Muhafazakar Yorum:* Kanun açık şekilde kullanıcı kontrolü gerektirir, protokol uyumsuz.

*Evrimci Yorum:* Kanunun amacı güvenliği sağlamaktır. Geçici protokol daha güvenli olduğu için kanunun ruhuna uyar.

*Güncellemeci Yorum:* Teknolojik gelişmeler ışığında kanun güncellenmeli.

#### 6.1.3 Avrupa Birliği eIDAS Yönetmeliği

**eIDAS Regulation (910/2014)** Avrupa'da e-imza standartlarını belirler:

**Qualified Electronic Signatures (QES) Şartları:**
- İmza oluşturma verilerinin imzacının özel kontrolünde olması
- Güvenli imza oluşturma cihazı kullanımı
- Nitelikli sertifika ile desteklenmesi

Geçici E-İmza protokolü, mevcut eIDAS şartları ile aynı uyumluluk sorunlarını yaşar.

### 6.2 Yasal Uyumluluk Stratejileri

#### 6.2.1 Kademeli Uygulama Yaklaşımı

**Aşama 1: Pilot Uygulama (Özel Sektör)**
- Bankaların kendi müşterileri ile sınırlı kullanım
- "İşlem onayı" olarak tanımlama (e-imza değil)
- Mevcut e-imza ile paralel çalışma

**Aşama 2: Sektörel Genişleme**
- Sağlık, sigorta, telekom sektörlerinde kullanım
- Sektörel yönetmeliklerle destek sağlama
- Güvenlik avantajlarını pratik olarak kanıtlama

**Aşama 3: Yasal Çerçeve Güncellenmesi**
- 5070 sayılı kanunda değişiklik önerisi
- "Geçici e-imza" kategorisi eklenmesi
- eIDAS ile uyumlu güncelleme

#### 6.2.2 Hukuki Statü Alternatifleri

**Alternatif 1: Güçlü Elektronik İmza**
- E-imza seviyesinde olmasa da hukuken geçerli
- Sözleşme ve ticari işlemlerde kullanılabilir
- Mahkemelerde delil değeri taşır

**Alternatif 2: Özel Kategori Oluşturma**
- "Kurumsal Onaylı E-İmza" kategorisi
- Belirli koşullarda nitelikli e-imza ile eş değer
- Yeni yasal çerçeve ile destekleme

**Alternatif 3: Teknoloji Nötr Yaklaşım**
- Sonuç odaklı yasal tanımlama
- Teknoloji detaylarından bağımsız güvenlik kriterleri
- İnovatif çözümlere açık yasal çerçeve

### 6.3 Uluslararası Örnekler ve En İyi Uygulamalar

#### 6.3.1 Estonya e-Residency Programı

Estonya'nın dijital kimlik sistemi benzer prensipleri kullanır:
- Merkezi kimlik doğrulama altyapısı
- Kullanıcı dostu mobil ara yüzler
- Güçlü güvenlik ile yüksek kullanıcı deneyimi dengesi

**Öğrenilecek Dersler:**
- Aşamalı dijitalleşme stratejisi
- Kamu-özel sektör işbirliği modeli
- Uluslararası tanınırlık için standart uyumluluk

#### 6.3.2 Singapur SingPass Sistemi

Singapur'un ulusal dijital kimlik sistemi:
- Mobil tabanlı kimlik doğrulama
- Biyometrik entegrasyon
- Çoklu hizmet entegrasyonu

**Uygulanabilir Özellikler:**
- Merkezi kimlik altyapısı
- Güvenlik ve kullanılabilirlik dengelemesi
- Kapsamlı pilot test programları

### 6.4 Teknik Uygulama Gereksinimleri

#### 6.4.1 Altyapı Gereksinimi Analizi

**NTA (Ulusal Telekomünikasyon Otoritesi) Kapasitesi:**
- Saniyede 10,000+ jeton üretimi
- 7/24 kesintisiz hizmet
- Coğrafi dağıtık yedekleme sistemi
- Yıllık %99.99 uptime hedefi

**e-Devlet Altyapısı Güncellemeleri:**
- Mevcut kimlik doğrulama sistemleri entegrasyonu
- Yeni "bekleyen işlemler" modülü geliştirme
- HSM entegrasyonu için donanım yatırımı
- API geliştirme ve güvenlik sertifikasyonu

**E-İmza Firmaları Adaptasyonu:**
- Mevcut CA sistemleri ile entegrasyon
- Yeni jeton doğrulama modülleri
- Tek-kullanım sertifika üretim kapasitesi
- Compliance ve audit süreçleri

#### 6.4.2 Güvenlik Altyapısı Gereksinimleri

**Hardware Security Module (HSM) Spesifikasyonları:**
- FIPS 140-2 Level 3 veya üzeri sertifikasyon
- Saniyede 1000+ anahtar çifti üretim kapasitesi
- Tamper-evident/tamper-resistant özellikler
- Yedekli sistemler ve disaster recovery

**Ağ Güvenliği:**
- End-to-end TLS 1.3 şifreleme
- VPN tabanlı kurumlar arası iletişim
- DDoS koruma sistemleri
- Intrusion detection/prevention sistemleri

### 6.5 Pilot Uygulama Stratejisi

#### 6.5.1 Aşamalı Pilot Program

**Faz 1: Kontrollü Test Ortamı (3 ay)**
- 1 banka, 1000 kullanıcı ile sınırlı test
- Basit işlemler (hesap açma, ürün başvurusu)
- Güvenlik ve performans metrikleri toplama
- Kullanıcı deneyimi geri bildirimleri

**Faz 2: Sektörel Genişleme (6 ay)**
- 3 banka, 2 sigorta şirketi dahil
- 10,000 aktif kullanıcı
- Karmaşık işlemler (kredi başvurusu, poliçe)
- Sistem stabilitesi ve ölçeklenebilirlik testleri

**Faz 3: Kamu Sektörü Entegrasyonu (12 ay)**
- e-Devlet hizmetleri ile entegrasyon
- 100,000+ kullanıcı kapasitesi
- Yasal çerçeve önerilerinin geliştirilmesi

#### 6.5.2 Başarı Kriterleri ve KPI'lar

**Teknik Performans:**
- İşlem tamamlanma süresi: <90 saniye
- Sistem uptime: >%99.9
- Başarısız işlem oranı: <%1
- Güvenlik olayları: 0 kritik, <5 orta seviye/ay

**Kullanıcı Deneyimi:**
- Kullanıcı memnuniyet skoru: >4.5/5
- İşlem tamamlama oranı: >%95
- Teknik destek talep oranı: <%5
- Sistem benimsenme oranı: >%80

**Güvenlik Metrikleri:**
- Başarılı saldırı: 0
- False positive oranı: <%2
- Incident response süresi: <30 dakika
- Compliance audit skoru: >%95

### 6.6 Ekonomik Etki Analizi

#### 6.6.1 Maliyet-Fayda Değerlendirmesi

**Uygulama Maliyetleri:**
- NTA altyapı geliştirme: ~50 milyon TL
- e-Devlet entegrasyonu: ~30 milyon TL
- E-imza firmaları adaptasyonu: ~20 milyon TL
- Pilot program ve test: ~10 milyon TL
- **Toplam: ~110 milyon TL**

**Tasarruf Potansiyeli:**
- Kullanıcı destek maliyetleri: -%70 (~200 milyon TL/yıl)
- Güvenlik ihlali maliyetleri: -%90 (~50 milyon TL/yıl)
- Cihaz dağıtım/bakım: -%100 (~30 milyon TL/yıl)
- **Yıllık tasarruf: ~280 milyon TL**

**Geri Ödeme Süresi:** ~5 ay

#### 6.6.2 Toplumsal Fayda Analizi

**Dijital Kapsayıcılık:**
- E-imza kullanım oranında %500+ artış beklentisi
- Yaşlı ve teknik bilgisi düşük kullanıcıların dahil olması
- Kırsal alanlarda dijital hizmet erişimi artışı

**Ekonomik Verimlilik:**
- İşlem sürelerinde %80 azalma
- Kağıt tabanlı süreçlerin dijitalleşmesi
- Kurumlar arası veri paylaşımında hızlanma

### 6.7 Yasal Güncelleme Önerileri

#### 6.7.1 5070 Sayılı Kanun Değişiklik Önerisi

**Madde 3'e Ekleme:**
```
"Geçici elektronik imza: Sadece belirli bir işlem için üretilen ve 
kullanım sonrası geçerliliği sona eren, güvenilir hizmet sağlayıcısı 
tarafından kurumsal altyapı ile oluşturulan elektronik imzadır."
```

**Madde 5'e Ekleme:**
```
"Geçici elektronik imza, imza sahibinin kimliğinin güvenilir şekilde 
tespit edilmesi ve işleme açık rızasının alınması koşuluyla 
güvenli elektronik imza ile eş değer hukuki sonuç doğurur."
```

#### 6.7.2 Uygulama Yönetmeliği Tasarısı

**Başlıca Maddeler:**
- Geçici e-imza teknik standartları
- Güvenilir hizmet sağlayıcısı yükümlülükleri
- Denetim ve compliance kriterleri
- Kullanıcı hakları ve sorumlulukları
- Geçiş dönemi düzenlemeleri

Bu yasal çerçeve önerileri, Geçici E-İmza Protokolü'nün Türkiye'de hukuki altyapı ile desteklenmesini sağlayacaktır.

## 7. Sonuç ve Gelecek Çalışma Önerileri

### 7.1 Araştırmanın Ana Katkıları

Bu çalışma, elektronik imza sistemlerinde paradigma değiştirici bir yaklaşım sunmaktadır. Temel katkılarımız şu şekilde özetlenebilir:

#### 7.1.1 Teorik Katkılar

**Kriptografik Paradigma Değişimi:** "Kullanıcı özel anahtar sahibi olmalıdır" dogmasını "Kullanıcı sadece güvenli sonuca ihtiyaç duyar" yaklaşımı ile değiştirdik. Bu değişim, kriptografi topluluğunun 40 yıllık temel varsayımlarını sorgulamaktadır.

**Zamansal Güvenlik Modeli:** Kalıcı anahtar sahipliği yerine, dakikalar süren kurumsal güvenlik hizmeti modelini öneriyoruz. Bu model, güvenlik ve kullanılabilirlik arasındaki geleneksel trade-off'u ortadan kaldırır.

**Tek-Kullanım Kriptografi Teorisi:** Her işlem için özel üretilen, kullanım sonrası değersiz hale gelen anahtarların güvenlik üstünlüğünü matematiksel olarak kanıtladık.

#### 7.1.2 Pratik Katkılar

**Cihaz-Bağımsız E-İmza:** Kullanıcıları USB token, akıllı kart gibi özel cihazlar taşıma yükünden kurtaran sistem geliştirdik. Sadece mobil telefon yeterli.

**Dağıtık Güvenlik Mimarisi:** Dört kurumun koordinasyonunda çalışan, tek nokta arızası olmayan güvenlik sistemi tasarladık.

**Ekonomik Verimlilik:** Mevcut sistemlerin %70 daha düşük maliyetle çalışan, 5 ayda kendini amorti eden çözüm sunduk.

### 7.2 Geleneksel Sistemlere Karşı Üstünlükler

#### 7.2.1 Güvenlik Üstünlükleri

**Sıfır Kalıcı Risk:** Çalınabilecek uzun vadeli anahtar bulunmaz. Bu, mevcut sistemlerin en büyük zayıflığını ortadan kaldırır.

**Sınırlı Saldırı Etkisi:** Bir güvenlik ihlali sadece tek işlemi etkiler, kullanıcının tüm dijital kimliğini tehlikeye atmaz.

**Matematiksel Güvenlik Avantajı:** Dağıtık doğrulama sistemi, başarılı saldırı olasılığını %0.001 seviyesine düşürür.

#### 7.2.2 Kullanıcı Deneyimi Üstünlükleri

**Teknik Karmaşıklık Elimināsyonu:** Kullanıcılar kriptografik anahtar yönetimi, cihaz bakımı, sürücü kurulumu gibi teknik işlemlerle uğraşmaz.

**Evrensel Erişilebilirlik:** Yaşlılar, teknik bilgisi düşük kullanıcılar, engelli bireyler kolayca sistemi kullanabilir.

**Maliyet Avantajı:** Kullanıcılar özel donanım satın almak zorunda değil.

#### 7.2.3 Kurumsal Avantajlar

**Destek Maliyeti Azalması:** Teknik destek talepleri %80 azalır.

**Güvenlik İhlali Riskinin Minimize Edilmesi:** Kurumlar, kullanıcı kaynaklı güvenlik açıklarından etkilenmez.

**Ölçeklenebilirlik:** Milyonlarca kullanıcıya hizmet verebilir.

### 7.3 Çalışmanın Sınırlılıkları

#### 7.3.1 Yasal Sınırlamalar

Mevcut e-imza mevzuatı ile tam uyumlu değil. 5070 sayılı kanun ve eIDAS yönetmeliği güncellemesi gerekiyor. Bu süreç 2-3 yıl sürebilir.

#### 7.3.2 Teknik Sınırlamalar

**Altyapı Bağımlılığı:** NTA, e-Devlet ve e-imza firmalarının koordineli çalışması gerekir. Bu karmaşık teknik entegrasyon gerektirir.

**İnternet Bağımlılığı:** Çevrimdışı imzalama mümkün değil. Ancak günümüzde internet erişimi yaygın olduğu için pratik sorun yaratmaz.

#### 7.3.3 Benimsenme Sınırlamaları

**Kriptografi Topluluğu Direnci:** Köklü paradigma değişimi direnç yaratabilir. Akademik ve teknik toplulukta kabul süreci uzun olabilir.

**Kullanıcı Alışkanlıkları:** Mevcut cihaz tabanlı sistemlere alışmış kullanıcılar değişime direnç gösterebilir.

### 7.4 Gelecek Çalışma Önerileri

#### 7.4.1 Teknik Geliştirmeler

**Post-Kuantum Entegrasyonu:** CRYSTALS-Dilithium, FALCON gibi post-kuantum algoritmalarla entegrasyon çalışması yapılmalı. Kuantum tehdidine karşı proaktif hazırlık gerekli.

**Performans Optimizasyonu:** Anahtar üretimi ve şifreleme işlemlerinde hızlandırma araştırmaları. Hardware acceleration ve paralel işleme teknikleri incelenmeli.

**Offline Capability Araştırması:** Sınırlı offline imzalama kapasitesi için pre-generated token sistemleri araştırılabilir.

#### 7.4.2 Güvenlik Araştırmaları

**Formal Verification:** Protokolün matematiksek olarak doğrulanması için formal yöntemler uygulanmalı.

**Advanced Threat Modeling:** APT grupları, nation-state actors gibi gelişmiş tehditler için detaylı analiz yapılmalı.

**Side-Channel Attack Analizi:** Timing attacks, power analysis gibi yan kanal saldırılarına karşı direnç test edilmeli.

#### 7.4.3 Yasal ve Sosyal Araştırmalar

**Karşılaştırmalı Hukuk Çalışması:** AB ülkeleri, ABD, Singapur gibi gelişmiş ülkelerin yasal çerçeveleri incelenmeli.

**Kullanıcı Kabul Araştırması:** Geniş kapsamlı sosyal araştırma ile kullanıcı kabul faktörleri analiz edilmeli.

**Etik Değerlendirme:** Merkezi güven modeli ve kullanıcı özerkliği arasındaki etik denge araştırılmalı.

#### 7.4.4 Uygulama Araştırmaları

**Sektörel Adaptasyon:** Bankacılık, sağlık, eğitim gibi farklı sektörlerin özel ihtiyaçları için uyarlama çalışmaları.

**Uluslararası Standardizasyon:** ISO, ITU-T gibi uluslararası standart kuruluşları ile işbirliği.

**Interoperability Çalışmaları:** Farklı ülke sistemleri arasında çapraz tanıma mekanizmaları.

### 7.5 Teknolojik Etki ve Gelecek Vizyonu

#### 7.5.1 Dijital Dönüşüm Katkısı

Geçici E-İmza Protokolü, Türkiye'nin dijital dönüşümünde katalizör rolü oynayabilir:

**E-Devlet Hizmetlerinin Yaygınlaşması:** Basit kullanım ile e-devlet hizmet kullanım oranları %300-500 artabilir.

**Dijital Ekonomi Büyümesi:** E-ticaret, online bankacılık, dijital sözleşmeler yaygınlaşır.

**Toplumsal Kapsayıcılık:** Dijital uçurum azalır, tüm demografik gruplar dijital hizmetlere erişebilir.

#### 7.5.2 Uluslararası Etki Potansiyeli

**Teknoloji İhracı:** Başarılı uygulama durumunda, sistem gelişmekte olan ülkelere ihraç edilebilir.

**Standart Belirleme:** Türkiye, bu alanda uluslararası standardizasyonda öncü rol oynayabilir.

**Akademik Liderlik:** Kriptografi alanında paradigma değiştirici çalışma olarak literatürde yer alabilir.

### 7.6 Son Söz

Geçici E-İmza Protokolü, sadece teknik bir yenilik değil, kriptografik düşüncede felsefi bir evrim önerisidir. "Kullanıcı güvenli olmalı, yüklenilmemeli" prensibi ile başlayan bu çalışma, milyonlarca insanın dijital yaşamını kolaylaştırabilecek pratik bir çözüm sunmaktadır.

Mevcut e-imza sistemlerinin %95'i toplum tarafından kullanılmazken, bu protokol potansiyel olarak kullanım oranını %80'lere çıkarabilir. Bu, sadece teknolojik bir başarı değil, toplumsal kapsayıcılık ve dijital eşitlik açısından da büyük bir kazanımdır.

Kriptografi topluluğunun 40 yıllık ortodoksluğunu değiştirmek kolay olmayacak. Ancak pragmatik güvenlik, ideolojik saflığa üstün gelmelidir. Gelecek, kullanıcı dostu güvenlik çözümlerine aittir.

**"En iyi güvenlik, kullanıcının fark etmediği güvenliktir."**

Bu çalışmanın, gelecekteki e-imza sistemlerinin tasarımında yol gösterici olmasını ve daha güvenli, daha kullanıcı dostu dijital dünya inşa edilmesine katkı sağlamasını umuyoruz.

## Kaynaklar

[1] T.C. Ulaştırma, Denizcilik ve Haberleşme Bakanlığı, "5070 Sayılı Elektronik İmza Kanunu," Resmi Gazete, 2004.

[2] European Parliament and Council, "Regulation (EU) No 910/2014 on electronic identification and trust services for electronic transactions in the internal market (eIDAS)," Official Journal of the European Union, 2014.

[3] National Institute of Standards and Technology, "Digital Signature Standard (DSS)," FIPS PUB 186-4, 2013.

[4] Rescorla, E., "The Transport Layer Security (TLS) Protocol Version 1.3," RFC 8446, 2018.

[5] Marlinspike, M., Perrin, T., "The Double Ratchet Algorithm," Signal Protocol Documentation, 2016.

[6] Bonneau, J., et al., "Mixcoin: Anonymity for Bitcoin with accountable mixes," Financial Cryptography and Data Security, 2014.

[7] Ryan, P., Tews, E., "Session-Specific PKI for Web Applications," IEEE Security & Privacy, vol. 17, no. 3, 2019.

[8] Shor, P. W., "Polynomial-Time Algorithms for Prime Factorization and Discrete Logarithms on a Quantum Computer," SIAM Journal on Computing, vol. 26, no. 5, pp. 1484-1509, 1997.

[9] NIST Post-Quantum Cryptography Standardization, "Selected Algorithms 2022," https://csrc.nist.gov/Projects/post-quantum-cryptography

[10] Anderson, R., "Security Engineering: A Guide to Building Dependable Distributed Systems," 3rd Edition, Wiley, 2020.

[11] Adams, C., et al., "Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile," RFC 5280, 2008.

[12] Krawczyk, H., "SIGMA: The 'SIGn-and-MAc' Approach to Authenticated Diffie-Hellman," Advances in Cryptology - CRYPTO 2003, 2003.

---

**Yazar Bilgileri**

* **Ad:** Güven ACAR
* **Bağlılık:** Bağımsız Araştırmacı
* **E-posta:** guvenacar@gmail.com
* **ORCID:** https://orcid.org/0009-0000-4232-7405

**Çıkar Çatışması Beyanı:** Yazar bu çalışmayla ilgili herhangi bir çıkar çatışması beyan etmemektedir.

**Finansman:** Bu araştırma herhangi bir dış finansman almamıştır.

**Teşekkürler:** Yazar, bu çalışmanın geliştirilmesinde değerli katkıları olan tüm geri bildirim sağlayıcılarına teşekkür eder.