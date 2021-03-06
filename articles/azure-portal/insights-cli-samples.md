<properties
	pageTitle="Azure Insights: Schnellstartbeispiele für die Azure Insights-Befehlszeilenschnittstelle | Microsoft Azure"
	description="Beispielbefehle für die Befehlszeilenschnittstelle können Ihnen als Hilfe beim Zugreifen auf Azure Insights-Überwachungsfeatures dienen. Azure Insights ist ein Microsoft Azure-Dienst, der die automatische Skalierung von Clouddiensten, virtuellen Computern und Web-Apps, das Senden von Warnbenachrichtigungen oder das Aufrufen von Web-URLs basierend auf Werten konfigurierter Telemetriedaten ermöglicht."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Azure Insights – Schnellstartbeispiele für plattformübergreifende Befehlszeilenschnittstelle

In diesem Artikel werden Beispielbefehle für die Befehlszeilenschnittstelle (Command Line Interface, CLI) beschrieben, mit denen Sie auf Azure Insights-Überwachungsfeatures zugreifen können. Azure Insights ermöglicht die automatische Skalierung von Clouddiensten, virtuellen Computern und Web-Apps sowie das Senden von Warnbenachrichtigungen oder das Aufrufen von Web-URLs basierend auf Werten konfigurierter Telemetriedaten.

>[AZURE.NOTE] Einige der neuesten Fehlerbehebungen für die Azure Insights-Befehlszeilenschnittstelle sind noch nicht abgeschlossen. Aus diesem Grund stehen einige der neuesten Funktionen noch nicht in der aktuellen Version der Azure-Befehlszeilenschnittstelle zur Verfügung. In der Zwischenzeit können Sie die neueste Azure Insights-Befehlszeilenschnittstelle verwenden, die die Fehlerbehebungen enthält. Dazu laden Sie unter [Azure XPLAT Tarball](http://github.com/Azure/azure-xplat-cli/archive/dev.tar.gz) den neuesten Tarball aus der Entwicklungsabteilung herunter. Befolgen Sie anschließend die Anweisungen im [ plattformübergreifenden Befehlszeilen-Repository für Microsoft Azure](https://github.com/Azure/azure-xplat-cli#installation-from-a-particular-branch-of-this-repository), um die Befehlszeilenschnittstelle mithilfe des heruntergeladenen Tarballs zu installieren. Sobald die Azure-Befehlszeilenschnittstelle aktualisiert wird, werden die Fehlerbehebungen zu Azure Insights hinzugefügt. Wir werden dieses Dokument aktualisieren, sobald die Änderungen in der Hauptzweigstelle der Azure-Befehlszeilenschnittstelle zur Verfügung stehen.

## Voraussetzungen

Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht installiert haben, finden Sie alle erforderlichen Informationen unter [Installieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md). Falls Sie mit der Azure-Befehlszeilenschnittstelle noch nicht vertraut sind, helfen Ihnen die Informationen unter [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Azure-Ressourcen-Manager](../xplat-cli-azure-resource-manager.md) weiter.


Installieren Sie npm unter Windows über die [Node.js-Website](https://nodejs.org/). Führen Sie nach Abschluss der Installation Folgendes in dem Ordner aus, in dem npm installiert ist, indem Sie „CMD.exe“ mit der Berechtigung „Als Administrator ausführen“ verwenden:

```
npm install azure-cli --global
```

Navigieren Sie als Nächstes zum gewünschten Ordner bzw. Speicherort, und geben Sie an der Befehlszeile Folgendes ein:

```
azure help
```

## Anmelden an Azure

Der erste Schritt besteht darin, sich am Azure-Konto anzumelden.

```
azure login
```

Hierfür müssen Sie die Anmeldung durchführen. Anschließend werden Ihr Konto, die Mandanten-ID und die standardmäßige Abonnement-ID angezeigt. Alle Befehle funktionieren im Kontext Ihres Standardabonnements.

Verwenden Sie den folgenden Befehl, um die Details Ihres aktuellen Abonnements aufzulisten.

```
azure account show
```

Verwenden Sie den folgenden Befehl, um den Arbeitskontext in ein anderes Abonnement zu ändern.

```
azure account set "subscription ID or subscription name"
```

Zum Verwenden von Azure Resource Manager- und Azure Insights-Befehlen müssen Sie sich im ARM-Modus befinden.

```
azure config mode arm
```

Führen Sie Folgendes aus, um eine Liste mit allen unterstützten Azure Insights-Befehlen anzuzeigen:

```
azure insights
```

## Anzeigen von Überwachungsprotokollen für ein Abonnement

Führen Sie Folgendes aus, um eine Liste mit Überwachungsprotokollen anzuzeigen:

```
azure insights logs list [options]
```

Probieren Sie Folgendes aus, um alle verfügbaren Optionen anzuzeigen:

```
azure insights logs list -help
```

Beispiel für das Auflisten von Protokollen nach resourceGroup

```
azure insights logs list --resourceGroup "myrg1"
```

Beispiel für das Auflisten von Protokollen nach Aufrufer

```
azure insights logs list --caller "myname@company.com"
```

Beispiel für das Auflisten von Protokollen nach Aufrufer für einen Ressourcentyp innerhalb eines Zeitraums mit Start- und Enddatum

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## Verwenden von Warnungen
Sie können die Informationen in diesem Abschnitt nutzen, um Warnungen zu verwenden.

### Abrufen von Warnungsregeln in einer Ressourcengruppe

```
node bin\azure insights alerts rule list abhingrgtest123
node bin\azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### Erstellen einer metrischen Warnungsregel

```
node bin\azure insights alerts actions email create --customEmails foo@microsoft.com
node bin\azure insights alerts actions webhook create https://someuri.com
node bin\azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### Erstellen einer Warnungsregel für ein Protokoll

```
insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### Erstellen einer Webtest-Warnungsregel

```
node bin\azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### Löschen einer Warnungsregel

```
node bin\azure insights alerts rule delete abhingrgtest123 andy0323
```

## Protokollprofile
Verwenden Sie die Informationen in diesem Abschnitt, um Protokollprofile zu verwenden.

### Abrufen eines Protokollprofils

```
node bin\azure insights logprofile list
node bin\azure insights logprofile get -n default
```


### Hinzufügen eines Protokollprofils ohne Aufbewahrung

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Entfernen eines Protokollprofils

```
node bin\azure insights logprofile delete --name default
```

### Hinzufügen eines Protokollprofils mit Aufbewahrung

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### Hinzufügen eines Protokollprofils mit Aufbewahrung und Event Hub

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## Diagnose
Verwenden Sie die Informationen in diesem Abschnitt, um Diagnoseeinstellungen zu verwenden.

### Abrufen einer Diagnoseeinstellung

```
node bin\azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### Deaktivieren einer Diagnoseeinstellung

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### Aktivieren einer Diagnoseeinstellung ohne Aufbewahrung

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
Nutzen Sie die Informationen in diesem Abschnitt, um Einstellungen für die automatische Skalierung zu verwenden. Sie müssen diese Beispiele ändern.

### Abrufen der Einstellungen für die automatische Skalierung für eine Ressourcengruppe

```
node bin\azure insights autoscale setting list montest2
```

### Abrufen der Einstellungen für die automatische Skalierung in einer Ressourcengruppe

```
node bin\azure insights autoscale setting list montest2 -n setting2
```


### Festlegen der Einstellungen für die automatische Skalierung

```
node bin\azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!---HONumber=AcomDC_0608_2016-->