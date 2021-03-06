<properties 
	pageTitle="Erstellen von Datenstichproben aus SQL Server in Azure | Microsoft Azure" 
	description="Erstellen von Datenstichproben aus SQL Server in Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Erstellen von Datenstichproben aus SQL Server in Azure


Dieses Dokument beschreibt die Erstellung von Stichproben für SQL Server-Daten in Azure unter Verwendung von SQL oder der Programmiersprache Python. Es wird gezeigt, wie erfasste Daten in Azure Machine Learning verschoben werden, indem Sie sie in einer Datei speichern, diese in einen Azure-Blob hochladen und anschließend in Azure Machine Learning Studio lesen.

Die Python-Stichprobe verwendet die [pyodbc](https://code.google.com/p/pyodbc/)-ODBC-Bibliothek für die Verbindung mit SQL Server auf Azure und die [Pandas](http://pandas.pydata.org/)-Bibliothek zum Ausführen der Stichprobe.

>[AZURE.NOTE] Beim SQL-Beispielcode in diesem Dokument wird davon ausgegangen, dass die Daten auf einem SQL Server in Azure gespeichert sind. Wenn dies nicht der Fall ist, finden Sie im Thema [Verschieben von Daten nach SQL Server in Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) Anweisungen zum Verschieben von Daten zu SQL Server unter Azure.

**Warum eine Datenstichprobe entnehmen?** Wenn das Dataset, das Sie analysieren möchten, groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Dies erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Cortana-Analyseprozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Modelle für das maschinelle Lernen zu erstellen.

Das nachstehende **Menü** enthält Links zu Themen, die beschreiben, wie Datenstichproben aus verschiedenen Speicherumgebungen erstellt werden.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Diese Datenstichprobenaufgabe ist ein Teil des [Cortana Analytics-Prozesses (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>Mit SQL

In diesem Abschnitt werden verschiedene Methoden beschrieben, um mit SQL einfache Stichproben der Daten in der Datenbank zu extrahieren. Wählen Sie eine Methode, die für Ihre Datengröße und -verteilung geeignet ist.

Die beiden Beispiele unten zeigen, wie Sie mit "newid" in SQL Server eine Stichprobe erstellen. Die von Ihnen gewählte Methode hängt davon ab, wie zufällig die Stichprobe sein soll ("pk\_id" wird im Beispielcode unten als ein automatisch generierter Primärschlüssel angenommen).

1. Beispiel mit geringerer Randomisierung

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. Beispiel mit höherer Randomisierung

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Sie können auch mit TABLESAMPLE eine Stichprobe erstellen, wie unten veranschaulicht. Dies ist möglicherweise besser, wenn Ihre Daten sehr umfangreich sind (vorausgesetzt, dass die Daten auf unterschiedlichen Seiten nicht korrelieren), damit die Abfrage in einem angemessenen Zeitraum abgeschlossen wird.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Sie können Funktionen aus diesen Stichprobendaten durchsuchen und generieren, indem Sie sie in einer neuen Tabelle speichern.


###<a name="sql-aml"></a>Herstellen einer Verbindung mit Azure Machine Learning

Sie können die Beispielabfragen oben direkt im „Import Data“-Modul von Azure ML verwenden, um Stichproben aus den Daten dynamisch zu erstellen und in einem Azure ML-Experiment zu verwenden. Dies ist ein Screenshot der Verwendung des Reader-Moduls zum Lesen der erfassten Daten:
   
![reader sql][1]

##<a name="python"></a>Mit der Programmiersprache Python 

In diesem Abschnitt wird veranschaulicht, wie die [pyodbc-Bibliothek](https://code.google.com/p/pyodbc/) in Python für das Herstellen einer ODBC-Verbindung mit einer SQL Server-Datenbank verwendet wird. Die Datenbankverbindungszeichenfolge lautet wie folgt (ersetzen Sie "servername", "dbname", "username" und "password" durch die entsprechenden Werte Ihrer Konfiguration):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Die [Pandas](http://pandas.pydata.org/)-Bibliothek in Python bietet eine Vielzahl von Datenstrukturen und Datenanalysetools für die Datenbearbeitung durch Python-Programmierung. Der folgende Code liest eine Datenstichprobe von 0,1 % aus einer Tabelle in einer Azure SQL-Datenbank in ein Pandas-DataFrame:

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Sie können jetzt mit den erfassten Daten im Pandas-DataFrame arbeiten.

###<a name="python-aml"></a>Herstellen einer Verbindung mit Azure Machine Learning

Mit dem folgenden Beispielcode können Sie die Stichprobendaten in einer Datei speichern und in ein Azure-Blob hochladen. Die Daten im Blob können mit dem *„Import Data“-Modul* direkt in ein Azure ML-Experiment gelesen werden. Die Schritte lauten wie folgt:

1. Schreiben Sie den Pandas-DataFrame in eine lokale Datei:

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Laden Sie die lokale Datei in das Azure-Blob hoch:

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Lesen Sie die Daten wie in der folgenden Abbildung dargestellt mit dem Azure ML-*Import Data*-Modul aus dem Azure-Blob:
 
![Reader-Blob][2]

## Beispiel: Cortana-Analyseprozess (CAP) in Aktion

Eine umfassende exemplarische Vorgehensweise zur Verwendung des Cortana-Analyseprozesses mit einem öffentlichen DataSet finden Sie unter [Cortana-Analyseprozess in Aktion: Verwenden von SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 

<!---HONumber=AcomDC_0608_2016-->