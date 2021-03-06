<properties
	pageTitle="Arbeiten mit Azure AD-Anwendungsproxyconnectors | Microsoft Azure"
	description="Erläutert das Erstellen und Verwalten von Connectorgruppen im Azure AD-Anwendungsproxy."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="kgremban"/>


# Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Speicherorten mit Connectorgruppen

> [AZURE.NOTE] Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Connectorgruppen sind unter anderem in folgenden Anwendungsbereichen nützlich:


- Websites mit mehreren verbundenen Datencentern. In diesem Fall soll jeweils möglichst viel Datenverkehr innerhalb des Datencenters verlaufen, da Verbindungen zwischen Datencentern gewöhnlich teuer und langsam sind. In jedem Datencenter lassen sich Connectors bereitstellen, die jeweils nur die lokalen Anwendungen bedienen. Mit diesem Vorgehen werden bei völliger Transparenz für den Benutzer die Verbindungen zwischen den Datencentern minimiert.
- Verwalten von Anwendungen, die in isolierten Netzwerken installiert, jedoch nicht Teil des Hauptnetzwerks des Unternehmens sind. Connectorgruppen können verwendet werden, um dedizierte Connectors für isolierte Netzwerke zu installieren und außerdem die Anwendungen vom Netzwerk zu isolieren.
- Für Anwendungen, die auf IaaS für den Cloudzugriff installiert sind, bieten Connectorgruppen einen übergreifenden Dienst, mit dem der Zugriff auf alle Apps gesichert wird, ohne eine zusätzliche Abhängigkeit vom Unternehmensnetzwerk zu schaffen oder die Einheitlichkeit der Benutzerführung aufzugeben. Connectors können in allen Cloudrechenzentren installiert werden und bedienen nur Anwendungen, die sich in diesem Netzwerk befinden. Es können mehrere Connectors installiert werden, um eine hohe Verfügbarkeit zu gewährleisten.
- Unterstützung für Umgebungen mit mehreren Gesamtstrukturen, in denen bestimmte Connectors in einzelnen Gesamtstrukturen bereitgestellt werden können, um dort bestimmte Anwendungen zu bedienen.
- Connectorgruppen können bei Websites als Teil von Notfallkonzepten eingesetzt werden, um ein Failover zu erkennen oder als Datensicherung für die Hauptwebsite zu dienen.
- Connectorgruppen können auch verwendet werden, um mehrere Unternehmen von einem einzigen Mandanten aus zu bedienen.

## Voraussetzung: Erstellen der Connectors
Um Connectors zu gruppieren, müssen Sie sicherstellen, dass [mehrere Connectors installiert sind](active-directory-application-proxy-enable.md), und diese dann entsprechend benennen und gruppieren. Im letzten Schritt müssen die Connectors dann bestimmten Apps zugewiesen werden.

## Schritt 1: Erstellen von Connectorgruppen
Sie können beliebig viele Connectorgruppen erstellen. Das Erstellen von Connectorgruppen erfolgt im klassischen Azure-Portal.

1. Wählen Sie Ihr Verzeichnis aus, und klicken Sie auf **Konfigurieren**. ![Screenshot zum Konfigurieren des Anwendungsproxys – auf „Connectorgruppen verwalten“ klicken](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. Klicken Sie dann unter „Anwendungsproxy“ auf **Connectorgruppen verwalten**, und erstellen Sie eine neue Connectorgruppe, indem Sie die Gruppe benennen. ![Screenshot zu Connectorgruppen für Anwendungsproxys – neue Gruppe benennen](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## Schritt 2: Zuordnen von Connectors zu Ihren Gruppen
Nachdem die Connectorgruppen erstellt wurden, werden die Connectors in die entsprechende Gruppe verschoben.

1. Klicken Sie unter **Anwendungsproxy** auf **Connectors verwalten**.
2. Wählen Sie unter **Gruppe** die Gruppe für jeden Connector aus. Beachten Sie, dass es bis zu 10 Minuten dauern kann, bis die Connectors in der neuen Gruppe aktiv werden. ![Screenshot zu Anwendungsproxyconnectors – Gruppe aus dem Dropdown-Menü auswählen](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## Schritt 3: Zuweisen von Anwendungen zu Ihren Connectorgruppen
Der letzte Schritt besteht darin, jede Anwendung der Connectorgruppe zuzuweisen, von der sie bedient wird.

1. Wählen Sie im klassischen Azure-Portal in Ihrem Verzeichnis die Anwendung aus, die Sie der Gruppe zuweisen möchten, und klicken Sie auf **Konfigurieren**.
2. Wählen Sie unter **Connectorgruppe** die Gruppe aus, die die Anwendung verwenden soll. Diese Änderung wird sofort übernommen. ![Screenshot zu Anwendungsproxy-Connectorgruppen – Gruppe aus dem Dropdown-Menü auswählen](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## Weitere Informationen

- [Aktivieren des Anwendungsproxys](active-directory-application-proxy-enable.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)
- [Problembehandlung von Anwendungsproxys](active-directory-application-proxy-troubleshoot.md)

Aktuelle Neuigkeiten und Updates finden Sie im [Blog zum Anwendungsproxy](http://blogs.technet.com/b/applicationproxyblog/).

<!---HONumber=AcomDC_0511_2016-->