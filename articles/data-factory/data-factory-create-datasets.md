<properties 
	pageTitle="Datasets in Azure Data Factory | Microsoft Azure" 
	description="Lernen Sie Azure Data Factory-Datasets kennen, und erfahren Sie, wie Sie sie erstellen können." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2016" 
	ms.author="spelluru"/>

# Datasets in Azure Data Factory
Datasets in Azure Data Factory sind benannte Verweise/Zeiger auf die Daten, die Sie als Eingabe oder Ausgabe einer Pipeline verwenden möchten. Datasets bestimmen Daten in verschiedenen Datenspeichern einschließlich Tabellen, Dateien, Ordnern und Dokumenten.

Ein **verknüpfter Dienst** definiert die Informationen, die Azure Data Factory zum **Herstellen einer Verbindung** mit einem **Datenspeicher** (wie einem Azure Storage-Konto und Azure SQL-Datenbank) oder **Computediensten** (wie Azure HDInsight und Azure Batch) benötigt. Der verknüpfte Dienst definiert den Mechanismus (Adresse, Protokoll, Authentifizierungsschema usw.) für den Zugriff auf den Datenspeicher oder den Computedienst.

Eine Liste mit den unterstützten verknüpften Datenspeicherdiensten finden Sie unter [Unterstützte Datenquellen](data-factory-data-movement-activities.md#supported-data-stores). Klicken Sie auf eine Datenquelle in der Tabelle, um das Thema aufzurufen, das Details zum Erstellen/Konfigurieren eines verknüpften Diensts für diesen Datenspeicher bereitstellt.

Eine Liste der unterstützten verknüpften Computedienste finden Sie unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md). Informationen zu den Aktivitäten, die die verknüpften Dienste verwenden, finden Sie unter [Transformation und Analyse mit Azure Data Factory](data-factory-data-transformation-activities.md).

Ein **Dataset** in Data Factory stellt Datenstrukturen in einem Datenspeicher dar, der durch einen **verknüpften Datenspeicherdienst**, z.B. einen Blobcontainer in einem Azure-Speicherkonto, eine Tabelle in einer Azure SQL-Datenbank, dargestellt wird. Sie können es als Eingabe oder Ausgabe einer Aktivität in einer Pipeline verwendet. Nachdem Sie Datasets erstellt haben, können Sie diese mit Aktivitäten in der Pipeline verwenden. Beispielsweise können Sie ein Dataset als Eingabe/Ausgabe-Dataset einer Kopieraktivität/HDInsightHive-Aktivität festlegen.

> [AZURE.NOTE] Wenn Sie noch nicht mit Azure Data Factory vertraut sind, finden Sie unter [Einführung in Azure Data Factory](data-factory-introduction.md) eine Übersicht über den Azure Data Factory-Dienst und unter [Erstellen der ersten Data Factory](data-factory-build-your-first-pipeline.md) ein Tutorial zum Erstellen Ihrer ersten Data Factory. Diese beiden Artikel liefern Hintergrundinformationen, die Sie zum besseren Verständnis dieses Artikels benötigen.


## Definieren von Datasets
Ein Dataset in Azure Data Factory wird wie folgt definiert:


	{
	    "name": "<name of dataset>",
	    "properties": {
	        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
			"external": <boolean flag to indicate external data. only for input datasets>,
	        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
	        "structure": [
	            {
	                "name": "<Name of the column>",
	                "type": "<Name of the type>"
	            }
	        ],
	        "typeProperties": {
	            "<type specific property>": "<value>",
				"<type specific property 2>": "<value 2>",
	        },
	        "availability": {
	            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
	            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
	        },
	       "policy": 
	        {      
	        }
	    }
	}

In der folgenden Tabelle werden die Eigenschaften im obigen JSON-Code beschrieben:

| Eigenschaft | Beschreibung | Erforderlich | Standard |
| -------- | ----------- | -------- | ------- |
| Name | Name des Datasets. Unter [Azure Data Factory – Benennungsregeln](data-factory-naming-rules.md) finden Sie die Benennungsregeln. | Ja | k. A. |
| Typ | Typ des Datasets. Geben Sie einen der von Azure Data Factory unterstützten Typen ein (z. B.: „azureblob“, „azuresqltable“). <br/><br/>Im Abschnitt [Dataset: Type](#Type) finden Sie weitere Details. | Ja | – |
| structure | Schema des Datasets<br/><br/>Im Abschnitt [Dataset: Structure](#Structure) finden Sie weitere Details. | Nr. | k. A. |
| typeProperties | Eigenschaften, die dem ausgewählten Typ entsprechen. Im Abschnitt [Dataset: Type](#Type) finden Sie ausführliche Informationen über die unterstützten Typen und deren Eigenschaften. | Ja | – |
| external | Boolesches Flag, das angibt, ob ein Dataset explizit durch eine Data Factory-Pipeline erstellt wird oder nicht. | Nein | false | 
| availability | Definiert das Verarbeitungsfenster oder das Modell für das Aufteilen in Slices für die Datasetproduktion. <br/><br/>Unter [Dataset: Availability](#Availability) finden Sie weitere Informationen.<br/><br/>Im Artikel [Planung und Ausführung](data-factory-scheduling-and-execution.md) finden Sie weitere Informationen zum Modell für das Aufteilen von Datasets in Slices. | Ja | –
| policy | Definiert die Kriterien oder die Bedingung, die die Dataset-Slices erfüllen müssen. <br/><br/>Unter [Dataset: Policy](#Policy) finden Sie weitere Informationen. | Nein | – |

### Beispiel

Im Folgenden finden Sie ein Beispiel für ein Dataset, das eine Tabelle mit dem Namen **MyTable** in einer **Azure SQL-Datenbank** darstellt.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

Beachten Sie Folgendes:

- „type“ ist auf „AzuresqlTable“ festgelegt.
- Als tableName-Typeigenschaft (spezifisch für AzureSplTable-Typ) ist „MyTable“ festgelegt.
- „linkedServiceName“ bezieht sich auf einen verknüpften Dienst vom Typ „AzureSqlDatabase“. Die Definition des verknüpften Dienstes finden Sie nachfolgend. 
- Als „availability frequency“ ist „day“ festgelegt und „interval“ ist auf „1“ festgelegt. Das bedeutet, dass der Slice täglich erzeugt wird.  

AzureSqlLinkedService wird wie folgt definiert:

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}

Im obigen JSON-Code:

- „type“ ist auf „AzureStorage“ festgelegt.
- Die „connectionString“-Typeigenschaft gibt Informationen zur Verbindung mit einer Azure SQL-Datenbank.  


Wie Sie sehen können, definiert der verknüpfte Dienst, wie eine Verbindung zu einer Azure SQL-Datenbank erstellt wird, und das Dataset definiert, welche Tabelle als Eingabe/Ausgabe in Ihrer Data Factory verwendet wird. Der Abschnitt „Aktivität“ in Ihrer [JSON](data-factory-create-pipelines.md)-Pipeline bestimmt, ob das Dataset als Eingabe- oder Ausgabedataset verwendet wird.


> [AZURE.IMPORTANT] Insofern kein Dataset von Azure Data Factory erzeugt wird, sollte es als **extern** markiert werden. Dies gilt im Allgemeinen für Eingaben der ersten Aktivität in einer Pipeline.

## <a name="Type"></a> Dataset: Type
Die unterstützten Datenquellen und die Dataset-Typen werden ausgerichtet. Informationen zu den Typen und der Konfiguration von Datasets finden Sie in den Themen, auf die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md#supported-data-stores) verwiesen wird. Wenn Sie z. B. Daten aus einer Azure SQL-Datenbank verwenden, klicken Sie in der Liste der unterstützten Datenspeicher auf Azure SQL-Datenbank, um ausführliche Informationen zur Verwendung von Azure SQL-Datenbank als Quelle oder Senkendatenspeicher zu erhalten.

## <a name="Structure"></a>Dataset: Structure
Der Abschnitt **Structure** definiert das Schema des Datasets. Er enthält eine Auflistung der Namen und Datentypen der Spalten. Im folgenden Beispiel hat das Dataset die drei Spalten „slicetimestamp“, „projectname“ und „pageviews“. Sie entsprechen dem Typ „String“, „String“ und „Decimal“.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Availability"></a> Dataset: Availability
Der Abschnitt **Availability** in einem Dataset definiert das Verarbeitungsfenster (stündlich, täglich, wöchentlich usw.) oder das Modell für das Aufteilen in Slices für das Dataset. Im Artikel [Planung und Ausführung](data-factory-scheduling-and-execution.md) finden Sie weitere Informationen zum Modell für das Aufteilen von Datasets in Slices und Abhängigkeit.

Der nachfolgende Abschnitt „Availability“ definiert, dass das Dataset im Falle eines Ausgabe-Datasets stündlich erstellt wird oder im Falle eines Eingabe-Datasets stündlich verfügbar ist.

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 1	
	}

In der folgenden Tabelle werden die Eigenschaften beschrieben, die Sie im Abschnitt „availability“ verwenden können.

| Eigenschaft | Beschreibung | Erforderlich | Standard |
| -------- | ----------- | -------- | ------- |
| frequency | Gibt die Zeiteinheit für die Erstellung der Datasetslices an.<br/><br/>**Unterstützte Häufigkeit**: „Minute“, „Hour“, „Day“, „Week“, „Month“ | Ja | – |
| interval | Gibt einen Multiplikator für die Häufigkeit an<br/><br/>„Frequency x interval“ bestimmt, wie oft der Slice erzeugt wird.<br/><br/>Wenn das Dataset auf Stundenbasis in Slices aufgeteilt werden soll, legen Sie **Frequency** auf **Hour** und **interval** auf **1** fest.<br/><br/>**Hinweis:** Wenn Sie „Frequency“ auf „Minute“ festlegen, sollten Sie „interval“ mindestens auf „15“ festlegen. | Ja | – |
| style | Gibt an, ob der Slice am Anfang/Ende des Intervalls erzeugt werden soll.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Wenn „Frequency“ auf „Month“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice am letzten Tag des Monats erstellt. Wenn „style“ auf „StartOfInterval“ festgelegt ist, wird der Slice am ersten Tag des Monats erstellt.<br/><br/>Wenn „Frequency“ auf „Day“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice in der letzten Stunde des Tages erstellt.<br/><br/>Wenn „Frequency“ auf „Hour“ und „style“ auf „EndOfInterval“ festgelegt ist, wird der Slice am Ende der Stunde erstellt. Ein Slice für den Zeitraum 13:00 bis 14:00 Uhr wird z. B. um 14.00 Uhr erstellt. | Nein | EndOfInterval |
| anchorDateTime | Definiert die absolute Position in der Zeit, die der Scheduler benötigt, um Dataset-Slicegrenzen zu berechnen. <br/><br/>**Hinweis:** Wenn anchorDateTime über Datumsteile verfügt, die präziser als die Häufigkeit sind, werden die präziseren Teile ignoriert. <br/><br/>Wenn **interval** z.B. auf **hourly** festgelegt ist („frequency: hour“ und „interval: 1“) und **anchorDateTime** Angaben für **Minuten und Sekunden** enthält, werden die **Minuten- und Sekundenteile** von anchorDateTime ignoriert. | Nein | 01/01/0001 |
| offset | Zeitspanne, um die Anfang und Ende aller Datasetslices verschoben werden. <br/><br/>**Hinweis:** Wenn sowohl „anchorDateTime“ als auch „offset“ angegeben werden, ist das Ergebnis die kombinierte Verschiebung. | Nein | k. A. |

### Beispiel zu Offset

Tägliche Slices, die um 6:00 Uhr anstelle des Standards Mitternacht beginnen.

	"availability":
	{
		"frequency": "Day",
		"interval": 1,
		"offset": "06:00:00"
	}

**frequency** ist auf **Month** und **interval** ist auf **1** (einmal im Monat) festgelegt: Wenn der Slice immer am neunten Tag jedes Monats um 6:00 Uhr erstellt werden soll, müssen Sie „offset“ auf „09.06:00:00“ festlegen. Beachten Sie, dass es sich hierbei um die UTC-Uhrzeit handelt.

Für einen zwölfmonatigen Plan ("frequency" = "month"; "interval" = 12) bedeutet "offset": 60.00:00:00 den Beginn am 1. oder 2. März jedes Jahres (60 Tage ab dem Anfang des Jahres, wenn "style" = "StartOfInterval"), abhängig davon, ob es sich um ein Schaltjahr handelt.

### Beispiel zu „anchorDateTime“

**Beispiel**: 23-Stunden-Datasetslices mit Startbeginn "2007-04-19T08:00:00"

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 23,	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}

### Beispiel zu „offset“/„style“

Wenn Sie ein Dataset jeden Monat an einem bestimmten Tag und zu einer bestimmten Uhrzeit ausführen müssen (beispielsweise am 3. jedes Monats um 8:00 Uhr), könnten Sie mit dem **offset**-Tag das Datum und die Uhrzeit der Ausführung angeben.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}


## <a name="Policy"></a>Dataset-Richtlinie

Der Abschnitt **policy** in der Datasetdefinition definiert die Kriterien oder die Bedingung, die die Datasetslices erfüllen müssen.

### Überprüfungsrichtlinien

| Richtlinienname | Beschreibung | Angewendet auf | Erforderlich | Standard |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Überprüft, ob die Daten in einem **Azure-Blob** die minimalen Größenanforderungen (in Megabytes) erfüllen. | Azure Blob | Nein | – |
|minimumRows | Überprüft, ob die Daten in einer **Azure SQL-Datenbank** oder einer **Azure-Tabelle** die minimale Anzahl von Zeilen enthalten. | <ul><li>Azure SQL-Datenbank</li><li>Azure-Tabelle</li></ul> | Nein | –

#### Beispiele

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Externe Datasets

Externe Datasets werden nicht durch eine Pipeline erstellt, die in der Data Factory ausgeführt wird. Wenn das Dataset als **extern** gekennzeichnet ist, kann die **ExternalData**-Richtlinie definiert werden, um das Verhalten der Dataset-Sliceverfügbarkeit zu beeinflussen.

Insofern kein Dataset von Azure Data Factory erzeugt wird, sollte es als **extern** markiert werden. Dies gilt im Allgemeinen für die Eingabe(n) der ersten Aktivität in einer Pipeline, insofern nicht die Aktivität oder Pipeline-Verkettung genutzt wird.

| Name | Beschreibung | Erforderlich | Standardwert |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Zeit, um die die Prüfung der Verfügbarkeit der externen Daten für den angegebenen Slice verzögert wird. Wenn die Daten z.B. stündlich verfügbar sein sollen, die Überprüfung der externen Daten verfügbar und der entsprechende Slice bereit ist, kann eine Verzögerung per „dataDelay“ ausgeführt werden.<br/><br/>Diese bezieht sich nur auf die aktuelle Zeit. Wenn es z.B. gerade 13:00 Uhr ist und dieser Wert 10 Minuten beträgt, beginnt die Überprüfung um 13:10 Uhr.<br/><br/>Diese Einstellung wirkt sich nicht auf Slices in der Vergangenheit aus (ohne Verzögerung werden Slices verarbeitet, für die Folgendes gilt: Slice-Endzeit + dataDelay < jetzt).<br/><br/>Ein Zeitwert von mehr als 23:59 Stunden muss im Format „Tag.Stunden:Minuten:Sekunden“ angegeben werden. Um beispielsweise 24 Stunden anzugeben, verwenden Sie nicht 24:00:00, sondern stattdessen 1.00:00:00. Wenn Sie 24:00:00 verwenden, wird dies als 24 Tage (24.00:00:00) gewertet. Für 1 Tag und 4 Stunden geben Sie „1.04:00:00“ an. | Nein | 0 |
| retryInterval | Die Wartezeit zwischen einem Fehler und dem nächsten Wiederholungsversuch. Gilt für die aktuelle Zeit. Wenn der vorherige Versuch fehlgeschlagen ist, ist dies die Wartezeit nach dem letzten Versuch. <br/><br/>Wenn es jetzt gerade 13:00 Uhr ist, beginnt der erste Versuch. Wenn die Ausführung der ersten Überprüfung 1 Minute gedauert hat und ein Fehler aufgetreten ist, findet die nächste Wiederholung um 13:00 + 1 Min. (Dauer) + 1 Min. (Wiederholungsintervall) = 1:02 Uhr statt. <br/><br/>Für Slices in der Vergangenheit gibt es keine Verzögerung. Die Wiederholung erfolgt sofort. | Nein | 00:01:00 (1 Minute) | 
| retryTimeout | Das Timeout für die einzelnen Wiederholungsversuche.<br/><br/>Wenn dieser Wert auf 10 Minuten festgelegt ist, muss die Überprüfung innerhalb von 10 Minuten abgeschlossen werden. Wenn die Ausführung der Überprüfung länger als 10 Minuten dauert, wird das Timeout für die Wiederholung wirksam.<br/><br/>Wenn für alle Überprüfungsversuche ein Timeout wirksam wird, wird der Slice als „TimedOut“ gekennzeichnet. | Nein | 00:10:00 (10 Minuten) |
| maximumRetry | Gibt an, wie oft die Verfügbarkeit der externen Daten überprüft werden soll. Der zulässige Höchstwert ist 10. | Nein | 3 | 

## Zugeordnete Datasets
Mit der Eigenschaft **datasets** können Sie Datasets erstellen, die einer Pipeline zugeordnet sind. Diese Datasets können nur von Aktivitäten innerhalb dieser Pipeline, aber nicht von Aktivitäten in anderen Pipelines verwendet werden. Das folgende Beispiel definiert eine Pipeline mit zwei Datasets (InputDataset Rdc und OutputDataset-Rdc), die in der Pipeline verwendet werden sollen.

> [AZURE.IMPORTANT] Zugeordnete Datasets werden nur mit einmalig ausgeführten Pipelines unterstützt (**pipelineMode** ist auf **OneTime** festgelegt). Weitere Informationen finden Sie unter [Pipeline mit einmaliger Ausführung](data-factory-scheduling-and-execution.md#onetime-pipeline).

	{
	    "name": "CopyPipeline-rdc",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset-rdc"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset-rdc"
	                    }
	                ],
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1,
	                    "style": "StartOfInterval"
	                },
	                "name": "CopyActivity-0"
	            }
	        ],
	        "start": "2016-02-28T00:00:00Z",
	        "end": "2016-02-28T00:00:00Z",
	        "isPaused": false,
	        "pipelineMode": "OneTime",
	        "expirationTime": "15.00:00:00",
	        "datasets": [
	            {
	                "name": "InputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "InputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/input",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": true,
	                    "policy": {}
	                }
	            },
	            {
	                "name": "OutputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "OutputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/output",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": false,
	                    "policy": {}
	                }
	            }
	        ]
	    }
	}

<!---HONumber=AcomDC_0608_2016-->