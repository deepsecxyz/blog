---
title: "Azure-Sicherheitsgrundlagen"
linkTitle: "Azure-Sicherheitsgrundlagen"
slug: "azure-grundlagen"
description: "Wesentliche Azure-Sicherheitspraktiken und bewährte Verfahren für den Schutz der Cloud-Infrastruktur"
translationKey: "azure-basics"
weight: 2
layout: docs
type: docs
url: "/de/blog/cloud-sicherheit/azure-grundlagen"

---

{{< callout type="warning" >}}
**Sicherheit an erster Stelle**: Die Azure-Sicherheit basiert auf einem geteilten Verantwortungsmodell. Während Microsoft die Cloud-Infrastruktur sichert, sind Sie für die Sicherung Ihrer Anwendungen und Daten in der Cloud verantwortlich.
{{< /callout >}}

Dieser Leitfaden behandelt die wesentlichen Azure-Sicherheitsgrundlagen, die jeder Cloud-Architekt und Sicherheitsfachmann verstehen sollte. Wir untersuchen Azure Active Directory (Azure AD), Netzwerksicherheit, Datenschutz und bewährte Verfahren für die Überwachung.

## Azure Active Directory (Azure AD)

Azure AD ist Microsofts cloudbasierter Identitäts- und Zugriffsverwaltungsdienst. Es ist die Grundlage für die sichere Zugriffssteuerung auf Azure-Ressourcen und -Anwendungen.

### Kernprinzipien von Azure AD

{{< tabs >}}
{{< tab title="Single Sign-On (SSO)" >}}
Implementieren Sie SSO, um Benutzern nahtlosen Zugriff auf mehrere Anwendungen mit einem einzigen Satz von Anmeldedaten zu ermöglichen. Dies verbessert die Benutzererfahrung bei gleichzeitiger Aufrechterhaltung der Sicherheit.

**Vorteile**: Reduzierte Passwort-Müdigkeit, verbesserte Sicherheit, zentrale Zugriffsverwaltung.
{{< /tab >}}

{{< tab title="Multi-Faktor-Authentifizierung" >}}
Aktivieren Sie MFA für alle Benutzer, insbesondere für solche mit Administratorrechten. Azure AD unterstützt mehrere MFA-Methoden einschließlich SMS, Telefonanrufe und Authenticator-Apps.

**Bewährtes Verfahren**: Verwenden Sie die Microsoft Authenticator-App für die sicherste MFA-Erfahrung.
{{< /tab >}}

{{< tab title="Bedingter Zugriff" >}}
Implementieren Sie Richtlinien für bedingten Zugriff, um den Zugriff basierend auf Benutzer, Gerät, Standort und Risikofaktoren zu steuern. Dies bietet adaptive Sicherheitskontrollen.

**Beispiel**: MFA erforderlich beim Zugriff von nicht vertrauenswürdigen Netzwerken oder neuen Geräten.
{{< /tab >}}
{{< /tabs >}}

### Azure AD-Richtlinien-Beispiel

Hier ist ein Beispiel für eine Richtlinie für bedingten Zugriff für administrative Konten:

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
**Bewährte Verfahren für bedingten Zugriff**: Beginnen Sie mit einer "Alle blockieren"-Richtlinie und fügen Sie schrittweise Ausnahmen hinzu. Testen Sie Richtlinien immer zuerst im Nur-Bericht-Modus.
{{< /callout >}}

## Netzwerksicherheit

Azure bietet umfassende Netzwerksicherheitsfunktionen zum Schutz Ihrer Cloud-Infrastruktur.

### Virtual Network-Architektur

{{< cards >}}
{{< card title="Virtuelle Netzwerke" icon="cloud" content="Erstellen Sie isolierte Netzwerkumgebungen mit benutzerdefinierten IP-Adressräumen, Subnetzen und Routing-Tabellen für sichere Ressourcenkommunikation." >}}

{{< card title="Netzwerksicherheitsgruppen" icon="eye" content="Filtern Sie Netzwerkverkehr zu und von Azure-Ressourcen mithilfe von Sicherheitsregeln. Anwendung auf Subnetz- und Netzwerkschnittstellenebene." >}}

{{< card title="Azure Firewall" icon="star" content="Verwalteter, cloud-nativer Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen mit integrierter Hochverfügbarkeit schützt." >}}
{{< /cards >}}

### Netzwerksicherheitsgruppen-Konfiguration

NSGs fungieren als virtuelle Firewalls für Ihre Azure-Ressourcen:

```bash
# Ressourcengruppe erstellen
az group create --name mySecurityRG --location eastus

# Virtuelles Netzwerk erstellen
az network vnet create \
    --resource-group mySecurityRG \
    --name myVNet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name default \
    --subnet-prefix 10.0.0.0/24

# Netzwerksicherheitsgruppe erstellen
az network nsg create \
    --resource-group mySecurityRG \
    --name WebServerNSG

# HTTP-Datenverkehr erlauben
az network nsg rule create \
    --resource-group mySecurityRG \
    --nsg-name WebServerNSG \
    --name AllowHTTP \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow

# HTTPS-Datenverkehr erlauben
az network nsg rule create \
    --resource-group mySecurityRG \
    --nsg-name WebServerNSG \
    --name AllowHTTPS \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 443 \
    --access allow

# SSH von spezifischem IP-Bereich erlauben
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
**NSG-Best-Practices**: Befolgen Sie immer das Prinzip der minimalen Berechtigung. Verwenden Sie spezifische IP-Bereiche anstelle von 0.0.0.0/0 für SSH-Zugriff.
{{< /callout >}}

### Azure Firewall-Konfiguration

Richten Sie Azure Firewall für erweiterten Netzwerkschutz ein:

```bash
# Azure Firewall erstellen
az network firewall create \
    --resource-group mySecurityRG \
    --name myAzureFirewall \
    --location eastus

# Öffentliche IP für Firewall erstellen
az network public-ip create \
    --resource-group mySecurityRG \
    --name fw-pip \
    --sku standard

# Firewall-Richtlinie konfigurieren
az network firewall policy create \
    --resource-group mySecurityRG \
    --name myFirewallPolicy

# Anwendungsregel-Sammlung hinzufügen
az network firewall policy rule-collection-group create \
    --resource-group mySecurityRG \
    --policy-name myFirewallPolicy \
    --name myAppRuleCollection \
    --priority 1000 \
    --rule-collection-type FirewallPolicyFilterRuleCollection
```

## Datenschutz und Verschlüsselung

Azure bietet mehrere Verschlüsselungs- und Datenschutzebenen.

### Azure Key Vault-Integration

Azure Key Vault ist unerlässlich für die Verwaltung von Geheimnissen, Schlüsseln und Zertifikaten:

```bash
# Key Vault erstellen
az keyvault create \
    --name mySecureKeyVault \
    --resource-group mySecurityRG \
    --location eastus \
    --enabled-for-disk-encryption \
    --enabled-for-deployment \
    --enabled-for-template-deployment

# Soft Delete und Purge Protection aktivieren
az keyvault update \
    --name mySecureKeyVault \
    --resource-group mySecurityRG \
    --enable-soft-delete true \
    --enable-purge-protection true

# Geheimnis erstellen
az keyvault secret set \
    --vault-name mySecureKeyVault \
    --name mySecret \
    --value "mySecretValue"

# Verschlüsselungsschlüssel erstellen
az keyvault key create \
    --vault-name mySecureKeyVault \
    --name myEncryptionKey \
    --kty RSA \
    --size 2048
```

### Speicherkonto-Verschlüsselung

Aktivieren Sie Verschlüsselung für Azure-Speicherkonten:

```bash
# Speicherkonto mit Verschlüsselung erstellen
az storage account create \
    --name mystorageaccount \
    --resource-group mySecurityRG \
    --location eastus \
    --sku Standard_LRS \
    --encryption-services blob file \
    --encryption-key-source Microsoft.Storage

# Kundenverwaltete Schlüssel aktivieren
az storage account update \
    --name mystorageaccount \
    --resource-group mySecurityRG \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault mySecureKeyVault \
    --encryption-key-name myEncryptionKey \
    --encryption-key-version "key-version"
```

{{< callout type="info" >}}
**Verschlüsselungs-Best-Practices**: Verwenden Sie kundenverwaltete Schlüssel für maximale Kontrolle über die Verschlüsselung. Rotieren Sie Schlüssel regelmäßig und überwachen Sie die Schlüsselverwendung.
{{< /callout >}}

## Überwachung und Compliance

Azure bietet umfassende Überwachungs- und Compliance-Tools.

### Azure Monitor-Konfiguration

Richten Sie Überwachung für Sicherheitsereignisse ein:

```bash
# Log Analytics-Arbeitsbereich erstellen
az monitor log-analytics workspace create \
    --resource-group mySecurityRG \
    --workspace-name mySecurityWorkspace

# Diagnoseeinstellungen für Key Vault aktivieren
az monitor diagnostic-settings create \
    --resource /subscriptions/your-subscription-id/resourceGroups/mySecurityRG/providers/Microsoft.KeyVault/vaults/mySecureKeyVault \
    --workspace mySecurityWorkspace \
    --name KeyVaultDiagnostics \
    --logs '[{"category": "AuditEvent", "enabled": true}]'

# Warnungsregel für fehlgeschlagene Authentifizierung erstellen
az monitor metrics alert create \
    --name "FailedAuthAlert" \
    --resource-group mySecurityRG \
    --scopes /subscriptions/your-subscription-id/resourceGroups/mySecurityRG/providers/Microsoft.KeyVault/vaults/mySecureKeyVault \
    --condition "count 'Authentication' > 5" \
    --description "Warnung bei mehreren fehlgeschlagenen Authentifizierungsversuchen"
```

### Azure Security Center

Aktivieren Sie Azure Security Center für erweiterten Bedrohungsschutz:

```bash
# Security Center aktivieren
az security pricing create \
    --name VirtualMachines \
    --tier standard

# Auto-Provisioning für Überwachungs-Agents aktivieren
az security auto-provisioning-settings update \
    --auto-provision on

# Sicherheitsrichtlinien konfigurieren
az security policy create \
    --name "SecurityPolicy" \
    --resource-group mySecurityRG \
    --policy-file security-policy.json
```

{{< callout type="warning" >}}
**Überwachungs-Best-Practices**: Aktivieren Sie Security Center für alle Abonnements, konfigurieren Sie kontinuierliche Überwachung und richten Sie automatisierte Reaktionen auf Sicherheitsbedrohungen ein.
{{< /callout >}}

## Sicherheitscheckliste

{{< details title="Azure-Sicherheitsimplementierungscheckliste" >}}

### Identitäts- und Zugriffsverwaltung
- [ ] Azure AD für alle Benutzer aktivieren
- [ ] Multi-Faktor-Authentifizierung implementieren
- [ ] Richtlinien für bedingten Zugriff konfigurieren
- [ ] Azure AD Privileged Identity Management verwenden
- [ ] Regelmäßige Zugriffsüberprüfungen und Bereinigung
- [ ] Azure AD Connect für Hybrid-Umgebungen aktivieren

### Netzwerksicherheit
- [ ] Virtuelle Netzwerke mit ordnungsgemäßen Subnetzen konfigurieren
- [ ] Netzwerksicherheitsgruppen implementieren
- [ ] Azure Firewall für erweiterten Schutz bereitstellen
- [ ] DDoS-Schutz aktivieren
- [ ] ExpressRoute für private Konnektivität konfigurieren
- [ ] Network Watcher für Überwachung implementieren

### Datenschutz
- [ ] Verschlüsselung für alle Speicherkonten aktivieren
- [ ] Azure Key Vault für Schlüsselverwaltung verwenden
- [ ] Azure Information Protection implementieren
- [ ] Backup und Disaster Recovery konfigurieren
- [ ] Azure Disk Encryption für VMs aktivieren
- [ ] Azure SQL Database-Verschlüsselung verwenden

### Überwachung und Compliance
- [ ] Azure Security Center aktivieren
- [ ] Azure Monitor und Log Analytics konfigurieren
- [ ] Azure Sentinel für SIEM einrichten
- [ ] Azure Policy für Compliance aktivieren
- [ ] Regelmäßige Sicherheitsbewertungen
- [ ] Sicherheitsverfahren dokumentieren

{{< /details >}}

## Nächste Schritte

{{< callout type="warning" >}}
**Kontinuierliche Sicherheit**: Azure-Sicherheit ist keine einmalige Einrichtung. Überprüfen und aktualisieren Sie regelmäßig Ihre Sicherheitskonfigurationen, überwachen Sie Bedrohungen und bleiben Sie über neue Azure-Sicherheitsfunktionen informiert.
{{< /callout >}}

Dieser Leitfaden behandelt die grundlegenden Aspekte der Azure-Sicherheit. Für erweiterte Sicherheitskonfigurationen sollten Sie die Implementierung von:

- **Azure Sentinel** für SIEM- und SOAR-Funktionen
- **Azure Information Protection** für Datenklassifizierung und -schutz
- **Azure DDoS Protection** für netzwerkseitigen Schutz
- **Azure Bastion** für sicheren RDP/SSH-Zugriff
- **Azure Private Link** für private Konnektivität zu Azure-Diensten

in Betracht ziehen.

---

*Für professionelle Azure-Sicherheitsberatung und -implementierung kontaktieren Sie DeepSec für maßgeschneiderte Beratung, die auf Ihre spezifischen Anforderungen zugeschnitten ist.* 