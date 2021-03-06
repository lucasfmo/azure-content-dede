<properties
   pageTitle="Konfigurieren der Authentifizierung mit Amazon Web Services | Microsoft Azure"
   description="In diesem Artikel wird beschrieben, wie Sie AWS-Anmeldeinformationen für Runbooks in Azure Automation zum Verwalten von AWS-Ressourcen erstellen und überprüfen."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="AWS-Authentifizierung, AWS konfigurieren"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/10/2016"
   ms.author="magoedte"/>

# Authentifizieren von Runbooks mit Amazon Web Services
Die Automatisierung von häufigen Aufgaben mit Ressourcen in Amazon Web Services (AWS) kann mit Automation-Runbooks in Azure erreicht werden. Sie können viele Aufgaben in AWS mit Automation-Runbooks automatisieren, wie dies auch mit Ressourcen in Azure möglich ist. Sie benötigen lediglich zwei Dinge:

* Ein AWS-Abonnement und einen Satz von Anmeldeinformationen. Genauer gesagt: Ihren AWS-Zugriffsschlüssel und den geheimen Schlüssel. Weitere Informationen finden Sie im Artikel [Using AWS Credentials](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) (Verwenden von AWS-Anmeldeinformationen).
* Ein Azure-Abonnement und ein Automation-Konto. Weitere Informationen zum Einrichten eines Azure Automation-Kontos finden Sie im Artikel [Konfigurieren eines ausführenden Kontos in Azure](../automation/automation-sec-configure-azure-runas-account.md).  

Zum Authentifizieren mit AWS müssen Sie einen Satz mit AWS-Anmeldeinformationen angeben, um Ihre über Azure Automation ausgeführten Anmeldeinformationen zu authentifizieren. Falls Sie bereits ein Automation-Konto erstellt haben und für die Authentifizierung mit AWS verwenden möchten, können Sie die Schritte im folgenden Abschnitt ausführen. Wenn Sie ein festes Konto für Runbooks zur Verwendung mit AWS-Ressourcen bestimmen möchten, sollten Sie zuerst ein neues [ausführendes Konto für Automation](../automation/automation-sec-configure-azure-runas-account) erstellen (überspringen Sie die Option zum Erstellen eines Dienstprinzipals) und dann die unten angegebenen Schritte ausführen.

## Konfigurieren des Automation-Kontos
Damit Azure Automation mit AWS kommunizieren kann, müssen Sie zuerst Ihre AWS-Anmeldeinformationen abrufen und als Ressourcen in Azure Automation speichern. Führen Sie die folgenden Schritte aus, die im AWS-Dokument [Managing Access Keys for your AWS Account](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) (Verwalten von Zugriffsschlüsseln für Ihr AWS-Konto) enthalten sind, um einen Zugriffsschlüssel zu erstellen und die **Zugriffsschlüssel-ID** und den **geheimen Zugriffsschlüssel** zu kopieren (optional können Sie auch die Schlüsseldatei herunterladen und an einem sicheren Ort speichern).

Nachdem Sie Ihre AWS-Sicherheitsschlüssel erstellt und kopiert haben, müssen Sie ein Anmeldeinformationsobjekt mit einem Azure Automation-Konto erstellen, um sie sicher zu speichern und in den Runbooks darauf verweisen zu können. Führen Sie die Schritte im Abschnitt **So erstellen Sie neue Anmeldeinformationen** des Artikels [Anmeldeinformationsobjekte in Azure Automation](../automation/automation-certificates.md/###So erstellen Sie neue Anmeldeinformationen mit dem Azure-Portal) aus, und geben Sie die folgenden Informationen ein:

1. Geben Sie im Feld **Name** den Wert **AWScred** oder einen anderen geeigneten Wert gemäß Ihren Benennungsvorgaben ein.  
2. Geben Sie im Feld **Benutzername** Ihre **Zugriffs-ID** und den **geheimen Zugriffsschlüssel** im Feld **Kennwort** bzw. **Kennwort bestätigen** ein.   

## Nächste Schritte

- Sehen Sie sich den Artikel [Automatisieren der Bereitstellung einer VM in Amazon Web Services](../automation/automation-scenario-aws-deployment.md) an, um zu erfahren, wie Sie Runbooks zum Automatisieren von Aufgaben in AWS erstellen.

<!---HONumber=AcomDC_0511_2016-->