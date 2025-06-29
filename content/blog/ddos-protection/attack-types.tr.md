---
title: "DDoS Saldırı Türleri ve Korunma Stratejileri"
linkTitle: "DDoS Saldırı Türleri"
slug: "saldiri-turleri"
description: "DDoS saldırı türleri, özellikleri ve etkili korunma stratejileri için kapsamlı rehber"
translationKey: "attack-types"
weight: 1
layout: docs
type: docs
url: "/tr/blog/ddos-koruma/saldiri-turleri"

---

{{< callout type="warning" >}}
**Kritik Güvenlik Tehdidi**: DDoS saldırıları önemli kesinti süreleri, mali kayıplar ve organizasyonunuzun itibarına zarar verebilir. Saldırı türlerini anlamak etkili koruma için ilk adımdır.
{{< /callout >}}

Bu kapsamlı rehber, organizasyonların bugün karşılaştığı Dağıtık Hizmet Reddi (DDoS) saldırılarının çeşitli türlerini inceler. Her kategori için saldırı özelliklerini, tespit yöntemlerini ve kanıtlanmış korunma stratejilerini inceleyeceğiz.

## DDoS Saldırılarını Anlama

DDoS saldırıları, hedef sistemleri kötü niyetli trafikle aşırı yüklemeyi ve bunları meşru kullanıcılar için erişilemez hale getirmeyi amaçlar. Bu saldırılar yaklaşımlarına ve hedeflerine göre üç ana tipe kategorize edilebilir.

{{< callout type="info" >}}
**Saldırı Evrimi**: Modern DDoS saldırıları genellikle birden fazla saldırı vektörünü birleştirir, bu da onları tespit etmeyi ve korunmayı daha zor hale getirir. Kapsamlı bir savunma stratejisi tüm saldırı türlerini ele almalıdır.
{{< /callout >}}

## Hacim Tabanlı Saldırılar

Hacim tabanlı saldırılar hedefi büyük miktarda trafikle doldurur ve ağ bant genişliğini ve altyapı kapasitesini aşırı yükler.

### Yaygın Hacim Saldırı Türleri

{{< tabs >}}
{{< tab title="UDP Flood" >}}
**Açıklama**: Saldırganlar hedef sisteme rastgele portlara büyük miktarda UDP paketi gönderir ve sistemin ICMP "hedef erişilemez" mesajlarıyla yanıt vermesini zorlar.

**Özellikler**:
- Yüksek bant genişliği tüketimi
- Ağ altyapısını hedefler
- Meşru trafiği etkilemeden filtrelemek zor

**Korunma**:
- UDP trafiği için hız sınırlama
- Trafik temizleme hizmetleri
- DDoS koruma cihazları
{{< /tab >}}

{{< tab title="ICMP Flood" >}}
**Açıklama**: Saldırganlar hedefi ICMP echo request (ping) paketleriyle aşırı yükler ve ağ kaynaklarını ve bant genişliğini tüketir.

**Özellikler**:
- Yürütmesi basit
- Yüksek amplifikasyon potansiyeli
- Ağ katmanını hedefler

**Korunma**:
- ICMP hız sınırlama
- ICMP'yi engelleyen güvenlik duvarı kuralları
- Olağandışı ICMP desenleri için ağ izleme
{{< /tab >}}

{{< tab title="DNS Amplifikasyon" >}}
**Açıklama**: Saldırganlar, hedefe büyük yanıtlar üreten küçük sorgular göndererek DNS sunucularını saldırı trafiğini amplifiye etmek için kullanır.

**Özellikler**:
- Yüksek amplifikasyon oranı (70:1'e kadar)
- Meşru DNS altyapısını kullanır
- Kaynağa geri izlemek zor

**Korunma**:
- DNS yanıt hız sınırlama
- Anycast DNS dağıtımı
- Kaynak IP doğrulama
{{< /tab >}}
{{< /tabs >}}

### Hacim Saldırı Tespiti

```bash
# Ağ bant genişliği kullanımını izle
netstat -i

# Olağandışı trafik desenlerini kontrol et
tcpdump -i eth0 -w capture.pcap

# Trafik akışlarını analiz et
iftop -i eth0

# Sistem kaynaklarını izle
top -p $(pgrep -d',' -f "network|http")
```

{{< callout type="warning" >}}
**Tespit Zorluğu**: Hacim saldırıları meşru trafik artışlarından ayırt etmek zor olabilir. Normal trafik desenlerini oluşturmak için temel izleme uygulayın.
{{< /callout >}}

## Protokol Saldırıları

Protokol saldırıları ağ protokollerindeki zayıflıkları kullanır ve bant genişliği yerine sunucu kaynaklarını tüketir.

### Yaygın Protokol Saldırı Türleri

{{< cards >}}
{{< card title="SYN Flood" icon="exclamation" content="Sunucuları TCP SYN paketleriyle aşırı yükler, bağlantıları yarı açık durumda bırakır ve bağlantı tablolarını tüketir." >}}

{{< card title="ACK Flood" icon="exclamation" content="İşlem kaynaklarını tüketmek ve meşru bağlantıları bozmak için büyük miktarda ACK paketi gönderir." >}}

{{< card title="Parçalama Saldırıları" icon="exclamation" content="Güvenlik önlemlerini atlatmak ve yeniden birleştirme tamponlarını aşırı yüklemek için IP parçalamasını kullanır." >}}
{{< /cards >}}

### SYN Flood Saldırı Detayları

SYN flood saldırıları özellikle tehlikelidir çünkü TCP el sıkışma sürecini hedefler:

```bash
# SYN flood tespiti örneği
# Saniyede SYN paketlerini izle
tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0' | wc -l

# Eksik bağlantıları kontrol et
netstat -an | grep SYN_RECV | wc -l

# Bağlantı tablosu kullanımını izle
cat /proc/net/sockstat
```

**Korunma Stratejileri**:
- SYN cookie uygulaması
- Bağlantı hız sınırlama
- TCP zaman aşımı optimizasyonu
- Yük dengeleyici koruması

{{< callout type="info" >}}
**Protokol Koruması**: Uygun TCP/IP yığını sertleştirme uygulayın ve SYN flood saldırılarının sunucu kaynaklarını tüketmesini önlemek için SYN cookie'leri kullanın.
{{< /callout >}}

## Uygulama Katmanı Saldırıları

Uygulama katmanı saldırıları belirli uygulamaları ve hizmetleri hedefler ve tespit etmek ve Korunmak daha zordur.

### HTTP Tabanlı Saldırılar

{{< tabs >}}
{{< tab title="HTTP Flood" >}}
**Açıklama**: Web sunucularını meşru görünen HTTP istekleriyle aşırı yükler ve uygulama kaynaklarını tüketir.

**Türler**:
- GET/POST flood'ları
- Önbellek bozma saldırıları
- Oturum tükenmesi

**Tespit**:
- IP başına istek oranlarını izle
- User agent desenlerini analiz et
- Oturum oluşturma oranlarını takip et
{{< /tab >}}

{{< tab title="Slowloris" >}}
**Açıklama**: Hedef sunucuya birçok bağlantı sürdürür ve bağlantıları canlı tutmak için kısmi HTTP istekleri gönderir.

**Özellikler**:
- Düşük bant genişliği kullanımı
- Tespit etmek zor
- Bağlantı limitlerini hedefler

**Korunma**:
- Bağlantı zaman aşımı ayarları
- İstek hız sınırlama
- Yük dengeleyici koruması
{{< /tab >}}

{{< tab title="SSL/TLS Saldırıları" >}}
**Açıklama**: Kriptografik işlemler yoluyla sunucu CPU kaynaklarını tüketmek için SSL/TLS el sıkışma sürecini kullanır.

**Türler**:
- SSL yeniden müzakere saldırıları
- TLS el sıkışma flood'ları
- Sertifika doğrulama saldırıları

**Korunma**:
- SSL offloading
- Bağlantı hız sınırlama
- Donanım hızlandırma
{{< /tab >}}
{{< /tabs >}}

### Uygulama Saldırı Tespiti

```bash
# HTTP istek oranlarını izle
tail -f /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -nr

# Yavaş bağlantıları kontrol et
netstat -an | grep ESTABLISHED | awk '{print $5}' | cut -d: -f1 | sort | uniq -c

# SSL el sıkışma performansını izle
openssl s_time -connect target:443 -new -time 10
```

## Gelişmiş Saldırı Vektörleri

Modern DDoS saldırıları genellikle maksimum etki için birden fazla tekniği birleştirir.

### Çok Vektörlü Saldırılar

{{< details title="Gelişmiş Saldırı Desenleri" >}}

**Yansıma Saldırıları**:
- DNS yansıması
- NTP amplifikasyonu
- SNMP amplifikasyonu
- SSDP amplifikasyonu

**Botnet Tabanlı Saldırılar**:
- IoT cihaz sömürüsü
- Ele geçirilmiş sunucular
- Konut proxy ağları
- Bulut hizmet kötüye kullanımı

**Hedefli Uygulama Saldırıları**:
- API endpoint flood'ları
- Veritabanı bağlantı tükenmesi
- Kimlik doğrulama hizmeti saldırıları
- CDN atlatma teknikleri

{{< /details >}}

## Saldırı Tespiti ve İzleme

Etkili DDoS koruması kapsamlı izleme ve tespit yetenekleri gerektirir.

### İzleme Metrikleri

{{< cards >}}
{{< card title="Ağ Metrikleri" icon="eye" content="Tüm ağ arayüzlerinde bant genişliği kullanımı, paket oranları, bağlantı sayıları ve trafik desenleri." >}}

{{< card title="Uygulama Metrikleri" icon="search" content="Web uygulamaları ve hizmetler için istek oranları, yanıt süreleri, hata oranları ve kaynak kullanımı." >}}

{{< card title="Altyapı Metrikleri" icon="cog" content="CPU kullanımı, bellek tüketimi, disk I/O ve sistem kaynak kullanılabilirliği." >}}
{{< /cards >}}

### Tespit Araçları ve Teknikleri

```bash
# Ağ trafik analizi
# Bant genişliği kullanımını izle
iftop -i eth0 -t

# Paket akışlarını analiz et
tcpdump -i eth0 -w capture.pcap -c 1000

# Bağlantı durumlarını izle
ss -tuln | grep :80

# Uygulama izleme
# Web sunucu loglarını kontrol et
tail -f /var/log/apache2/access.log | grep -E "(404|500|503)"

# Veritabanı bağlantılarını izle
mysql -e "SHOW PROCESSLIST;" | grep -v Sleep

# Sistem kaynak izleme
# CPU ve bellek kullanımı
top -b -n 1

# Ağ arayüz istatistikleri
cat /proc/net/dev
```

{{< callout type="warning" >}}
**Gerçek Zamanlı İzleme**: Saldırıları erken tespit etmek ve etkileri minimize etmek için uyarı eşikleriyle otomatik izleme uygulayın.
{{< /callout >}}

## Korunma Stratejileri

Kapsamlı bir DDoS koruma stratejisi birden fazla savunma katmanı gerektirir.

### Ağ Seviyesi Koruması

{{< tabs >}}
{{< tab title="Trafik Temizleme" >}}
**Açıklama**: Kötü niyetli trafiği filtreleyen özel DDoS koruma hizmetleri aracılığıyla trafiği yönlendirin.

**Faydalar**:
- Gerçek zamanlı trafik analizi
- Otomatik saldırı tespiti
- Minimal gecikme etkisi
- 24/7 koruma

**Uygulama**:
- Temizleme merkezlerine BGP yönlendirme
- DNS tabanlı trafik yönlendirme
- İsteğe bağlı aktivasyon
{{< /tab >}}

{{< tab title="Hız Sınırlama" >}}
**Açıklama**: Ağ ve uygulama kaynaklarının aşırı yüklenmesini önlemek için trafik hız limitleri uygulayın.

**Yapılandırma**:
- Bağlantı hız limitleri
- İstek hız limitleri
- Bant genişliği kısıtlama
- Coğrafi kısıtlamalar

**Araçlar**:
- iptables/nftables
- Yük dengeleyiciler
- Web uygulama güvenlik duvarları
{{< /tab >}}

{{< tab title="Trafik Filtreleme" >}}
**Açıklama**: Bilinen saldırı desenlerini ve kötü niyetli trafik kaynaklarını engellemek için güvenlik duvarları ve filtreleme kuralları kullanın.

**Teknikler**:
- IP itibar filtreleme
- Protokol doğrulama
- Yük inceleme
- Davranış analizi
{{< /tab >}}
{{< /tabs >}}

### Uygulama Seviyesi Koruması

{{< callout type="info" >}}
**Derinlikte Savunma**: Kapsamlı DDoS Korunma için ağ ve uygulama seviyesi korumalarını birleştirin.
{{< /callout >}}

**Web Uygulama Güvenlik Duvarları (WAF)**:
- İstek filtreleme ve doğrulama
- Hız sınırlama ve kısıtlama
- Bot tespiti ve engelleme
- Özel kural oluşturma

**Yük Dengeleme**:
- Trafik dağıtımı
- Sağlık kontrolü
- Otomatik failover
- Coğrafi yük dengeleme

**CDN Koruması**:
- Edge önbellekleme
- Trafik emme
- Coğrafi dağıtım
- DDoS Korunma hizmetleri

## Olay Müdahale Planı

{{< details title="DDoS Olay Müdahale Kontrol Listesi" >}}

### Hazırlık Aşaması
- [ ] İzleme ve uyarı sistemleri kurun
- [ ] Müdahale ekibi rolleri ve sorumluluklarını tanımlayın
- [ ] İletişim prosedürleri oluşturun
- [ ] Eskalasyon süreçlerini belgeleyin
- [ ] Müdahale prosedürlerini düzenli olarak test edin

### Tespit Aşaması
- [ ] Olağandışı trafik desenlerini izleyin
- [ ] Sistem performans metriklerini analiz edin
- [ ] Saldırı türünü ve kapsamını doğrulayın
- [ ] Potansiyel etkiyi değerlendirin
- [ ] Olay müdahale ekibini aktifleştirin

### Müdahale Aşaması
- [ ] Acil Korunma önlemlerini uygulayın
- [ ] DDoS koruma hizmetlerini aktifleştirin
- [ ] Paydaşlarla iletişim kurun
- [ ] Saldırı ilerlemesini izleyin
- [ ] Yapılan tüm eylemleri belgeleyin

### Kurtarma Aşaması
- [ ] Saldırının bittiğini doğrulayın
- [ ] Hasar ve etkiyi değerlendirin
- [ ] Normal operasyonları geri yükleyin
- [ ] Saldırı desenlerini analiz edin
- [ ] Koruma önlemlerini güncelleyin

### Olay Sonrası
- [ ] Olay incelemesi yapın
- [ ] Müdahale prosedürlerini güncelleyin
- [ ] Öğrenilen dersleri uygulayın
- [ ] Bulguları ekip ile paylaşın
- [ ] Dokümantasyonu güncelleyin

{{< /details >}}

## En İyi Uygulamalar

{{< callout type="warning" >}}
**Sürekli İyileştirme**: DDoS koruması sürekli izleme, test ve yeni saldırı vektörlerine adaptasyon gerektirir.
{{< /callout >}}

### Önleme Stratejileri

1. **Ağ Mimarisi**:
   - Yedekli bağlantılar uygulayın
   - Birden fazla ISP kullanın
   - Trafik temizleme hizmetleri dağıtın
   - Uygun hız sınırlama yapılandırın

2. **Uygulama Güvenliği**:
   - WAF koruması kullanın
   - Uygun kimlik doğrulama uygulayın
   - Düzenli güvenlik güncellemeleri
   - Uygulama performansını izleyin

3. **İzleme ve Uyarı**:
   - Gerçek zamanlı trafik izleme
   - Otomatik uyarı sistemleri
   - Düzenli penetrasyon testleri
   - Olay müdahale tatbikatları

4. **Hizmet Sağlayıcı Seçimi**:
   - DDoS koruması olan sağlayıcıları seçin
   - Koruma yeteneklerini doğrulayın
   - Hizmet seviyesi anlaşmalarını anlayın
   - Koruma etkinliğini test edin

## Sonraki Adımlar

{{< callout type="warning" >}}
**Proaktif Koruması**: DDoS koruması uygulamak için saldırı beklemeyin. Temel önlemlerle başlayın ve zamanla kapsamlı koruma oluşturun.
{{< /callout >}}

Bu rehber, DDoS saldırılarını anlama ve koruma önlemleri uygulama için bir temel sağlar. Kapsamlı DDoS koruma hizmetleri ve uzman danışmanlığı için şunları düşünün:

- **Profesyonel DDoS Koruma Hizmetleri**: 24/7 izleme ile yönetilen koruma
- **Güvenlik Değerlendirmesi**: Mevcut koruma önlemlerinizi değerlendirin
- **Olay Müdahale Planlaması**: Kapsamlı müdahale prosedürleri geliştirin
- **Personel Eğitimi**: Ekipleri saldırı tanıma ve müdahale konusunda eğitin

---

*DDoS koruma danışmanlığı ve uygulaması için, özel altyapınıza ve gereksinimlerinize göre uyarlanmış profesyonel rehberlik için DeepSec ile iletişime geçin.* 