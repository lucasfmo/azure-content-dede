<properties 
    pageTitle="Konfigurieren der aktiven Georeplikation für Azure SQL-Datenbank mit PowerShell | Microsoft Azure" 
    description="Konfigurieren der aktiven Georeplikation für die Azure SQL-Datenbank mit PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# Konfigurieren der Georeplikation für die Azure SQL-Datenbank mit PowerShell



> [AZURE.SELECTOR]
- [Azure-Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


In diesem Artikel wird beschrieben, wie Sie die Georeplikation der SQL-Datenbank mit PowerShell konfigurieren.

Informationen zum Initiieren eines Failovers finden Sie unter [Initiieren eines geplanten oder ungeplanten Failovers für die Azure SQL-Datenbank](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Die aktive Georeplikation (lesbare sekundäre Datenbanken) ist jetzt für alle Datenbanken in allen Diensttarifen verfügbar. Im April 2017 wird der nicht lesbare sekundäre Typ eingestellt, und vorhandene nicht lesbare Datenbanken werden automatisch auf lesbare sekundäre Datenbanken aktualisiert.

Sie können bis zu vier lesbare sekundäre Datenbanken in derselben oder in verschiedenen Standorten von Rechenzentren (Regionen) konfigurieren. Sekundäre Datenbanken stehen zur Verfügung, wenn ein Datencenter ausgefallen ist oder keine Verbindung mit der primären Datenbank möglich ist.

Zum Konfigurieren der Georeplikation benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **KOSTENLOSES KONTO** klicken. Lesen Sie anschließend diesen Artikel zu Ende.
- Eine Azure SQL-Datenbank: Die primäre Datenbank, die in eine andere geografische Region repliziert werden soll.
- Azure PowerShell 1.0 oder höher Sie können die Azure-PowerShell-Module herunterladen und installieren, indem Sie Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) befolgen.


## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Starten Sie also PowerShell, und führen Sie dann das folgende Cmdlet aus. Geben Sie auf dem Anmeldebildschirm die E-Mail-Adresse und das Kennwort wie für die Anmeldung beim Azure-Portal ein.


	Login-AzureRmAccount

Nach der erfolgreichen Anmeldung werden einige Informationen auf dem Bildschirm angezeigt, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zum Auswählen des Abonnements benötigen Sie Ihre Abonnement-ID. Sie können die Abonnement-ID aus den Informationen im vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen und mehr Details benötigen, können Sie auch das Cmdlet **Get-AzureRmSubscription** ausführen und die gewünschten Abonnementinformationen aus dem Resultset kopieren. Das folgende Cmdlet verwendet die Abonnement-ID, um das aktuelle Abonnement festzulegen:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Nach dem erfolgreichen Ausführen von **Select-AzureRMSubscription** kehren Sie zur PowerShell-Eingabeaufforderung zurück.


## Hinzufügen einer sekundären Datenbank


Mit den folgenden Schritten wird eine neue sekundäre Datenbank in einer Partnerschaft für die Georeplikation erstellt.
  
Zum Aktivieren einer sekundären Datenbank müssen Sie der Besitzer oder Mitbesitzer des Abonnements sein.

Sie können das Cmdlet **New-AzureRmSqlDatabaseSecondary** nutzen, um eine sekundäre Datenbank auf einem Partnerserver in einer lokalen Datenbank auf dem Server hinzuzufügen, mit dem Sie verbunden sind (die primäre Datenbank).

Dieses Cmdlet ersetzt **Start AzureSqlDatabaseCopy** durch den **–IsContinuous**-Parameter. Es gibt ein **AzureRmSqlDatabaseSecondary**-Objekt aus, das von anderen Cmdlets für die eindeutige Bezeichnung einer bestimmten Replikationsverknüpfung verwendet werden kann. Dieses Cmdlet wird zurückgegeben, wenn die sekundäre Datenbank erstellt wurde und das Seeding erfolgt ist Je nach Größe der Datenbank liegt der Zeitaufwand zwischen wenigen Minuten und mehreren Stunden.

Die replizierte Datenbank auf dem zweiten Server hat den gleichen Namen wie die primäre Datenbank und standardmäßig auch die gleiche Dienstebene. Die sekundäre Datenbank kann lesbar oder nicht lesbar und eine Einzeldatenbank oder eine elastische Datenbank sein. Weitere Informationen finden Sie unter [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) und [Dienstebenen](sql-database-service-tiers.md). Nachdem die sekundäre Datenbank erstellt und das Seeding ausgeführt wurde, beginnt die Replikation der Daten von der primären Datenbank in die neue sekundäre Datenbank. Nachfolgend erfahren Sie, wie Sie mithilfe von PowerShell nicht lesbare und lesbare sekundäre Datenbanken erstellen – mit einer Einzeldatenbank oder einer elastischen Datenbank.

Wenn die Partnerdatenbank bereits vorhanden ist (z.B. aufgrund der Beendigung einer vorherigen Georeplikationsbeziehung), tritt für den Befehl ein Fehler auf.



### Hinzufügen einer nicht lesbaren sekundären Datenbank (Einzeldatenbank)

Mit dem folgenden Befehl erstellen Sie eine nicht lesbare sekundäre Datenbank der „mydb“-Datenbank des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### Hinzufügen einer lesbaren sekundären Datenbank (Einzeldatenbank)

Mit dem folgenden Befehl erstellen Sie eine lesbare sekundäre Datenbank der „mydb“-Datenbank des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Hinzufügen einer nicht lesbaren sekundären Datenbank (elastische Datenbank)

Mit dem folgenden Befehl erstellen Sie eine nicht lesbare sekundäre Datenbank der „mydb“-Datenbank im elastischen Datenbankpool „ElasticPool1“ des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### Hinzufügen einer lesbaren sekundären Datenbank (elastische Datenbank)

Mit dem folgenden Befehl erstellen Sie eine lesbare sekundäre Datenbank der „mydb“-Datenbank im elastischen Datenbankpool „ElasticPool1“ des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Entfernen einer sekundären Datenbank

Mit dem Cmdlet **Remove-AzureRmSqlDatabaseSecondary** können Sie die Replikationspartnerschaft zwischen einer sekundären Datenbank und ihrer primären Datenbank dauerhaft beenden. Nach dem Beenden der Beziehung wird die sekundäre Datenbank eine Datenbank mit Lese-/Schreibzugriff. Wenn die Verbindung mit der sekundären Datenbank unterbrochen wird, ist der Befehl zwar erfolgreich, aber die sekundäre Datenbank wird erst mit Lese-/ Schreibzugriff versehen, nachdem die Verbindung wiederhergestellt wurde. Weitere Informationen finden Sie unter [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) und [Tarife](sql-database-service-tiers.md).

Dieses Cmdlet ersetzt „Stop-AzureSqlDatabaseCopy“ für die Replikation.

Dieser Vorgang ähnelt einer erzwungenen Beendigung, die die Replikationsverknüpfung entfernt. Die sekundäre Datenbank ist nun eine eigenständige Datenbank, die vor der Beendigung nicht komplett repliziert wird. Alle verknüpften Daten werden auf den vorherigen primären und sekundären Datenbanken bereinigt, falls oder wenn sie verfügbar sind. Dieses Cmdlet gibt zurück, wenn die Replikationsverknüpfung entfernt wird.


Zum Entfernen der sekundären Datenbank benötigen die Benutzer Schreibzugriff auf primäre und sekundäre Datenbanken entsprechend RBAC. Weitere Informationen finden Sie im Abschnitt zur rollenbasierten Zugriffssteuerung.

Mit dem folgenden Code entfernen Sie die Replikationsverknüpfung der „mydb“-Datenbank zum Server „srv2“ der Ressourcengruppe „rg2“.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## Überprüfen der Konfiguration und Integrität der Georeplikation

Zu den Überwachungsaufgaben gehören die Überwachung der Konfiguration der Georeplikation und der Integrität der Datenreplikation.

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) kann verwendet werden, um die Informationen zu den „forward“-Replikationsverknüpfungen in der Katalogsicht „sys.geo\_replication\_links“ abzurufen.

Der folgende Befehl ruft den Status der Replikationsverknüpfung zwischen der primären „mydb“-Datenbank und der sekundären Datenbank auf Server „srv2“ von der Ressourcengruppe „rg2“ auf.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


  

## Nächste Schritte

- [Initiieren eines geplanten oder ungeplanten Failovers für die Azure SQL-Datenbank](sql-database-geo-replication-failover-powershell.md)
- [Warnungen zur Notfallwiederherstellung](sql-database-disaster-recovery-drills.md)




## Zusätzliche Ressourcen

- [Sicherheitskonfiguration für die Georeplikation](sql-database-geo-replication-security-config.md)
- [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/) (Die neuen Georeplikationsfunktionen im Überblick)
- [BCDR in SQL-Datenbank – Häufig gestellte Fragen](sql-database-bcdr-faq.md)
- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [Aktive Georeplikation](sql-database-geo-replication-overview.md)
- [Entwerfen einer Anwendung für die cloudbasierte Notfallwiederherstellung](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Abschließen der wiederhergestellten Azure SQL-Datenbank](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0608_2016-->