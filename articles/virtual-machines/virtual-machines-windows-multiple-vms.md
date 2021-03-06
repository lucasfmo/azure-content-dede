<properties
	pageTitle="Erstellen mehrerer virtueller Computer | Microsoft Azure"
	description="Optionen für das Erstellen mehrerer virtueller Computer unter Windows"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="guybo"/>

# Erstellen mehrerer virtueller Azure-Computer

In vielen Szenarios müssen Sie eine große Anzahl ähnlicher virtueller Computer erstellen. Beispiele hierfür sind High Performance Computing (HPC), umfangreiche Datenanalysen, skalierbare und häufig zustandslose Server auf mittlerer Ebene oder Back-End-Server (z.B. Webserver) und verteilte Datenbanken.

In diesem Artikel werden die verfügbaren Optionen zum Erstellen mehrerer virtueller Computer in Azure beschrieben. Diese Optionen gehen über die einfachen Fälle hinaus, bei denen Sie mehrere virtuelle Computer manuell erstellen. Wenn Sie eine größere Anzahl virtueller Computer erstellen müssen, lassen sich die normalerweise durchgeführten Vorgänge nicht so gut skalieren, wie dies bei der Erstellung einer Handvoll virtueller Computer der Fall ist.

Eine Möglichkeit zum Erstellen vieler ähnlicher virtueller Computer besteht in der Verwendung des Azure Resource Manager-Konstrukts der _Ressourcenschleifen_.

## Ressourcenschleifen

Ressourcenschleifen sind eine syntaktische Kurzform in Azure Resource Manager-Vorlagen. Mit Ressourcenschleifen lässt sich eine Gruppe ähnlich konfigurierter Ressourcen in einer Schleife erstellen. Sie können mit Ressourcenschleifen mehrere Speicherkonten, Netzwerkschnittstellen oder virtuelle Computer erstellen. Weitere Informationen zu Ressourcenschleifen finden Sie unter [Create VMs in Availability Sets using Resource Loops](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/) (Erstellen von virtuellen Computern in Verfügbarkeitsgruppen mithilfe von Ressourcenschleifen).

## Herausforderungen bei der Skalierung

Obwohl Ressourcenschleifen die Erstellung einer umfangreichen Cloudinfrastruktur einfacher gestalten und präzisere Vorlagen liefern, bleiben bestimmte Probleme bestehen. Beim Erstellen von 100 virtuellen Computern mit einer Ressourcenschleife müssen Sie z.B. die Netzwerkschnittstellenkarten (NICs) mit den entsprechenden virtuellen Computern und Speicherkonten korrelieren. Da sich die Anzahl der virtuellen Computer wahrscheinlich von der Anzahl der Speicherkonten unterscheidet, unterscheiden sich auch die Größen der Ressourcenschleifen. Dies sind lösbare Probleme, aber die Komplexität nimmt mit zunehmender Größe erheblich zu.

Eine weitere Herausforderung entsteht, wenn Sie eine Infrastruktur mit elastischer Skalierung benötigen, beispielsweise eine Infrastruktur mit automatischer Skalierung, bei der die Anzahl der virtuellen Computer als Reaktion auf die Workload automatisch erhöht oder verringert wird. Virtuelle Computer bieten keinen integrierten Mechanismus zum Variieren der Anzahl (horizontales Hoch- und Herunterskalieren). Wenn Sie durch das Entfernen von virtuellen Computern horizontal herunterskalieren, ist die Sicherstellung einer hohen Verfügbarkeit durch die gleichmäßige Verteilung der virtuellen Computer über Update- und Fehlerdomänen nur schwer zu gewährleisten.

Bei Verwendung einer Ressourcenschleife gehen schließlich mehrere Aufrufe zum Erstellen von Ressourcen an das zugrunde liegende Fabric. Wenn mit mehreren Aufrufen ähnliche Ressourcen erstellt werden, besteht in Azure die implizite Möglichkeit, diesen Entwurf zu verbessern und die Zuverlässigkeit und Leistung bei der Bereitstellung zu optimieren. Hier werden _VM-Skalierungsgruppen_ relevant.

## VM-Skalierungsgruppen

VM-Skalierungsgruppen sind eine Azure Cloud Services-Ressource, mit der Sie eine Gruppe von identischen virtuellen Computern bereitstellen und verwalten können. Wenn alle virtuellen Computer identisch konfiguriert sind, lassen sich VM-Skalierungsgruppen einfach horizontal herunter- und hochskalieren. Dazu ändern Sie einfach die Anzahl der virtuellen Computer in der Gruppe. Zudem können Sie VM-Skalierungsgruppen basierend auf den Anforderungen der Workload für die automatische Skalierung konfigurieren.

Bei Anwendungen, für die Computeressourcen horizontal herunter- und hochskaliert werden müssen, werden Skalierungsoperationen implizit über Fehler- und Updatedomänen ausgeglichen.

Statt der Korrelation mehrerer Ressourcen wie z.B. Netzwerkschnittstellenkarten und virtueller Computer verfügt eine VM-Skalierungsgruppe über Eigenschaften für Netzwerk, Speicher, virtuelle Computer und Erweiterungen, die zentral konfiguriert werden können.

Eine Einführung in VM-Skalierungsgruppen finden Sie auf der [Produktseite für Skalierungsgruppen für virtuelle Computer](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Ausführlichere Informationen finden Sie in der [Dokumentation zu Skalierungsgruppen für virtuelle Computer](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0518_2016-->