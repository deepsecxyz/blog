---
title: "Veri Sınıflandırma Çerçevesi ve Uygulama Rehberi"
linkTitle: "Veri Sınıflandırma"
slug: "veri-siniflandirm"
description: "Kapsamlı veri sınıflandırma çerçevesi, uygulama stratejileri ve kurumsal veri koruma en iyi uygulamaları"
translationKey: "data-classification"
weight: 1
layout: docs
type: docs
url: "/tr/blog/varlik-guvenligi/veri-siniflandirma"

---

{{< callout type="warning" >}}
**Kritik Veri Koruması**: Etkili veri sınıflandırma, bilgi güvenliğinin temelidir. Bu rehber, organizasyonunuzun en değerli varlıklarını korumak için sağlam veri sınıflandırma stratejileri uygulamak üzere kapsamlı bir çerçeve sunar.
{{< /callout >}}

Veri sınıflandırma, verileri hassasiyet, değer ve düzenleyici gereksinimlerine göre kategorilere ayırmak için sistematik bir yaklaşımdır. Bu kapsamlı rehber, kurumsal veri varlıklarını korumak için sınıflandırma çerçevelerini, uygulama stratejilerini ve en iyi uygulamaları incelemektedir.

## Veri Sınıflandırmayı Anlama

Veri sınıflandırma, verileri hassasiyet seviyesi, iş değeri ve uyumluluk gereksinimlerine göre kategorilere organize etmeyi içerir. Bu sistematik yaklaşım, organizasyonların uygun güvenlik kontrollerini ve koruma önlemlerini uygulamasını sağlar.

{{< callout type="info" >}}
**Güvenliğin Temeli**: Veri sınıflandırma, organizasyonunuz genelinde etkili veri koruma, erişim kontrolleri ve uyumluluk önlemlerini uygulamak için köşe taşı görevi görür.
{{< /callout >}}

## Sınıflandırma Çerçevesi

### Standart Sınıflandırma Seviyeleri

{{< tabs >}}
{{< tab title="Genel" >}}
**Açıklama**: Herhangi bir kısıtlama olmadan halkla serbestçe paylaşılabilen bilgiler.

**Özellikler**:
- Gizlilik gereksinimi yok
- Düzenleyici kısıtlama yok
- Dış dağıtım için güvenli
- Pazarlama materyalleri, basın bültenleri
- Kamu duyuruları

**Koruma Seviyesi**: Minimal
**Erişim Kontrolü**: Açık erişim
**Şifreleme**: Gerekli değil
**Saklama**: Standart iş saklama
{{< /tab >}}

{{< tab title="İç" >}}
**Açıklama**: Organizasyon içinde iç kullanım için tasarlanmış bilgiler.

**Özellikler**:
- İç iş operasyonları
- Çalışan iletişimi
- Operasyonel prosedürler
- Hassas olmayan iş verileri
- İç raporlar ve analizler

**Koruma Seviyesi**: Düşük
**Erişim Kontrolü**: Çalışan erişimi
**Şifreleme**: İletim için önerilir
**Saklama**: Standart iş saklama
{{< /tab >}}

{{< tab title="Gizli" >}}
**Açıklama**: Yetkisiz erişimden korunması gereken hassas bilgiler.

**Özellikler**:
- İş hassasiyetli bilgiler
- Müşteri verileri (düzenlenmemiş)
- Finansal bilgiler
- Stratejik planlar
- Fikri mülkiyet

**Koruma Seviyesi**: Yüksek
**Erişim Kontrolü**: Rol tabanlı erişim
**Şifreleme**: Gerekli (beklemede ve iletimde)
**Saklama**: Kontrollerle genişletilmiş saklama
{{< /tab >}}

{{< tab title="Kısıtlı" >}}
**Açıklama**: Katı erişim kontrolleri ve düzenleyici gereksinimleri olan yüksek hassasiyetli bilgiler.

**Özellikler**:
- Kişisel Tanımlayıcı Bilgiler (PII)
- Finansal veriler (düzenlenmiş)
- Sağlık bilgileri (HIPAA)
- Hükümet/sınıflandırılmış bilgiler
- Ticari sırlar

**Koruma Seviyesi**: Maksimum
**Erişim Kontrolü**: Onayla katı rol tabanlı
**Şifreleme**: Gerekli (beklemede, iletimde, kullanımda)
**Saklama**: Düzenleyici uyumluluk saklama
{{< /tab >}}
{{< /tabs >}}

### Sınıflandırma Kriterleri

{{< cards >}}
{{< card title="Hassasiyet Seviyesi" icon="eye" content="Verilerin yetkisiz açıklanması, değiştirilmesi veya yok edilmesinin potansiyel etkisini değerlendirin." >}}

{{< card title="İş Değeri" icon="star" content="Bilgilerin stratejik önemini ve iş kritikliğini değerlendirin." >}}

{{< card title="Düzenleyici Gereksinimler" icon="cloud" content="Belirli veri türleri için geçerli olan yasal ve uyumluluk yükümlülüklerini göz önünde bulundurun." >}}

{{< card title="Erişim Gereksinimleri" icon="users" content="Kimin erişime ihtiyaç duyduğunu ve hangi koşullarda olduğunu belirleyin." >}}
{{< /cards >}}

## Uygulama Stratejisi

### Faz 1: Değerlendirme ve Planlama

{{< details title="Veri Keşfi ve Envanter" >}}

**Veri Keşfi Aktiviteleri**:
- [ ] Tüm veri depolarını tanımlayın
- [ ] Veri akışlarını ve süreçlerini haritalayın
- [ ] Veri sahipliğini belgelendirin
- [ ] Mevcut sınıflandırma durumunu değerlendirin
- [ ] Düzenleyici gereksinimleri tanımlayın

**Envanter Bileşenleri**:
- [ ] Yapılandırılmış veritabanları
- [ ] Yapılandırılmamış dosya sistemleri
- [ ] Bulut depolama platformları
- [ ] E-posta sistemleri
- [ ] İşbirliği araçları
- [ ] Yedekleme sistemleri

**Keşif Araçları**:
- [ ] Veri keşif yazılımı
- [ ] Ağ tarama araçları
- [ ] Dosya sistemi analizörleri
- [ ] Veritabanı envanter araçları
- [ ] Manuel değerlendirme süreçleri

{{< /details >}}

### Faz 2: Sınıflandırma Çerçevesi Geliştirme

```yaml
# Örnek Veri Sınıflandırma Politikası
veri_siniflandirma:
  seviyeler:
    genel:
      etiket: "GENEL"
      renk: "yesil"
      aciklama: "Kamuya açık serbest bırakma için güvenli bilgiler"
      kontroller:
        - "Özel işlem gerekmez"
        - "Standart erişim kontrolleri"
    
    ic:
      etiket: "IC"
      renk: "mavi"
      aciklama: "İç iş bilgileri"
      kontroller:
        - "Sadece çalışan erişimi"
        - "İletimde şifreleme"
        - "Standart saklama"
    
    gizli:
      etiket: "GIZLI"
      renk: "turuncu"
      aciklama: "Hassas iş bilgileri"
      kontroller:
        - "Rol tabanlı erişim kontrolü"
        - "Beklemede ve iletimde şifreleme"
        - "Denetim günlüğü gerekli"
        - "Genişletilmiş saklama"
    
    kisitli:
      etiket: "KISITLI"
      renk: "kirmizi"
      aciklama: "Yüksek hassasiyetli bilgiler"
      kontroller:
        - "Katı erişim kontrolleri"
        - "Çok faktörlü kimlik doğrulama"
        - "Tam şifreleme"
        - "Kapsamlı denetim günlüğü"
        - "Düzenleyici uyumluluk"
```

{{< callout type="warning" >}}
**Politika Geliştirme**: Sınıflandırma politikanızın iş hedefleri, düzenleyici gereksinimler ve organizasyon kültürü ile uyumlu olduğundan emin olun.
{{< /callout >}}

### Faz 3: Uygulama ve Dağıtım

{{< tabs >}}
{{< tab title="Otomatik Sınıflandırma" >}}
**Araçlar ve Teknolojiler**:
- Veri Kaybı Önleme (DLP) çözümleri
- İçerik farkında sınıflandırma motorları
- Makine öğrenmesi tabanlı sınıflandırıcılar
- Desen tanıma sistemleri
- Meta veri analiz araçları

**Uygulama Adımları**:
1. Sınıflandırma araçlarını dağıtın
2. Sınıflandırma kurallarını yapılandırın
3. Sınıflandırma modellerini eğitin
4. Sınıflandırma doğruluğunu doğrulayın
5. İzleyin ve iyileştirin

**Faydalar**:
- Tutarlı sınıflandırma
- Azaltılmış manuel çaba
- Gerçek zamanlı sınıflandırma
- Ölçeklenebilir çözüm
{{< /tab >}}

{{< tab title="Manuel Sınıflandırma" >}}
**Süreçler ve Prosedürler**:
- Sınıflandırma karar ağaçları
- Standart operasyon prosedürleri
- İnceleme ve onay iş akışları
- Kalite güvence süreçleri
- Düzenli denetimler ve doğrulama

**Uygulama Adımları**:
1. Sınıflandırma prosedürlerini geliştirin
2. Sınıflandırma ekiplerini eğitin
3. İnceleme süreçlerini kurun
4. Kalite kontrollerini uygulayın
5. Uyumluluğu izleyin

**Faydalar**:
- İnsan yargısı ve bağlam
- Detaylı analiz
- Özel sınıflandırma kuralları
- Düzenleyici uzmanlık
{{< /tab >}}

{{< tab title="Hibrit Yaklaşım" >}}
**Kombine Strateji**:
- Otomatik başlangıç sınıflandırması
- Manuel inceleme ve iyileştirme
- Sürekli öğrenme ve iyileştirme
- Düzenli politika güncellemeleri

**Uygulama Adımları**:
1. Otomatik araçları dağıtın
2. Manuel inceleme süreçlerini kurun
3. Geri bildirim döngüleri oluşturun
4. Sürekli iyileştirme uygulayın
5. Düzenli politika güncellemeleri

**Faydalar**:
- Otomasyonun verimliliği
- İnsan incelemesinin doğruluğu
- Sürekli iyileştirme
- Esneklik ve uyarlanabilirlik
{{< /tab >}}
{{< /tabs >}}

## Teknik Uygulama

### Veri Etiketleme ve İşaretleme

{{< cards >}}
{{< card title="Dosya Seviyesi İşaretleme" icon="cog" content="Meta veriler, başlıklar veya filigranlar kullanarak tek dosyalara ve belgelere sınıflandırma etiketleri uygulayın." >}}

{{< card title="Veritabanı Sınıflandırması" icon="eye" content="Sütun seviyesi ve satır seviyesi güvenlik kontrolleri ile veritabanı seviyesinde sınıflandırma uygulayın." >}}

{{< card title="E-posta Sınıflandırması" icon="mail" content="İçerik ve alıcı hassasiyetine göre e-postalara ve eklerine sınıflandırma etiketleri uygulayın." >}}

{{< card title="Bulut Veri Sınıflandırması" icon="cloud" content="Bulut depolama, işbirliği platformları ve SaaS uygulamaları için sınıflandırma kontrollerini uygulayın." >}}
{{< /cards >}}

### Erişim Kontrolü Uygulaması

```bash
# Örnek LDAP/Active Directory Sınıflandırma Grupları
# Sınıflandırma tabanlı gruplar oluştur
dsadd group "CN=Gizli-Veriler-Erisim,OU=Guvenlik-Gruplari,DC=sirket,DC=com"
dsadd group "CN=Kisitli-Veriler-Erisim,OU=Guvenlik-Gruplari,DC=sirket,DC=com"

# Kullanıcıları sınıflandırma gruplarına ata
dsmod group "CN=Gizli-Veriler-Erisim,OU=Guvenlik-Gruplari,DC=sirket,DC=com" -addmbr "CN=Ahmet Yilmaz,OU=Kullanicilar,DC=sirket,DC=com"

# Örnek dosya sistemi izinleri
# Gizli veri dizini
chmod 750 /veriler/gizli
chown root:gizli-erisim /veriler/gizli

# Kısıtlı veri dizini
chmod 700 /veriler/kisitli
chown root:kisitli-erisim /veriler/kisitli
```

### Şifreleme ve Koruma

{{< tabs >}}
{{< tab title="Beklemede Şifreleme" >}}
**Uygulama**:
- Tam disk şifreleme
- Dosya seviyesi şifreleme
- Veritabanı şifreleme
- Yedekleme şifreleme

**Teknolojiler**:
- BitLocker (Windows)
- FileVault (macOS)
- LUKS (Linux)
- Transparent Data Encryption (TDE)
- Sütun seviyesi şifreleme

**Yapılandırma**:
- Güçlü şifreleme algoritmaları (AES-256)
- Güvenli anahtar yönetimi
- Düzenli anahtar rotasyonu
- Hardware Security Modules (HSM)
{{< /tab >}}

{{< tab title="İletimde Şifreleme" >}}
**Uygulama**:
- Web trafiği için TLS/SSL
- Uzaktan erişim için VPN
- Şifreli dosya transferi
- Güvenli e-posta protokolleri

**Teknolojiler**:
- TLS 1.3
- IPsec VPN
- SFTP/SCP
- E-posta için S/MIME
- Dosyalar için PGP/GPG

**Yapılandırma**:
- Güçlü şifre paketleri
- Sertifika doğrulama
- Perfect Forward Secrecy
- Düzenli güvenlik güncellemeleri
{{< /tab >}}

{{< tab title="Kullanımda Şifreleme" >}}
**Uygulama**:
- Bellek şifreleme
- Uygulama seviyesi şifreleme
- Güvenli enklavlar
- Homomorfik şifreleme

**Teknolojiler**:
- Intel SGX
- AMD SEV
- Uygulama şifreleme kütüphaneleri
- Güvenli çok taraflı hesaplama

**Yapılandırma**:
- Güvenli kodlama uygulamaları
- Bellek koruması
- Çalışma zamanı şifreleme
- Güvenli anahtar işleme
{{< /tab >}}
{{< /tabs >}}

## İzleme ve Uyumluluk

### Denetim Günlüğü ve İzleme

```python
# Örnek denetim günlüğü uygulaması
import logging
import datetime
from dataclasses import dataclass

@dataclass
class VeriErisimOlayi:
    zaman_damgasi: datetime.datetime
    kullanici_id: str
    veri_siniflandirma: str
    eylem: str
    kaynak: str
    ip_adresi: str
    basari: bool

def veri_erisimi_gunlukle(olay: VeriErisimOlayi):
    logger = logging.getLogger('veri_siniflandirma')
    
    gunluk_girisi = {
        'zaman_damgasi': olay.zaman_damgasi.isoformat(),
        'kullanici_id': olay.kullanici_id,
        'siniflandirma': olay.veri_siniflandirma,
        'eylem': olay.eylem,
        'kaynak': olay.kaynak,
        'ip_adresi': olay.ip_adresi,
        'basari': olay.basari
    }
    
    logger.info(f"Veri erişim olayı: {gunluk_girisi}")
    
    # Kısıtlı veri erişiminde uyarı
    if olay.veri_siniflandirma == 'KISITLI':
        uyari_gonder(f"Kısıtlı veri erişimi {olay.kullanici_id} tarafından")
```

### Uyumluluk İzleme

{{< details title="Düzenleyici Uyumluluk Kontrol Listesi" >}}

**KVKK Uyumluluğu**:
- [ ] PII için veri sınıflandırması
- [ ] Onay yönetimi
- [ ] Veri sahibi hakları
- [ ] İhlal bildirim prosedürleri
- [ ] Veri koruma etki değerlendirmeleri

**SOX Uyumluluğu**:
- [ ] Finansal veri sınıflandırması
- [ ] Finansal sistemler için erişim kontrolleri
- [ ] Denetim izi bakımı
- [ ] Değişiklik yönetimi kontrolleri
- [ ] Görev ayrımı

**HIPAA Uyumluluğu**:
- [ ] PHI sınıflandırması
- [ ] Sağlık verileri için erişim kontrolleri
- [ ] Şifreleme gereksinimleri
- [ ] İş ortağı anlaşmaları
- [ ] Olay müdahale prosedürleri

**PCI DSS Uyumluluğu**:
- [ ] Kart sahibi veri sınıflandırması
- [ ] Ödeme sistemi güvenliği
- [ ] Şifreleme standartları
- [ ] Erişim izleme
- [ ] Düzenli güvenlik değerlendirmeleri

{{< /details >}}

## Eğitim ve Farkındalık

### Çalışan Eğitim Programı

{{< cards >}}
{{< card title="Sınıflandırma Temelleri" icon="cloud" content="Veri sınıflandırma seviyeleri, kriterleri ve organizasyonel güvenlik için öneminin temel anlayışı." >}}

{{< card title="İşleme Prosedürleri" icon="cog" content="Sınıflandırma seviyelerine göre verilerin işlenmesi, saklanması ve iletilmesi için uygun prosedürler." >}}

{{< card title="Olay Müdahale" icon="exclamation" content="Veri sınıflandırma olaylarını ve güvenlik ihlallerini nasıl tanıyacağınız ve bunlara nasıl yanıt vereceğiniz." >}}

{{< card title="Uyumluluk Gereksinimleri" icon="star" content="Veri koruma ile ilgili düzenleyici gereksinimler ve organizasyonel politikaların anlaşılması." >}}
{{< /cards >}}

### Eğitim Yöntemleri

1. **Başlangıç Eğitimi**:
   - Yeni çalışan oryantasyonu
   - Rol özel eğitimi
   - Uygulamalı alıştırmalar
   - Değerlendirme ve sertifikasyon

2. **Sürekli Eğitim**:
   - Yıllık yenileme eğitimi
   - Politika güncellemeleri
   - Olay dersleri
   - En iyi uygulama paylaşımı

3. **Özelleştirilmiş Eğitim**:
   - Veri sorumluları ve sahipleri
   - BT yöneticileri
   - Güvenlik profesyonelleri
   - Uyumluluk görevlileri

{{< callout type="info" >}}
**Eğitim Etkinliği**: Düzenli değerlendirme ve geri bildirim, eğitim programlarının etkili ve organizasyonel ihtiyaçlar için uygun kalmasını sağlar.
{{< /callout >}}

## En İyi Uygulamalar ve Öneriler

### Uygulama En İyi Uygulamaları

{{< details title="Veri Sınıflandırma En İyi Uygulamalar Kontrol Listesi" >}}

### Planlama ve Hazırlık
- [ ] Yönetici sponsorluğu ve desteği
- [ ] Fonksiyonlar arası ekip katılımı
- [ ] Net hedefler ve başarı metrikleri
- [ ] Kaynak tahsisi ve bütçe
- [ ] Zaman çizelgesi ve kilometre taşı planlaması

### Çerçeve Geliştirme
- [ ] İş hedefleri ile uyum
- [ ] Düzenleyici gereksinimleri göz önünde bulundurma
- [ ] Önemli paydaşları dahil etme
- [ ] Politikaları ve prosedürleri belgelendirme
- [ ] Yönetişim yapısı kurma

### Uygulama
- [ ] Pilot programlarla başlama
- [ ] Mümkün olduğunda otomatik araçlar kullanma
- [ ] Kapsamlı eğitim sağlama
- [ ] İlerlemeyi izleme ve ölçme
- [ ] Süreçleri sürekli iyileştirme

### Bakım
- [ ] Düzenli politika incelemeleri
- [ ] Teknoloji güncellemeleri
- [ ] Eğitim yenilemeleri
- [ ] Uyumluluk denetimleri
- [ ] Olay müdahale güncellemeleri

{{< /details >}}

### Kaçınılması Gereken Yaygın Tuzaklar

{{< callout type="warning" >}}
**Uygulama Zorlukları**: Veri sınıflandırma girişimlerini başarısızlığa uğratabilecek yaygın tuzakların farkında olun.
{{< /callout >}}

**Yaygın Tuzaklar**:
1. **Aşırı Sınıflandırma**: Çok fazla veriyi yüksek hassasiyetli olarak sınıflandırma
2. **Yetersiz Sınıflandırma**: Hassas verileri düzgün şekilde tanımlamama
3. **Tutarsız Uygulama**: Sınıflandırma kurallarını tutarsız şekilde uygulama
4. **Kötü Eğitim**: Yetersiz çalışan eğitimi ve farkındalık
5. **Teknoloji Bağımlılığı**: Otomatik araçlara çok fazla güvenme
6. **Yönetişim Eksikliği**: Yetersiz gözetim ve yönetim
7. **Sadece Uyumluluk Odaklı**: İş değeri ve riski görmezden gelme
8. **Statik Yaklaşım**: Çerçeveyi güncellememe ve geliştirmeme

## Teknoloji Çözümleri

### Veri Sınıflandırma Araçları

{{< tabs >}}
{{< tab title="Kurumsal DLP" >}}
**Çözümler**:
- Symantec Data Loss Prevention
- McAfee DLP
- Forcepoint DLP
- Digital Guardian

**Özellikler**:
- İçerik farkında sınıflandırma
- Gerçek zamanlı izleme
- Politika uygulama
- Olay müdahale
- Raporlama ve analizler

**Kullanım Alanları**:
- Uç nokta koruması
- Ağ izleme
- E-posta güvenliği
- Bulut veri koruması
{{< /tab >}}

{{< tab title="Bulut Yerli Çözümler" >}}
**Çözümler**:
- Microsoft Information Protection
- Google Cloud DLP
- AWS Macie
- Azure Information Protection

**Özellikler**:
- Bulut yerli sınıflandırma
- Bulut hizmetleri ile entegrasyon
- Otomatik keşif
- Politika uygulama
- Uyumluluk raporlama

**Kullanım Alanları**:
- Bulut depolama koruması
- SaaS uygulama güvenliği
- Çoklu bulut ortamları
- Düzenleyici uyumluluk
{{< /tab >}}

{{< tab title="Açık Kaynak Araçlar" >}}
**Çözümler**:
- Apache Atlas
- Apache Ranger
- OpenDLP
- DataHub

**Özellikler**:
- Meta veri yönetimi
- Erişim kontrolü
- Politika uygulama
- Denetim günlüğü
- Özelleştirme seçenekleri

**Kullanım Alanları**:
- Özel uygulamalar
- Maliyet bilinçli organizasyonlar
- Özel gereksinimler
- Mevcut sistemlerle entegrasyon
{{< /tab >}}
{{< /tabs >}}

## Metrikler ve Ölçüm

### Anahtar Performans Göstergeleri

{{< cards >}}
{{< card title="Sınıflandırma Kapsamı" icon="eye" content="Çerçeveye göre düzgün şekilde sınıflandırılmış ve etiketlenmiş veri varlıklarının yüzdesi." >}}

{{< card title="Uyumluluk Oranı" icon="star" content="Tüm organizasyonda sınıflandırma politikalarına ve düzenleyici gereksinimlere uyum." >}}

{{< card title="Olay Azaltma" icon="exclamation" content="Sınıflandırma uygulamasından sonra veri güvenliği olaylarında ve ihlallerinde azalma." >}}

{{< card title="Kullanıcı Kabulü" icon="users" content="Sınıflandırma prosedürleri ve politikalarına çalışan katılımı ve uyumu." >}}
{{< /cards >}}

### Ölçüm Çerçevesi

```yaml
# Örnek metrikler dashboard yapılandırması
metrikler:
  siniflandirma_kapsami:
    hedef: 95%
    olcum: "Sınıflandırılmış veri varlıklarının yüzdesi"
    siklik: "Aylık"
    
  uyumluluk_orani:
    hedef: 98%
    olcum: "Politika uyumluluk yüzdesi"
    siklik: "Üç aylık"
    
  olay_azaltma:
    hedef: 50%
    olcum: "Yıl üzerinden olay azaltma"
    siklik: "Yıllık"
    
  kullanici_kabulu:
    hedef: 90%
    olcum: "Çalışan eğitim tamamlama"
    siklik: "Aylık"
```

## Sonraki Adımlar

{{< callout type="warning" >}}
**Sürekli İyileştirme**: Veri sınıflandırma, düzenli inceleme ve güncellemeler gerektiren tek seferlik bir proje değil, sürekli bir süreçtir.
{{< /callout >}}

Bu rehber, etkili veri sınıflandırma stratejilerini uygulamak için bir temel sağlar. Kapsamlı veri sınıflandırma hizmetleri ve uzman danışmanlık için şunları düşünün:

- **Veri Sınıflandırma Değerlendirmesi**: Mevcut sınıflandırma olgunluğunuzu değerlendirin
- **Çerçeve Geliştirme**: Özelleştirilmiş sınıflandırma politikaları ve prosedürleri oluşturun
- **Teknoloji Uygulaması**: Uygun araçları ve çözümleri dağıtın
- **Eğitim ve Değişim Yönetimi**: Tüm organizasyonda başarılı benimseme sağlayın
- **Sürekli Destek**: Sınıflandırma programınızı sürdürün ve optimize edin

---

*Veri sınıflandırma ve uygulama konusunda uzman danışmanlık için, veri ortamınıza ve uyumluluk gereksinimlerinize özel profesyonel rehberlik için DeepSec ile iletişime geçin.* 