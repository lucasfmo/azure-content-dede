<properties
	pageTitle="Empfohlene Methoden für Azure App Service"
	description="Informationen zu empfohlenen Methoden und zur Problembehandlung für Azure App Service."
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="dariagrigoriu"/>
    
# Empfohlene Methoden für Azure App Service

In diesem Artikel werden die empfohlenen Methoden für die Verwendung von [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) zusammengefasst.

## <a name="colocation"></a>Zusammenstellen
Befinden sich Azure-Ressourcen, aus denen sich eine Lösung zusammensetzt (beispielsweise eine Web-App und eine Datenbank), in unterschiedlichen Regionen, kann sich dies folgendermaßen auswirken:

*  erhöhte Latenz bei der Kommunikation zwischen den Ressourcen
*  Kosten für ausgehende, regionsübergreifende Datenübertragung, Details finden Sie in der [Azure-Preisübersicht](https://azure.microsoft.com/pricing/details/data-transfers)

Das Zusammenstellen in derselben Region eignet sich am besten für Azure-Ressourcen, die zusammen eine Lösung bilden, wie z.B. eine Web-App und eine Datenbank oder ein Speicherkonto für die Inhalte oder Daten. Beim Erstellen von Ressourcen sollten Sie sicherstellen, dass sich diese in derselben Azure-Region befinden, es sei denn, es sprechen geschäftliche oder entwurfstechnische Gründe dagegen. Mithilfe der [App Service-Klonfunktion](app-service-web-app-cloning-portal.md), die derzeit für Premium-App Service-Plan-Apps verfügbar ist, können Sie eine App Service-App in die gleiche Region wie Ihre Datenbank verschieben.

## <a name="memoryresources"></a>Wenn Apps mehr Speicherplatz als erwartet belegen
Wenn Sie durch Überwachung oder Dienstempfehlungen feststellen, dass eine App mehr Speicherplatz belegt als erwartet, sollten Sie die [Selbstreparaturfunktion von App Service](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites) in Betracht ziehen. Eine der Optionen der Selbstreparaturfunktion besteht darin, benutzerdefinierte Aktionen basierend auf einem Speicherschwellenwert zu ergreifen. Die Aktionen reichen von E-Mail-Benachrichtigungen über eine Untersuchung mittels Speicherabbild bis hin zur Behebung vor Ort durch Recycling des Arbeitsprozesses. Die automatische Reparatur kann, wie in diesem Blogbeitrag für die [App Service Support-Websiteerweiterung](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps) beschrieben, über „web.config“ und eine benutzerfreundliche Benutzeroberfläche konfiguriert werden.

## <a name="CPUresources"></a>Wenn Apps mehr CPU-Leistung als erwartet beanspruchen
Wenn Sie feststellen, dass eine App mehr CPU-Leistung als erwartet beansprucht, oder wenn laut Überwachung oder Dienstempfehlungen wiederholt CPU-Spitzen zu verzeichnen sind, sollten Sie in Betracht ziehen, den App Service-Plan entweder zentral oder horizontal hochzuskalieren. Wenn Ihre Anwendung zustandsbehaftet ist, ist eine zentrale Hochskalierung die einzige Option. Ist Ihre Anwendung zustandslos, erreichen Sie mit einer horizontalen Hochskalierung mehr Flexibilität und ein höheres Skalierungspotenzial.

Weitere Informationen zu „zustandsbehafteten“ und „zustandslosen“ Anwendungen beinhaltet dieses Video: [Planning a Scalable End-to-End Multi-Tier Application on Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid) (Planen einer skalierbaren End-to-End-Anwendung mit mehreren Ebenen in einer Microsoft Azure-Web-App). Weitere Informationen zur Skalierung von App Service und Optionen zur automatischen Skalierung finden Sie im Artikel [Skalieren einer Web-App in Azure App Service](web-sites-scale.md).

## <a name="socketresources"></a>Wenn Socketressourcen erschöpft sind
Eine häufige Ursache für das Erschöpfen ausgehender TCP-Verbindungen ist die Verwendung von Clientbibliotheken, die nicht zur Wiederverwendung von TCP-Verbindungen implementiert wurden, oder im Fall eines übergeordneten Protokolls wie HTTP die fehlende Nutzung von Keep-Alive. Prüfen Sie die Dokumentation zu den einzelnen Bibliotheken, auf die von den Apps in Ihrem App Service-Plan verwiesen wird, um sicherzustellen, dass sie konfiguriert sind oder dass in Ihrem Code darauf zugegriffen wird, um so eine effiziente Wiederverwendung ausgehender Verbindungen zu gewährleisten. Befolgen Sie auch den Leitfaden zur Bibliotheksdokumentation für eine ordnungsgemäße Erstellung und Freigabe oder Bereinigung, um Verbindungsverluste zu vermeiden. Während diese Clientbibliotheksuntersuchungen ausgeführt werden, können die Auswirkungen durch ein horizontales Hochskalieren auf mehrere Instanzen verringert werden.

<!---HONumber=AcomDC_0525_2016-->