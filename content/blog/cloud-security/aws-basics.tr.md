---
title: "AWS Güvenlik Temelleri"
linkTitle: "AWS Güvenlik Temelleri"
slug: "aws-temelleri"
description: "Bulut altyapısı koruması için temel AWS güvenlik uygulamaları ve en iyi yöntemler"
translationKey: "aws-basics"
weight: 1
layout: docs
type: docs
url: "/tr/blog/bulut-guvenligi/aws-temelleri"
---

{{< callout type="warning" >}}
**Güvenlik Öncelik**: AWS güvenliği paylaşılan sorumluluk modelidir. AWS bulut altyapısını güvence altına alırken, siz bulut içindeki uygulamalarınızı ve verilerinizi güvence altına almakla sorumlusunuz.
{{< /callout >}}

Bu rehber, her bulut mimarı ve güvenlik uzmanının anlaması gereken temel AWS güvenlik temellerini kapsar. Kimlik ve Erişim Yönetimi (IAM), Sanal Özel Bulut (VPC) güvenliği, şifreleme stratejileri ve izleme en iyi uygulamalarını inceleyeceğiz.

## Kimlik ve Erişim Yönetimi (IAM)

IAM, AWS güvenliğinin temelidir. AWS kaynaklarınıza kimlerin erişebileceğini ve bunlarla ne yapabileceklerini kontrol eder.

### IAM Temel Prensipleri

{{< tabs >}}
{{< tab title="En Az Ayrıcalık Prensibi" >}}
Kullanıcılara ve uygulamalara görevlerini yerine getirmek için gereken minimum izinleri verin. Bu, saldırı yüzeyini azaltır ve tehlikeye girmiş kimlik bilgilerinden kaynaklanan potansiyel zararları sınırlar.

**Örnek**: Bir geliştiricisine tam yönetici erişimi vermek yerine, sadece projesinin kaynakları için özel izinler verin.
{{< /tab >}}

{{< tab title="Rol Tabanlı Erişim" >}}
Mümkün olduğunda uzun süreli erişim anahtarları yerine IAM rollerini kullanın. Roller geçici kimlik bilgileri sağlar ve sabit kodlanmış erişim anahtarlarından daha güvenlidir.

**En İyi Uygulama**: Geçici kimlik bilgileri için AWS STS (Security Token Service) kullanın.
{{< /tab >}}

{{< tab title="Çok Faktörlü Kimlik Doğrulama" >}}
Tüm IAM kullanıcıları için, özellikle yönetici ayrıcalıklarına sahip olanlar için MFA'yı etkinleştirin. MFA, şifrelerin ötesinde ek bir güvenlik katmanı ekler.
{{< /tab >}}
{{< /tabs >}}

### IAM Politikası Örneği

İşte bir geliştirme ekibi için iyi yapılandırılmış bir IAM politikası örneği:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEC2ReadAccess",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeVpcs"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/Environment": "development"
                }
            }
        },
        {
            "Sid": "DenyDeleteOperations",
            "Effect": "Deny",
            "Action": [
                "ec2:DeleteInstance",
                "ec2:DeleteSecurityGroup",
                "ec2:DeleteVpc"
            ],
            "Resource": "*"
        }
    ]
}
```

{{< callout type="info" >}}
**Politika En İyi Uygulamaları**: Hassas işlemler için her zaman açık red ifadeleri kullanın ve mümkün olduğunda kaynak seviyesinde izinler uygulayın.
{{< /callout >}}

## Sanal Özel Bulut (VPC) Güvenliği

VPC, AWS kaynaklarınız üzerinde ağ izolasyonu ve kontrol sağlar. Düzgün VPC yapılandırması güvenlik için kritiktir.

### Ağ Mimarisi

{{< cards >}}
{{< card title="Genel Alt Ağlar" icon="cloud" content="Yük dengeleyiciler ve köprü ana bilgisayarlar gibi internet yönelimli kaynakları barındırın. Koruma için Network ACL'ler ve Güvenlik Grupları kullanın." >}}

{{< card title="Özel Alt Ağlar" icon="eye" content="Uygulama sunucularını ve veritabanlarını barındırın. Doğrudan internet erişimi yok - trafik NAT Gateway üzerinden yönlendirilir." >}}

{{< card title="Veritabanı Alt Ağları" icon="star" content="RDS ve diğer veritabanları için izole alt ağlar. Ek güvenlik katmanları ve şifreleme gerekli." >}}
{{< /cards >}}

### Güvenlik Grubu Yapılandırması

Güvenlik Grupları, EC2 örnekleriniz için sanal güvenlik duvarları görevi görür:

```bash
# Web sunucuları için güvenlik grubu oluştur
aws ec2 create-security-group \
    --group-name WebServerSG \
    --description "Web sunucuları için güvenlik grubu" \
    --vpc-id vpc-12345678

# HTTP trafiğine izin ver
aws ec2 authorize-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0

# HTTPS trafiğine izin ver
aws ec2 authorize-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 443 \
    --cidr 0.0.0.0/0

# Belirli IP aralığından SSH'a izin ver
aws ec2 authorize-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 22 \
    --cidr 10.0.0.0/16
```

{{< callout type="warning" >}}
**Güvenlik Grubu Kuralları**: Her zaman en az ayrıcalık prensibini takip edin. Mutlak gerekli olmadıkça SSH erişimi için asla 0.0.0.0/0 kullanmayın.
{{< /callout >}}

## Şifreleme Stratejileri

AWS, verilerinizi hem beklerken hem de aktarılırken korumak için birden fazla şifreleme katmanı sağlar.

### Bekleyen Verilerin Şifrelenmesi

{{< tabs >}}
{{< tab title="S3 Şifreleme" >}}
Tüm S3 bucket'ları için sunucu tarafı şifrelemeyi etkinleştirin:

```bash
# Varsayılan şifreleme ile bucket oluştur
aws s3api create-bucket \
    --bucket guvenli-bucketim \
    --region us-east-1

# Varsayılan şifrelemeyi etkinleştir
aws s3api put-bucket-encryption \
    --bucket guvenli-bucketim \
    --server-side-encryption-configuration '{
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "AES256"
                }
            }
        ]
    }'
```
{{< /tab >}}

{{< tab title="RDS Şifreleme" >}}
RDS örnekleri için şifrelemeyi etkinleştirin:

```bash
# Şifrelenmiş RDS örneği oluştur
aws rds create-db-instance \
    --db-instance-identifier veritabanim \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username admin \
    --master-user-password sifrem \
    --storage-encrypted \
    --kms-key-id arn:aws:kms:us-east-1:123456789012:key/abcd1234-ef56-7890-abcd-ef1234567890
```
{{< /tab >}}

{{< tab title="EBS Şifreleme" >}}
EBS volume'ları için şifrelemeyi etkinleştirin:

```bash
# Şifrelenmiş EBS volume oluştur
aws ec2 create-volume \
    --size 100 \
    --availability-zone us-east-1a \
    --encrypted \
    --kms-key-id arn:aws:kms:us-east-1:123456789012:key/abcd1234-ef56-7890-abcd-ef1234567890
```
{{< /tab >}}
{{< /tabs >}}

### Aktarılan Verilerin Şifrelenmesi

Veri aktarımı için her zaman TLS/SSL kullanın:

```bash
# ALB için HTTPS listener yapılandır
aws elbv2 create-listener \
    --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/alb-im/1234567890abcdef \
    --protocol HTTPS \
    --port 443 \
    --certificates CertificateArn=arn:aws:acm:us-east-1:123456789012:certificate/abcd1234-ef56-7890-abcd-ef1234567890 \
    --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/tg-im/1234567890abcdef
```

## İzleme ve Günlük Kaydı

Kapsamlı izleme ve günlük kaydı, güvenlik ve uyumluluk için gereklidir.

### CloudTrail Yapılandırması

API aktivite günlük kaydı için CloudTrail'i etkinleştirin:

```bash
# CloudTrail oluştur
aws cloudtrail create-trail \
    --name guvenlik-trail-im \
    --s3-bucket-name cloudtrail-bucket-im \
    --include-global-service-events \
    --is-multi-region-trail

# Günlük kaydını başlat
aws cloudtrail start-logging \
    --name guvenlik-trail-im
```

### CloudWatch Alarmları

Güvenlik odaklı CloudWatch alarmları kurun:

```bash
# Başarısız giriş denemeleri için alarm oluştur
aws cloudwatch put-metric-alarm \
    --alarm-name "BasarisizGirisDenemeleri" \
    --alarm-description "Birden fazla başarısız giriş denemesinde uyarı" \
    --metric-name FailedLoginAttempts \
    --namespace AWS/IAM \
    --statistic Sum \
    --period 300 \
    --threshold 5 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 1
```

{{< callout type="info" >}}
**İzleme En İyi Uygulamaları**: Olağandışı aktiviteler, başarısız kimlik doğrulama denemeleri ve yetkisiz API çağrıları için uyarılar kurun.
{{< /callout >}}

## Güvenlik Kontrol Listesi

{{< details title="AWS Güvenlik Uygulama Kontrol Listesi" >}}

### Kimlik ve Erişim Yönetimi
- [ ] Tüm IAM kullanıcıları için MFA'yı etkinleştirin
- [ ] Erişim anahtarları yerine IAM rollerini kullanın
- [ ] En az ayrıcalık politikalarını uygulayın
- [ ] Düzenli erişim incelemeleri ve temizlik
- [ ] CloudTrail günlük kaydını etkinleştirin

### Ağ Güvenliği
- [ ] Özel alt ağlarla VPC'yi yapılandırın
- [ ] Minimum kurallarla güvenlik gruplarını uygulayın
- [ ] Ek koruma için Network ACL'leri kullanın
- [ ] VPC Flow Logs'u etkinleştirin
- [ ] Özel örnekler için NAT Gateway yapılandırın

### Veri Koruması
- [ ] Tüm S3 bucket'ları için şifrelemeyi etkinleştirin
- [ ] RDS örneklerini şifreleyin
- [ ] Şifrelenmiş EBS volume'ları kullanın
- [ ] Tüm bağlantılar için TLS/SSL uygulayın
- [ ] Anahtar yönetimi için AWS KMS kullanın

### İzleme ve Uyumluluk
- [ ] CloudWatch alarmları kurun
- [ ] Tüm bölgeler için CloudTrail'i yapılandırın
- [ ] Uyumluluk izleme için AWS Config'i etkinleştirin
- [ ] Düzenli güvenlik değerlendirmeleri
- [ ] Güvenlik prosedürlerini belgeleyin

{{< /details >}}

## Sonraki Adımlar

{{< callout type="warning" >}}
**Sürekli Güvenlik**: AWS güvenliği bir kerelik kurulum değildir. Güvenlik yapılandırmalarınızı düzenli olarak gözden geçirin ve güncelleyin, tehditleri izleyin ve yeni AWS güvenlik özellikleri hakkında bilgi sahibi olun.
{{< /callout >}}

Bu rehber, AWS güvenliğinin temel yönlerini kapsar. Gelişmiş güvenlik yapılandırmaları için şunları uygulamayı düşünün:

- **AWS WAF** web uygulaması koruması için
- **AWS Shield** DDoS koruması için
- **AWS GuardDuty** tehdit tespiti için
- **AWS Security Hub** merkezi güvenlik yönetimi için

---

*Profesyonel AWS güvenlik danışmanlığı ve uygulaması için, özel gereksinimlerinize göre uyarlanmış profesyonel rehberlik için DeepSec ile iletişime geçin.* 