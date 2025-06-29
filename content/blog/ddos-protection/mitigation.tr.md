---
title: "DDoS savunma Stratejileri ve Uygulama Rehberi"
linkTitle: "DDoS Savunma"
slug: "savunma"
description: "Kapsamlı DDoS savunma stratejileri, uygulama en iyi uygulamaları ve gerçek dünya koruma teknikleri"
translationKey: "mitigation"
weight: 2
layout: docs
type: docs
url: "/tr/blog/ddos-koruma/savunma"

---

{{< callout type="warning" >}}
**Kritik Altyapı Koruması**: Etkili DDoS savunma, ağ, uygulama ve altyapı seviyesi savunmaları birleştiren çok katmanlı bir yaklaşım gerektirir. Bu rehber, dijital varlıklarınızı korumak için kapsamlı stratejiler sunar.
{{< /callout >}}

Bu kapsamlı rehber, kanıtlanmış DDoS savunma stratejilerini ve uygulama tekniklerini incelemektedir. DDoS saldırılarına karşı sağlam bir savunma oluşturmak için ağ seviyesi korumaları, uygulama güvenlik önlemlerini, izleme sistemlerini ve olay müdahale prosedürlerini inceleyeceğiz.

## DDoS Savunmayı Anlama

DDoS savunma, meşru kullanıcılar için hizmet kullanılabilirliğini korurken, kötü niyetli trafiği tespit etmek, filtrelemek ve emmek için birden fazla koruma katmanının uygulanmasını içerir.

{{< callout type="info" >}}
**Derinlikte Savunma**: Başarılı DDoS savunma, ağ altyapısı, uygulama hizmetleri ve iş sürekliliği planlaması dahil olmak üzere birden fazla seviyede koruma gerektirir.
{{< /callout >}}

## Ağ Seviyesi savunma

Ağ seviyesi savunma, altyapı ve bant genişliğini hacimsel saldırılardan ve protokol tabanlı tehditlerden korumaya odaklanır.

### Trafik Temizleme Hizmetleri

{{< tabs >}}
{{< tab title="İsteğe Bağlı Temizleme" >}}
**Açıklama**: Trafik, yalnızca saldırılar tespit edildiğinde temizleme merkezlerinden yönlendirilir, normal operasyonlar sırasında gecikmeyi minimize eder.

**Faydalar**:
- Normal operasyonlar sırasında düşük gecikme
- Arasıra saldırılar için maliyet etkin
- Olaylar sırasında otomatik aktivasyon
- Minimal yapılandırma değişiklikleri

**Uygulama**:
- Temizleme merkezlerine BGP yönlendirme
- DNS tabanlı trafik yönlendirme
- Gerçek zamanlı saldırı tespit tetikleyicileri
{{< /tab >}}

{{< tab title="Sürekli Koruma" >}}
**Açıklama**: Tüm trafik, sürekli koruma için sürekli olarak temizleme merkezlerinden yönlendirilir.

**Faydalar**:
- Sürekli koruma
- Anında saldırı savunma
- Kapsamlı trafik analizi
- Gelişmiş tehdit tespiti

**Düşünceler**:
- Daha yüksek gecikme etkisi
- Artan maliyetler
- Daha karmaşık yönlendirme
{{< /tab >}}

{{< tab title="Hibrit Yaklaşım" >}}
**Açıklama**: Kritik hizmetler için sürekli koruma ile diğerleri için isteğe bağlı temizlemeyi birleştirir.

**Faydalar**:
- Dengeli maliyet ve koruma
- Esnek dağıtım seçenekleri
- Ölçeklenebilir koruma seviyeleri
- Risk tabanlı kaynak tahsisi
{{< /tab >}}
{{< /tabs >}}

### Hız Sınırlama ve Trafik Filtreleme

{{< cards >}}
{{< card title="Bağlantı Hız Sınırlama" icon="cog" content="Bağlantı tükenme saldırılarını ve kaynak tükenmesini önlemek için kaynak IP başına bağlantı sayısını sınırlayın." >}}

{{< card title="Paket Hız Sınırlama" icon="eye" content="Yüksek hacimli saldırılardan bant genişliği doygunluğunu ve altyapı aşırı yüklenmesini önlemek için paket hızlarını kontrol edin." >}}

{{< card title="Coğrafi Filtreleme" icon="star" content="Mevru iş yapılmayan bölgelerden gelen trafiği engelleyerek saldırı yüzeyini azaltın." >}}
{{< /cards >}}

### Uygulama Örnekleri

```bash
# iptables hız sınırlama örneği
# IP başına bağlantıları sınırla
iptables -A INPUT -p tcp --syn -m limit --limit 1/s --limit-burst 3 -j ACCEPT
iptables -A INPUT -p tcp --syn -j DROP

# Saniye başına paketleri sınırla
iptables -A INPUT -m limit --limit 1000/s --limit-burst 100 -j ACCEPT
iptables -A INPUT -j DROP

# Coğrafi filtreleme (ipset kullanarak)
ipset create blacklist hash:net
ipset add blacklist 192.168.1.0/24
iptables -A INPUT -m set --match-set blacklist src -j DROP
```

{{< callout type="warning" >}}
**Yapılandırma Testi**: Meşru trafiğin etkilenmediğinden emin olmak için hız sınırlama yapılandırmalarını her zaman bir test ortamında test edin.
{{< /callout >}}

## Uygulama Seviyesi Koruması

Uygulama seviyesi savunma, web uygulamalarını, API'leri ve hizmetleri sofistike saldırılardan korumaya odaklanır.

### Web Uygulama Güvenlik Duvarı (WAF)

{{< tabs >}}
{{< tab title="İmza Tabanlı Tespit" >}}
**Açıklama**: Önceden tanımlanmış imzalar ve kurallar kullanarak bilinen saldırı kalıplarını tanımlar ve engeller.

**Özellikler**:
- Önceden tanımlanmış saldırı imzaları
- Düzenli imza güncellemeleri
- Düşük yanlış pozitif oranları
- Hızlı tespit ve engelleme

**En İyi Uygulamalar**:
- Düzenli imza güncellemeleri
- Özel kural oluşturma
- Performans izleme
- Düzenli test ve doğrulama
{{< /tab >}}

{{< tab title="Davranış Analizi" >}}
**Açıklama**: Anormal trafik kalıplarını tespit etmek için makine öğrenmesi ve davranış analizi kullanır.

**Özellikler**:
- Uyarlanabilir tehdit tespiti
- Sıfır gün saldırı koruması
- Azaltılmış yanlış pozitifler
- Sürekli öğrenme

**Uygulama**:
- Temel çizgi kurulumu
- Anomali eşik yapılandırması
- Düzenli model güncellemeleri
- Performans optimizasyonu
{{< /tab >}}

{{< tab title="Hız Sınırlama Kuralları" >}}
**Açıklama**: Kullanıcı davranışına ve istek kalıplarına dayalı uygulama özel hız sınırlaması uygular.

**Özellikler**:
- Kullanıcı başına hız sınırlama
- Uç nokta başına koruma
- Oturum tabanlı limitler
- Coğrafi kısıtlamalar

**Yapılandırma**:
- İstek hız eşikleri
- Patlama ödeneği ayarları
- Zaman penceresi yapılandırması
- Eylem tanımları
{{< /tab >}}
{{< /tabs >}}

### Yük Dengeleme ve Trafik Dağıtımı

```bash
# Nginx hız sınırlama yapılandırması
http {
    # Hız sınırlama bölgelerini tanımla
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
    limit_req_zone $binary_remote_addr zone=login:10m rate=1r/s;
    
    server {
        # API uç noktası hız sınırlama
        location /api/ {
            limit_req zone=api burst=20 nodelay;
            proxy_pass http://backend;
        }
        
        # Giriş uç noktası hız sınırlama
        location /login {
            limit_req zone=login burst=5 nodelay;
            proxy_pass http://backend;
        }
    }
}
```

{{< callout type="info" >}}
**WAF En İyi Uygulamaları**: Kapsamlı koruma için imza tabanlı ve davranış tabanlı tespiti birleştirin. Kuralları düzenli olarak güncelleyin ve performans etkisini izleyin.
{{< /callout >}}

## Altyapı Güçlendirme

### Ağ Mimarisi En İyi Uygulamaları

{{< details title="Ağ Güvenliği Güçlendirme Kontrol Listesi" >}}

**Yedeklilik ve Yedekleme**:
- [ ] Çoklu ISP bağlantıları
- [ ] Coğrafi dağılım
- [ ] Otomatik yedekleme sistemleri
- [ ] Sağlayıcılar arası yük dengeleme

**Trafik Yönetimi**:
- [ ] BGP yönlendirme optimizasyonu
- [ ] Anycast DNS dağıtımı
- [ ] CDN entegrasyonu
- [ ] Trafik mühendisliği

**Güvenlik Kontrolleri**:
- [ ] Ağ segmentasyonu
- [ ] Erişim kontrol listeleri
- [ ] Saldırı tespit sistemleri
- [ ] Güvenlik izleme

**Belgelendirme**:
- [ ] Ağ topolojisi belgelendirmesi
- [ ] Olay müdahale prosedürleri
- [ ] İletişim bilgileri
- [ ] Escalation prosedürleri

{{< /details >}}

### Sunucu ve Uygulama Güçlendirme

{{< cards >}}
{{< card title="İşletim Sistemi Güçlendirme" icon="cog" content="Güvenlik temellerini uygulayın, gereksiz hizmetleri devre dışı bırakın ve güvenlik yamalarını düzenli olarak uygulayın." >}}

{{< card title="Uygulama Güvenliği" icon="star" content="Güvenli kodlama uygulamaları kullanın, giriş doğrulaması uygulayın ve düzenli güvenlik değerlendirmeleri yapın." >}}

{{< card title="Kaynak Yönetimi" icon="eye" content="Uygun kaynak limitlerini yapılandırın, bağlantı havuzlaması uygulayın ve uygulama performansını optimize edin." >}}
{{< /cards >}}

## İzleme ve Tespit

Etkili DDoS savunma, kapsamlı izleme ve erken tespit yetenekleri gerektirir.

### Gerçek Zamanlı İzleme Sistemleri

{{< tabs >}}
{{< tab title="Ağ İzleme" >}}
**İzlenecek Metrikler**:
- Bant genişliği kullanımı
- Paket hızları ve akışları
- Bağlantı sayıları
- Protokol dağılımı
- Coğrafi trafik kalıpları

**Araçlar**:
- NetFlow analizi
- SNMP izleme
- Paket yakalama analizi
- Trafik akışı izleme
{{< /tab >}}

{{< tab title="Uygulama İzleme" >}}
**İzlenecek Metrikler**:
- İstek hızları ve kalıpları
- Yanıt süreleri
- Hata oranları
- Kaynak kullanımı
- Kullanıcı davranış kalıpları

**Araçlar**:
- Uygulama performans izleme
- Log analizi
- Gerçek zamanlı paneller
- Uyarı sistemleri
{{< /tab >}}

{{< tab title="Altyapı İzleme" >}}
**İzlenecek Metrikler**:
- CPU ve bellek kullanımı
- Disk I/O performansı
- Ağ arayüzü istatistikleri
- Hizmet kullanılabilirliği
- Kaynak tükenme göstergeleri

**Araçlar**:
- Sistem izleme araçları
- Kaynak takibi
- Performans temel çizgileri
- Kapasite planlama
{{< /tab >}}
{{< /tabs >}}

### Tespit ve Uyarı

```bash
# DDoS tespiti için örnek izleme betiği
#!/bin/bash

# Bağlantı hızlarını izle
CONN_RATE=$(netstat -an | grep ESTABLISHED | wc -l)
THRESHOLD=1000

if [ $CONN_RATE -gt $THRESHOLD ]; then
    echo "Yüksek bağlantı hızı tespit edildi: $CONN_RATE" | mail -s "DDoS Uyarısı" admin@company.com
    
    # savunma önlemlerini etkinleştir
    /usr/local/bin/activate_ddos_protection.sh
fi

# Bant genişliği kullanımını izle
BW_USAGE=$(iftop -t -s 10 -L 100 2>/dev/null | grep "Total send rate" | awk '{print $4}')
BW_THRESHOLD="100M"

if [[ "$BW_USAGE" > "$BW_THRESHOLD" ]]; then
    echo "Yüksek bant genişliği kullanımı tespit edildi: $BW_USAGE" | mail -s "Bant Genişliği Uyarısı" admin@company.com
fi
```

{{< callout type="warning" >}}
**Uyarı Eşikleri**: Temel çizgi trafik kalıplarına dayalı uygun eşikler belirleyin. Çok düşük eşikler yanlış pozitiflere neden olurken, çok yüksek eşikler yanıtı geciktirir.
{{< /callout >}}

## Olay Müdahale Prosedürleri

### DDoS Olay Müdahale Planı

{{< details title="Olay Müdahale İş Akışı" >}}

### Faz 1: Tespit ve Değerlendirme
- [ ] Uyarı sistemlerini izle
- [ ] Saldırı türünü ve kapsamını doğrula
- [ ] Potansiyel etkiyi değerlendir
- [ ] Olay müdahale ekibini etkinleştir
- [ ] İlk gözlemleri belgelendir

### Faz 2: Anında Yanıt
- [ ] DDoS koruma hizmetlerini etkinleştir
- [ ] Acil durum hız sınırlaması uygula
- [ ] Paydaşları ve müşterileri bilgilendir
- [ ] Saldırı ilerlemesini izle
- [ ] Yapılan tüm eylemleri belgelendir

### Faz 3: savunma ve Kurtarma
- [ ] Ek koruma önlemlerini dağıt
- [ ] Hizmet kullanılabilirliğini izle
- [ ] Koruma seviyelerini gerektiği gibi ayarla
- [ ] Durum güncellemelerini iletişim kur
- [ ] Kurtarma prosedürlerini başlat

### Faz 4: Olay Sonrası Analiz
- [ ] Olay incelemesi yap
- [ ] Saldırı kalıplarını analiz et
- [ ] Koruma önlemlerini güncelle
- [ ] Öğrenilen dersleri belgelendir
- [ ] Müdahale prosedürlerini güncelle

{{< /details >}}

### İletişim Planı

{{< callout type="info" >}}
**Paydaş İletişimi**: DDoS olayları sırasında müşteriler, ortaklar ve iç ekiplerle net iletişim kanallarını sürdürün.
{{< /callout >}}

**İletişim Şablonları**:
- Müşteri bildirimleri
- İç durum güncellemeleri
- Yönetici brifingleri
- Teknik ekip iletişimi
- Halkla ilişkiler açıklamaları

## Gelişmiş savunma Teknikleri

### Makine Öğrenmesi ve Yapay Zeka

Modern DDoS savunma, gelişmiş tespit ve yanıt için yapay zekayı kullanır:

```python
# ML tabanlı anomali tespiti örneği
import numpy as np
from sklearn.ensemble import IsolationForest

def detect_anomalies(traffic_data):
    # Isolation Forest modelini eğit
    model = IsolationForest(contamination=0.1)
    model.fit(traffic_data)
    
    # Anomalileri tahmin et
    predictions = model.predict(traffic_data)
    
    # Anormal trafiği döndür
    return traffic_data[predictions == -1]
```

### Bulut Tabanlı Koruma

{{< cards >}}
{{< card title="Cloudflare Koruması" icon="cloud" content="Yerleşik DDoS koruması, hız sınırlama ve güvenlik özellikleri ile global CDN." >}}

{{< card title="AWS Shield" icon="cloud" content="Otomatik savunma ve izleme ile AWS kaynakları için yönetilen DDoS koruması." >}}

{{< card title="Azure DDoS Koruması" icon="cloud" content="Uyarlanabilir ayarlama ve gerçek zamanlı izleme ile Microsoft'un DDoS koruma hizmeti." >}}
{{< /cards >}}

## Performans Optimizasyonu

### savunma Performans En İyi Uygulamaları

1. **Gecikme Optimizasyonu**:
   - Temizleme için kenar konumları kullanın
   - Önbellekleme stratejileri uygulayın
   - Yönlendirme yollarını optimize edin
   - Performans etkisini izleyin

2. **Kaynak Yönetimi**:
   - Koruma kaynaklarını ölçeklendirin
   - Kaynak havuzlaması uygulayın
   - Kaynak kullanımını izleyin
   - Kapasite büyümesi için plan yapın

3. **Maliyet Optimizasyonu**:
   - Uygun koruma seviyelerini seçin
   - Kullanım kalıplarını izleyin
   - Kaynak tahsisini optimize edin
   - Fiyatlandırma modellerini gözden geçirin

{{< callout type="warning" >}}
**Performans vs. Koruma**: Koruma etkinliğini performans gereksinimleriyle dengeleyin. savunma önlemlerini gerçekçi koşullar altında test edin.
{{< /callout >}}

## Test ve Doğrulama

### DDoS Koruması Testleri

{{< tabs >}}
{{< tab title="Simülasyon Testleri" >}}
**Açıklama**: Koruma etkinliğini test etmek için kontrollü ortamlarda DDoS saldırılarını simüle edin.

**Faydalar**:
- Güvenli test ortamı
- Tekrarlanabilir test senaryoları
- Performans ölçümü
- Boşluk tanımlama

**Test Türleri**:
- Hacim saldırısı simülasyonu
- Protokol saldırısı testleri
- Uygulama seviyesi testleri
- Çok vektörlü saldırı simülasyonu
{{< /tab >}}

{{< tab title="Penetrasyon Testleri" >}}
**Açıklama**: Güvenlik açıklarını tanımlamak ve koruma önlemlerini doğrulamak için profesyonel güvenlik testleri.

**Kapsam**:
- Ağ altyapısı testleri
- Uygulama güvenliği değerlendirmesi
- Yapılandırma incelemesi
- Olay müdahale testleri

**Sıklık**:
- Yıllık kapsamlı testler
- Üç aylık hedefli testler
- Aylık otomatik testler
- Sürekli izleme
{{< /tab >}}

{{< tab title="Kırmızı Takım Egzersizleri" >}}
**Açıklama**: Gerçek dünya saldırı koşullarını simüle eden gelişmiş test senaryoları.

**Hedefler**:
- Tespit yeteneklerini test et
- Müdahale prosedürlerini doğrula
- Ekip hazırlığını değerlendir
- İyileştirme alanlarını tanımla
{{< /tab >}}
{{< /tabs >}}

## Uyumluluk ve Raporlama

### Düzenleyici Uyumluluk

DDoS savunma, çeşitli düzenleyici gereksinimlerle uyumlu olmalıdır:

- **KVKK**: Olaylar sırasında veri koruması
- **SOX**: Finansal sistem kullanılabilirliği
- **HIPAA**: Sağlık sistemi koruması
- **PCI DSS**: Ödeme sistemi güvenliği

### Raporlama ve Belgelendirme

{{< callout type="info" >}}
**Belgelendirme Gereksinimleri**: savunma önlemlerinin, olay müdahalelerinin ve uyumluluk faaliyetlerinin kapsamlı belgelendirmesini sürdürün.
{{< /callout >}}

**Gerekli Belgelendirme**:
- savunma stratejisi belgeleri
- Olay müdahale prosedürleri
- Uyumluluk raporları
- Performans metrikleri
- Denetim izleri

## En İyi Uygulamalar Özeti

{{< details title="DDoS savunma En İyi Uygulamalar Kontrol Listesi" >}}

### Hazırlık
- [ ] Kapsamlı savunma stratejisi geliştir
- [ ] Çok katmanlı koruma uygula
- [ ] İzleme ve uyarı kur
- [ ] Olay müdahale prosedürleri oluştur
- [ ] Müdahale ekiplerini eğit

### Uygulama
- [ ] Ağ seviyesi korumaları dağıt
- [ ] Uygulama güvenliği uygula
- [ ] İzleme sistemlerini yapılandır
- [ ] Koruma önlemlerini test et
- [ ] Yapılandırmaları belgelendir

### Bakım
- [ ] Düzenli güvenlik güncellemeleri
- [ ] Performans izleme
- [ ] Kapasite planlama
- [ ] Olay müdahale tatbikatları
- [ ] Strateji incelemeleri

### Sürekli İyileştirme
- [ ] Saldırı kalıplarını analiz et
- [ ] Koruma önlemlerini güncelle
- [ ] Tespit yeteneklerini geliştir
- [ ] Performansı optimize et
- [ ] Tehditlerle güncel kal

{{< /details >}}

## Sonraki Adımlar

{{< callout type="warning" >}}
**Proaktif Koruma**: DDoS koruması uygulamak için bir saldırı beklemeyin. Temel önlemlerle başlayın ve zamanla kapsamlı koruma oluşturun.
{{< /callout >}}

Bu rehber, etkili DDoS savunma stratejilerini uygulamak için bir temel sağlar. Kapsamlı DDoS koruma hizmetleri ve uzman danışmanlık için şunları düşünün:

- **Profesyonel DDoS Koruma Hizmetleri**: 7/24 izleme ile yönetilen koruma
- **Güvenlik Değerlendirmesi**: Mevcut koruma önlemlerinizi değerlendirin
- **Uygulama Desteği**: Dağıtım için profesyonel rehberlik
- **Sürekli Yönetim**: Sürekli izleme ve optimizasyon

---

*DDoS savunma ve uygulama konusunda uzman danışmanlık için, altyapınıza ve gereksinimlerinize özel profesyonel rehberlik için DeepSec ile iletişime geçin.* 