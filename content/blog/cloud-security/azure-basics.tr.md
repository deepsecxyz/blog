---
title: "Azure Güvenlik Temelleri"
linkTitle: "Azure Güvenlik Temelleri"
slug: "azure-temelleri"
description: "Bulut altyapısı koruması için temel Azure güvenlik uygulamaları ve en iyi yöntemler"
translationKey: "azure-basics"
weight: 2
layout: docs
type: docs
url: "/tr/blog/bulut-guvenligi/azure-temelleri"
---

{{< callout type="warning" >}}
**Güvenlik Öncelik**: Azure güvenliği paylaşılan sorumluluk modelidir. Microsoft bulut altyapısını güvence altına alırken, siz bulut içindeki uygulamalarınızı ve verilerinizi güvence altına almakla sorumlusunuz.
{{< /callout >}}

Bu rehber, her bulut mimarı ve güvenlik uzmanının anlaması gereken temel Azure güvenlik temellerini kapsar. Azure Active Directory (Azure AD), ağ güvenliği, veri koruması ve izleme en iyi uygulamalarını inceleyeceğiz.

## Azure Active Directory (Azure AD)

Azure AD, Microsoft'un bulut tabanlı kimlik ve erişim yönetimi hizmetidir. Azure kaynaklarına ve uygulamalarına güvenli erişim için temeldir.

### Azure AD Temel Prensipleri

{{< tabs >}}
{{< tab title="Tek Oturum Açma (SSO)" >}}
Kullanıcılara tek bir kimlik bilgisi setiyle birden fazla uygulamaya sorunsuz erişim sağlamak için SSO'yu uygulayın. Bu, güvenliği korurken kullanıcı deneyimini iyileştirir.

**Faydalar**: Azaltılmış şifre yorgunluğu, geliştirilmiş güvenlik, merkezi erişim yönetimi.
{{< /tab >}}

{{< tab title="Çok Faktörlü Kimlik Doğrulama" >}}
Tüm kullanıcılar için, özellikle yönetici ayrıcalıklarına sahip olanlar için MFA'yı etkinleştirin. Azure AD, SMS, telefon aramaları ve kimlik doğrulayıcı uygulamalar dahil olmak üzere birden fazla MFA yöntemini destekler.

**En İyi Uygulama**: En güvenli MFA deneyimi için Microsoft Authenticator uygulamasını kullanın.
{{< /tab >}}

{{< tab title="Koşullu Erişim" >}}
Erişimi kullanıcı, cihaz, konum ve risk faktörlerine göre kontrol etmek için koşullu erişim politikalarını uygulayın. Bu, uyarlanabilir güvenlik kontrolleri sağlar.

**Örnek**: Güvenilmeyen ağlardan veya yeni cihazlardan erişirken MFA gerekli.
{{< /tab >}}
{{< /tabs >}}

### Azure AD Politikası Örneği

İşte yönetici hesapları için koşullu erişim politikası örneği:

```json
{
    "displayName": "Admin MFA Policy",
    "state": "enabled",
    "conditions": {
        "users": {
            "includeUsers": ["admin@company.com"],
            "excludeUsers": []
        },
        "applications": {
            "includeApplications": ["All"],
            "excludeApplications": []
        },
        "clientAppTypes": ["all"],
        "locations": {
            "includeLocations": ["All"],
            "excludeLocations": ["TrustedIPs"]
        }
    },
    "grantControls": {
        "operator": "AND",
        "builtInControls": ["mfa"],
        "customAuthenticationFactors": [],
        "termsOfUse": []
    },
    "sessionControls": {
        "applicationEnforcedRestrictions": null,
        "persistentBrowser": null,
        "cloudAppSecurity": null,
        "signInFrequency": null
    }
}
```

{{< callout type="info" >}}
**Koşullu Erişim En İyi Uygulamaları**: "Tümünü engelle" politikasıyla başlayın ve kademeli olarak istisnalar ekleyin. Politikaları her zaman önce yalnızca rapor modunda test edin.
{{< /callout >}}

## Ağ Güvenliği

Azure, bulut altyapınızı korumak için kapsamlı ağ güvenliği özellikleri sağlar.

### Sanal Ağ Mimarisi

{{< cards >}}
{{< card title="Sanal Ağlar" icon="cloud" content="Güvenli kaynak iletişimi için özel IP adres alanları, alt ağlar ve yönlendirme tabloları ile izole ağ ortamları oluşturun." >}}

{{< card title="Ağ Güvenlik Grupları" icon="eye" content="Güvenlik kuralları kullanarak Azure kaynaklarına ve kaynaklarından ağ trafiğini filtreleyin. Alt ağ ve ağ arayüzü seviyelerinde uygulayın." >}}

{{< card title="Azure Güvenlik Duvarı" icon="star" content="Azure Sanal Ağ kaynaklarınızı yerleşik yüksek kullanılabilirlik ile koruyan yönetilen, bulut yerel ağ güvenliği hizmeti." >}}
{{< /cards >}}

### Ağ Güvenlik Grubu Yapılandırması

NSG'ler Azure kaynaklarınız için sanal güvenlik duvarları görevi görür:

```bash
# Kaynak grubu oluştur
az group create --name mySecurityRG --location eastus

# Sanal ağ oluştur
az network vnet create \
    --resource-group mySecurityRG \
    --name myVNet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name default \
    --subnet-prefix 10.0.0.0/24

# Ağ güvenlik grubu oluştur
az network nsg create \
    --resource-group mySecurityRG \
    --name WebServerNSG

# HTTP trafiğine izin ver
az network nsg rule create \
    --resource-group mySecurityRG \
    --nsg-name WebServerNSG \
    --name AllowHTTP \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow

# HTTPS trafiğine izin ver
az network nsg rule create \
    --resource-group mySecurityRG \
    --nsg-name WebServerNSG \
    --name AllowHTTPS \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 443 \
    --access allow

# Belirli IP aralığından SSH'a izin ver
az network nsg rule create \
    --resource-group mySecurityRG \
    --nsg-name WebServerNSG \
    --name AllowSSH \
    --protocol tcp \
    --priority 1002 \
    --destination-port-range 22 \
    --source-address-prefix 10.0.0.0/16 \
    --access allow
```

{{< callout type="warning" >}}
**NSG En İyi Uygulamaları**: Her zaman en az ayrıcalık prensibini takip edin. SSH erişimi için 0.0.0.0/0 yerine belirli IP aralıkları kullanın.
{{< /callout >}}

### Azure Güvenlik Duvarı Yapılandırması

Gelişmiş ağ koruması için Azure Güvenlik Duvarı'nı kurun:

```bash
# Azure Güvenlik Duvarı oluştur
az network firewall create \
    --resource-group mySecurityRG \
    --name myAzureFirewall \
    --location eastus

# Güvenlik duvarı için genel IP oluştur
az network public-ip create \
    --resource-group mySecurityRG \
    --name fw-pip \
    --sku standard

# Güvenlik duvarı politikasını yapılandır
az network firewall policy create \
    --resource-group mySecurityRG \
    --name myFirewallPolicy

# Uygulama kural koleksiyonu ekle
az network firewall policy rule-collection-group create \
    --resource-group mySecurityRG \
    --policy-name myFirewallPolicy \
    --name myAppRuleCollection \
    --priority 1000 \
    --rule-collection-type FirewallPolicyFilterRuleCollection
```

## Veri Koruması ve Şifreleme

Azure, birden fazla şifreleme ve veri koruma katmanı sağlar.

### Azure Key Vault Entegrasyonu

Azure Key Vault, gizli diziler, anahtarlar ve sertifikaları yönetmek için gereklidir:

```bash
# Key Vault oluştur
az keyvault create \
    --name mySecureKeyVault \
    --resource-group mySecurityRG \
    --location eastus \
    --enabled-for-disk-encryption \
    --enabled-for-deployment \
    --enabled-for-template-deployment

# Yumuşak silme ve temizleme korumasını etkinleştir
az keyvault update \
    --name mySecureKeyVault \
    --resource-group mySecurityRG \
    --enable-soft-delete true \
    --enable-purge-protection true

# Gizli dizi oluştur
az keyvault secret set \
    --vault-name mySecureKeyVault \
    --name mySecret \
    --value "mySecretValue"

# Şifreleme anahtarı oluştur
az keyvault key create \
    --vault-name mySecureKeyVault \
    --name myEncryptionKey \
    --kty RSA \
    --size 2048
```

### Depolama Hesabı Şifreleme

Azure Depolama hesapları için şifrelemeyi etkinleştirin:

```bash
# Şifreleme ile depolama hesabı oluştur
az storage account create \
    --name mystorageaccount \
    --resource-group mySecurityRG \
    --location eastus \
    --sku Standard_LRS \
    --encryption-services blob file \
    --encryption-key-source Microsoft.Storage

# Müşteri tarafından yönetilen anahtarları etkinleştir
az storage account update \
    --name mystorageaccount \
    --resource-group mySecurityRG \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault mySecureKeyVault \
    --encryption-key-name myEncryptionKey \
    --encryption-key-version "key-version"
```

{{< callout type="info" >}}
**Şifreleme En İyi Uygulamaları**: Şifreleme üzerinde maksimum kontrol için müşteri tarafından yönetilen anahtarları kullanın. Anahtarları düzenli olarak döndürün ve anahtar kullanımını izleyin.
{{< /callout >}}

## İzleme ve Uyumluluk

Azure, kapsamlı izleme ve uyumluluk araçları sağlar.

### Azure Monitor Yapılandırması

Güvenlik olayları için izleme kurun:

```bash
# Log Analytics çalışma alanı oluştur
az monitor log-analytics workspace create \
    --resource-group mySecurityRG \
    --workspace-name mySecurityWorkspace

# Key Vault için tanılama ayarlarını etkinleştir
az monitor diagnostic-settings create \
    --resource /subscriptions/your-subscription-id/resourceGroups/mySecurityRG/providers/Microsoft.KeyVault/vaults/mySecureKeyVault \
    --workspace mySecurityWorkspace \
    --name KeyVaultDiagnostics \
    --logs '[{"category": "AuditEvent", "enabled": true}]'

# Başarısız kimlik doğrulama için uyarı kuralı oluştur
az monitor metrics alert create \
    --name "FailedAuthAlert" \
    --resource-group mySecurityRG \
    --scopes /subscriptions/your-subscription-id/resourceGroups/mySecurityRG/providers/Microsoft.KeyVault/vaults/mySecureKeyVault \
    --condition "count 'Authentication' > 5" \
    --description "Birden fazla başarısız kimlik doğrulama denemesinde uyarı"
```

### Azure Security Center

Gelişmiş tehdit koruması için Azure Security Center'ı etkinleştirin:

```bash
# Security Center'ı etkinleştir
az security pricing create \
    --name VirtualMachines \
    --tier standard

# İzleme aracıları için otomatik sağlamayı etkinleştir
az security auto-provisioning-settings update \
    --auto-provision on

# Güvenlik politikalarını yapılandır
az security policy create \
    --name "SecurityPolicy" \
    --resource-group mySecurityRG \
    --policy-file security-policy.json
```

{{< callout type="warning" >}}
**İzleme En İyi Uygulamaları**: Tüm abonelikler için Security Center'ı etkinleştirin, sürekli izlemeyi yapılandırın ve güvenlik tehditlerine otomatik yanıtlar kurun.
{{< /callout >}}

## Güvenlik Kontrol Listesi

{{< details title="Azure Güvenlik Uygulama Kontrol Listesi" >}}

### Kimlik ve Erişim Yönetimi
- [ ] Tüm kullanıcılar için Azure AD'yi etkinleştirin
- [ ] Çok Faktörlü Kimlik Doğrulama uygulayın
- [ ] Koşullu erişim politikalarını yapılandırın
- [ ] Azure AD Privileged Identity Management kullanın
- [ ] Düzenli erişim incelemeleri ve temizlik
- [ ] Hibrit ortamlar için Azure AD Connect'i etkinleştirin

### Ağ Güvenliği
- [ ] Uygun alt ağlarla Sanal Ağları yapılandırın
- [ ] Ağ Güvenlik Gruplarını uygulayın
- [ ] Gelişmiş koruma için Azure Güvenlik Duvarı'nı dağıtın
- [ ] DDoS Korumasını etkinleştirin
- [ ] Özel bağlantı için ExpressRoute'u yapılandırın
- [ ] İzleme için Network Watcher'ı uygulayın

### Veri Koruması
- [ ] Tüm Depolama hesapları için şifrelemeyi etkinleştirin
- [ ] Anahtar yönetimi için Azure Key Vault kullanın
- [ ] Azure Information Protection uygulayın
- [ ] Yedekleme ve olağanüstü durum kurtarma yapılandırın
- [ ] VM'ler için Azure Disk Encryption'i etkinleştirin
- [ ] Azure SQL Database şifreleme kullanın

### İzleme ve Uyumluluk
- [ ] Azure Security Center'ı etkinleştirin
- [ ] Azure Monitor ve Log Analytics'i yapılandırın
- [ ] SIEM için Azure Sentinel'i kurun
- [ ] Uyumluluk için Azure Policy'yi etkinleştirin
- [ ] Düzenli güvenlik değerlendirmeleri
- [ ] Güvenlik prosedürlerini belgeleyin

{{< /details >}}

## Sonraki Adımlar

{{< callout type="warning" >}}
**Sürekli Güvenlik**: Azure güvenliği bir kerelik kurulum değildir. Güvenlik yapılandırmalarınızı düzenli olarak gözden geçirin ve güncelleyin, tehditleri izleyin ve yeni Azure güvenlik özellikleri hakkında bilgi sahibi olun.
{{< /callout >}}

Bu rehber, Azure güvenliğinin temel yönlerini kapsar. Gelişmiş güvenlik yapılandırmaları için şunları uygulamayı düşünün:

- **Azure Sentinel** SIEM ve SOAR yetenekleri için
- **Azure Information Protection** veri sınıflandırması ve koruması için
- **Azure DDoS Protection** ağ seviyesi koruma için
- **Azure Bastion** güvenli RDP/SSH erişimi için
- **Azure Private Link** Azure hizmetlerine özel bağlantı için

---

*Profesyonel Azure güvenlik danışmanlığı ve uygulaması için, özel gereksinimlerinize göre uyarlanmış profesyonel rehberlik için DeepSec ile iletişime geçin.* 