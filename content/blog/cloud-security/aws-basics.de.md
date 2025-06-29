---
title: "AWS-Sicherheitsgrundlagen"
linkTitle: "AWS-Sicherheitsgrundlagen"
slug: "aws-grundlagen"
description: "Wesentliche AWS-Sicherheitspraktiken und bewährte Verfahren für den Schutz der Cloud-Infrastruktur"
translationKey: "aws-basics"
weight: 1
layout: docs
type: docs
url: "/de/blog/cloud-sicherheit/aws-grundlagen"
---

{{< callout type="warning" >}}
**Sicherheit an erster Stelle**: Die AWS-Sicherheit basiert auf einem geteilten Verantwortungsmodell. Während AWS die Cloud-Infrastruktur sichert, sind Sie für die Sicherung Ihrer Anwendungen und Daten in der Cloud verantwortlich.
{{< /callout >}}

Dieser Leitfaden behandelt die wesentlichen AWS-Sicherheitsgrundlagen, die jeder Cloud-Architekt und Sicherheitsfachmann verstehen sollte. Wir untersuchen Identity and Access Management (IAM), Virtual Private Cloud (VPC)-Sicherheit, Verschlüsselungsstrategien und bewährte Verfahren für die Überwachung.

## Identity and Access Management (IAM)

IAM ist die Grundlage der AWS-Sicherheit. Es steuert, wer auf Ihre AWS-Ressourcen zugreifen kann und was er damit machen darf.

### Kernprinzipien von IAM

{{< tabs >}}
{{< tab title="Prinzip der minimalen Berechtigung" >}}
Gewähren Sie Benutzern und Anwendungen nur die minimalen Berechtigungen, die für ihre Aufgaben erforderlich sind. Dies reduziert die Angriffsfläche und begrenzt potenzielle Schäden durch kompromittierte Anmeldedaten.

**Beispiel**: Anstatt einem Entwickler vollen Administratorzugriff zu gewähren, erteilen Sie nur spezifische Berechtigungen für die Ressourcen seines Projekts.
{{< /tab >}}

{{< tab title="Rollenbasierter Zugriff" >}}
Verwenden Sie IAM-Rollen anstelle von langfristigen Zugriffsschlüsseln, wann immer möglich. Rollen stellen temporäre Anmeldedaten bereit und sind sicherer als hartcodierte Zugriffsschlüssel.

**Bewährtes Verfahren**: Verwenden Sie AWS STS (Security Token Service) für temporäre Anmeldedaten.
{{< /tab >}}

{{< tab title="Multi-Faktor-Authentifizierung" >}}
Aktivieren Sie MFA für alle IAM-Benutzer, insbesondere für solche mit Administratorrechten. MFA fügt eine zusätzliche Sicherheitsebene über Passwörter hinaus hinzu.
{{< /tab >}}
{{< /tabs >}}

### IAM-Richtlinien-Beispiel

Hier ist ein Beispiel für eine gut strukturierte IAM-Richtlinie für ein Entwicklungsteam:

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
**Richtlinien-Best-Practices**: Verwenden Sie immer explizite Verweigerungsanweisungen für sensible Operationen und implementieren Sie ressourcenbasierte Berechtigungen, wo möglich.
{{< /callout >}}

## Virtual Private Cloud (VPC)-Sicherheit

VPC bietet Netzwerkisolation und Kontrolle über Ihre AWS-Ressourcen. Eine ordnungsgemäße VPC-Konfiguration ist entscheidend für die Sicherheit.

### Netzwerkarchitektur

{{< cards >}}
{{< card title="Öffentliche Subnetze" icon="cloud" content="Hosting von internetorientierten Ressourcen wie Load Balancer und Bastion-Hosts. Verwenden Sie Network ACLs und Security Groups zum Schutz." >}}

{{< card title="Private Subnetze" icon="eye" content="Hosting von Anwendungsservern und Datenbanken. Kein direkter Internetzugang - Datenverkehr wird über NAT Gateway geleitet." >}}

{{< card title="Datenbank-Subnetze" icon="star" content="Isolierte Subnetze für RDS und andere Datenbanken. Zusätzliche Sicherheitsebenen und Verschlüsselung erforderlich." >}}
{{< /cards >}}

### Security Group-Konfiguration

Security Groups fungieren als virtuelle Firewalls für Ihre EC2-Instanzen:

```bash
# Security Group für Webserver erstellen
aws ec2 create-security-group \
    --group-name WebServerSG \
    --description "Security Group für Webserver" \
    --vpc-id vpc-12345678

# HTTP-Datenverkehr erlauben
aws ec2 authorize-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0

# HTTPS-Datenverkehr erlauben
aws ec2 authorize-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 443 \
    --cidr 0.0.0.0/0

# SSH von spezifischem IP-Bereich erlauben
aws ec2 authorize-security-group-ingress \
    --group-id sg-12345678 \
    --protocol tcp \
    --port 22 \
    --cidr 10.0.0.0/16
```

{{< callout type="warning" >}}
**Security Group-Regeln**: Befolgen Sie immer das Prinzip der minimalen Berechtigung. Verwenden Sie niemals 0.0.0.0/0 für SSH-Zugriff, es sei denn, es ist absolut notwendig.
{{< /callout >}}

## Verschlüsselungsstrategien

AWS bietet mehrere Verschlüsselungsebenen zum Schutz Ihrer Daten im Ruhezustand und bei der Übertragung.

### Verschlüsselung ruhender Daten

{{< tabs >}}
{{< tab title="S3-Verschlüsselung" >}}
Aktivieren Sie serverseitige Verschlüsselung für alle S3-Buckets:

```bash
# Bucket mit Standardverschlüsselung erstellen
aws s3api create-bucket \
    --bucket mein-sicherer-bucket \
    --region us-east-1

# Standardverschlüsselung aktivieren
aws s3api put-bucket-encryption \
    --bucket mein-sicherer-bucket \
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

{{< tab title="RDS-Verschlüsselung" >}}
Aktivieren Sie Verschlüsselung für RDS-Instanzen:

```bash
# Verschlüsselte RDS-Instanz erstellen
aws rds create-db-instance \
    --db-instance-identifier meine-datenbank \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username admin \
    --master-user-password meinpasswort \
    --storage-encrypted \
    --kms-key-id arn:aws:kms:us-east-1:123456789012:key/abcd1234-ef56-7890-abcd-ef1234567890
```
{{< /tab >}}

{{< tab title="EBS-Verschlüsselung" >}}
Aktivieren Sie Verschlüsselung für EBS-Volumes:

```bash
# Verschlüsseltes EBS-Volume erstellen
aws ec2 create-volume \
    --size 100 \
    --availability-zone us-east-1a \
    --encrypted \
    --kms-key-id arn:aws:kms:us-east-1:123456789012:key/abcd1234-ef56-7890-abcd-ef1234567890
```
{{< /tab >}}
{{< /tabs >}}

### Verschlüsselung übertragener Daten

Verwenden Sie immer TLS/SSL für die Datenübertragung:

```bash
# HTTPS-Listener für ALB konfigurieren
aws elbv2 create-listener \
    --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/mein-alb/1234567890abcdef \
    --protocol HTTPS \
    --port 443 \
    --certificates CertificateArn=arn:aws:acm:us-east-1:123456789012:certificate/abcd1234-ef56-7890-abcd-ef1234567890 \
    --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/mein-tg/1234567890abcdef
```

## Überwachung und Protokollierung

Umfassende Überwachung und Protokollierung sind für Sicherheit und Compliance unerlässlich.

### CloudTrail-Konfiguration

Aktivieren Sie CloudTrail für API-Aktivitätsprotokollierung:

```bash
# CloudTrail erstellen
aws cloudtrail create-trail \
    --name mein-sicherheits-trail \
    --s3-bucket-name mein-cloudtrail-bucket \
    --include-global-service-events \
    --is-multi-region-trail

# Protokollierung starten
aws cloudtrail start-logging \
    --name mein-sicherheits-trail
```

### CloudWatch-Alarme

Richten Sie sicherheitsorientierte CloudWatch-Alarme ein:

```bash
# Alarm für fehlgeschlagene Anmeldeversuche erstellen
aws cloudwatch put-metric-alarm \
    --alarm-name "FehlgeschlageneAnmeldeversuche" \
    --alarm-description "Warnung bei mehreren fehlgeschlagenen Anmeldeversuchen" \
    --metric-name FailedLoginAttempts \
    --namespace AWS/IAM \
    --statistic Sum \
    --period 300 \
    --threshold 5 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 1
```

{{< callout type="info" >}}
**Überwachungs-Best-Practices**: Richten Sie Warnungen für ungewöhnliche Aktivitäten, fehlgeschlagene Authentifizierungsversuche und unbefugte API-Aufrufe ein.
{{< /callout >}}

## Sicherheitscheckliste

{{< details title="AWS-Sicherheitsimplementierungscheckliste" >}}

### Identity and Access Management
- [ ] MFA für alle IAM-Benutzer aktivieren
- [ ] IAM-Rollen anstelle von Zugriffsschlüsseln verwenden
- [ ] Richtlinien mit minimalen Berechtigungen implementieren
- [ ] Regelmäßige Zugriffsüberprüfungen und Bereinigung
- [ ] CloudTrail-Protokollierung aktivieren

### Netzwerksicherheit
- [ ] VPC mit privaten Subnetzen konfigurieren
- [ ] Security Groups mit minimalen Regeln implementieren
- [ ] Network ACLs für zusätzlichen Schutz verwenden
- [ ] VPC Flow Logs aktivieren
- [ ] NAT Gateway für private Instanzen konfigurieren

### Datenschutz
- [ ] Verschlüsselung für alle S3-Buckets aktivieren
- [ ] RDS-Instanzen verschlüsseln
- [ ] Verschlüsselte EBS-Volumes verwenden
- [ ] TLS/SSL für alle Verbindungen implementieren
- [ ] AWS KMS für Schlüsselverwaltung verwenden

### Überwachung und Compliance
- [ ] CloudWatch-Alarme einrichten
- [ ] CloudTrail für alle Regionen konfigurieren
- [ ] AWS Config für Compliance-Überwachung aktivieren
- [ ] Regelmäßige Sicherheitsbewertungen
- [ ] Sicherheitsverfahren dokumentieren

{{< /details >}}

## Nächste Schritte

{{< callout type="warning" >}}
**Kontinuierliche Sicherheit**: AWS-Sicherheit ist keine einmalige Einrichtung. Überprüfen und aktualisieren Sie regelmäßig Ihre Sicherheitskonfigurationen, überwachen Sie Bedrohungen und bleiben Sie über neue AWS-Sicherheitsfunktionen informiert.
{{< /callout >}}

Dieser Leitfaden behandelt die grundlegenden Aspekte der AWS-Sicherheit. Für erweiterte Sicherheitskonfigurationen sollten Sie die Implementierung von:

- **AWS WAF** für Webanwendungsschutz
- **AWS Shield** für DDoS-Schutz
- **AWS GuardDuty** für Bedrohungserkennung
- **AWS Security Hub** für zentrale Sicherheitsverwaltung

in Betracht ziehen.

---

*Für professionelle AWS-Sicherheitsberatung und -implementierung kontaktieren Sie DeepSec für maßgeschneiderte Beratung, die auf Ihre spezifischen Anforderungen zugeschnitten ist.* 