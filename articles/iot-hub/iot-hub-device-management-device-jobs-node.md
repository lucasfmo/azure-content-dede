<properties
	pageTitle="IoT Hub-Geräteverwaltung: Geräteaufträge | Microsoft Azure"
	description="Im Tutorial zur Azure IoT Hub-Geräteverwaltung wird beschrieben, wie Geräteaufträge verwendet werden, um Vorgänge wie Remotefirmwareupdates auszuführen."
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Tutorial: Aktualisieren der Gerätefirmware mithilfe von Geräteaufträgen (Vorschau)

[AZURE.INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## Einführung
Die Azure IoT-Geräteverwaltung ermöglicht Ihnen, mithilfe von Geräteaufträgen mit physischen Geräten zu interagieren. Nachdem Sie den Gerätezwilling (die Dienstdarstellung eines physischen Geräts) identifiziert haben, können Sie mithilfe von Geräteaufträgen mit dem entsprechenden physischen Gerät interagieren. Geräteaufträge aktivieren die Koordination von komplexen Prozessen auf mehreren Geräten. Dieser Prozess kann mehrere Schritte und Vorgänge mit langer Laufzeit enthalten.

Es gibt sechs Typen von Geräteaufträgen, die zurzeit von der Azure IoT Hub-Geräteverwaltung bereitgestellt werden (es werden zusätzliche Aufträge hinzugefügt, sobald Kunden sie benötigen):

- **Firmwareupdate**: Dient zum Aktualisieren der Firmware (bzw. des Betriebssystemimage) auf dem physischen Gerät.
- **Neustart**: Startet das physische Gerät neu.
- **Zurücksetzung auf Werkseinstellungen**: Setzt die Firmware (bzw. das Betriebssystemimage) des physischen Geräts auf ein ab Werk bereitgestelltes Sicherungsimage zurück, das auf dem Gerät gespeichert ist.
- **Konfigurationsaktualisierung**: Konfiguriert den IoT Hub-Client-Agent, der auf dem physischen Gerät ausgeführt wird.
- **Geräteeigenschaft lesen**: Dient zum Abrufen des aktuellsten Werts einer Geräteeigenschaft auf dem physischen Gerät.
- **Geräteeigenschaft schreiben**: Ändert eine Geräteeigenschaft auf dem physischen Gerät.

Weitere Informationen zur Verwendung der einzelnen Aufträge finden Sie in der [API-Dokumentation][lnk-apidocs].

Sie können den Auftragsverlauf abfragen, um den Status von Aufträgen zu erkennen, die Sie gestartet haben. Einige Beispiele für Abfragen finden Sie in [unserer Bibliothek mit Abfrageausdrücken][lnk-query-samples].

## Verwenden von Geräteaufträgen zum Ausführen von Firmwareupdates: Architektur

Das folgende Diagramm zeigt eine Interaktion eines Firmwareupdate-Geräteauftrags mit einem einzigen physischen Gerät.

![][img-architecture]

Schritte für den Firmwareupdate-Geräteauftrag:

1.  Die cloudbasierte IoT-Lösung startet den Firmwareupdate-Geräteauftrag und stellt den URI für den Speicherort des Firmwarepakets bereit. Es liegt in der Verantwortung der IoT-Lösung, das Firmwarepaket an einem Speicherort zu platzieren, von dem Sie das Gerät herunterladen können.

2.  Wenn das physische Gerät diesen URI empfängt, beginnt es automatisch, vom angegebenen URI aus herunterzuladen.

3.  Der Code auf dem Gerät empfängt die Anforderung vom IoT Hub und lädt das Firmwarepaket aus dem angegebenen Speicherort-URI.

4.  Nachdem der Geräteauftrag die Gerätestatusnachricht über den abgeschlossenen Download erhalten hat, weist er das Gerät an, das heruntergeladene Firmwareimage anzuwenden.

5.  Nachdem das Gerät das Firmwareimage angewendet hat, startet es neu, verbindet sich erneut mit IoT Hub und teilt IoT Hub mit, dass die neue Firmware angewendet wurde und der Geräteauftrag nun abgeschlossen ist.

## Ausführen des Beispiels des Firmwareupdates

Das folgende Beispiel erweitert die Funktionalität des Tutorials [Erste Schritte mit der Azure IoT Hub-Geräteverwaltung mithilfe von C# (Vorschau)][lnk-get-started]. Auf der Basis, dass die verschiedenen simulierten Geräte ausgeführt werden, wird ein Auftrag zum Aktualisieren der Firmware auf allen Geräten gestartet.

### Voraussetzungen 

Vor dem Ausführen dieses Beispiels müssen Sie die Schritte in [Erste Schritte mit der Azure IoT Hub-Geräteverwaltung mithilfe von C# (Vorschau)][lnk-get-started] abgeschlossen haben. Das bedeutet, dass Ihre simulierten Geräte ausgeführt werden müssen. Wenn Sie den Vorgang zuvor abgeschlossen haben, starten Sie Ihre simulierten Geräte jetzt neu.

### Starten des Beispiels

Um das Beispiel zu starten, müssen Sie `jobClient_scheduleJob.js` ausführen. Damit wird die Aktualisierung der Firmware auf allen simulierten Geräten gestartet. Folgen Sie den Schritten unten, um das Beispiel zu starten:

1.  Navigieren Sie aus dem Stammordner, in dem Sie das Repository **azure-iot-sdks** geklont haben, zum Verzeichnis **azure-iot-sdks/node/service/samples**.  

2.  Öffnen Sie **jobClient\_scheduleJob.js**, und ersetzen Sie den Platzhalter durch Ihre IoT Hub-Verbindungszeichenfolge.

4.  Führen Sie `node jobClient_scheduleJob.js` aus.

Im Befehlszeilenfenster sollten sieben Geräteaufträge angezeigt werden, die das simulierte Firmwareupdate für die sechs simulierten Geräte nachverfolgen.

### Starten eines Geräteauftrags

Im Allgemeinen werden Geräteaufträge mit einer Reihe von **schedule&lt;JobName&gt;**-Methoden auf der **JobClient**-Instanz gestartet. Im Firmwareupdatebeispiel rufen wir die **scheduleFirmwareUpdate**-Methode auf. Da wir ein Array mit 6 Geräte-IDs übergeben, werden 7 Geräteaufträge gestartet, einer für jedes Gerät, das aktualisiert wird, und einer für ein übergeordnetes Gerät, das zum Nachverfolgen der anderen 6 verwendet wird.

Im folgenden Codeausschnitt wird ein Firmwareupdateauftrag gestartet. Der Aufruf verwendet ein Zeichenfolgenarray von **deviceId**-Werten als Parameter, die die Geräte darstellen, die wir aktualisieren möchten.

```
jobClient.scheduleFirmwareUpdate(jobId, deviceIds, packageURI, timeoutInMinutes, callback);
```

### Details zur Gerätesimulatorimplementierung

In den vorherigen Abschnitten zeigten wir die Details der Implementierung des Firmwareupdates vom Dienststandpunkt aus (Starten eines Geräteauftrags und Abfragen seines Status). Nun erörtern wir die entsprechende Implementierung von der Geräteseite aus betrachtet.

Die Clientbibliothek der Azure IoT Hub-Geräteverwaltung übernimmt die Kommunikation zwischen dem Gerät und dem Dienst, sodass nur noch die Implementierung der gerätespezifischen Logik durchgeführt werden muss. Diese besteht aus zwei Teilen:

- Implementieren des gerätespezifischen Firmwareupdateprozesses: Umfasst das Schreiben der gerätespezifischen Logik, um das Firmwarepaket herunterzuladen, und Anwenden des Updates in den entsprechenden unten aufgeführten Rückrufen. Im simulierten Beispiel ist dies im [Beispiel][lnk-github-firmware] implementiert:

  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```

- Informieren Sie den Dienst des Firmwareupdateprozesses: Dies schließt die Änderung des Firmwareupdatestatus und der Firmwareupdate-Ergebnisfelder ein. Sie erreichen dies durch Aufrufen der **set\_firmwareupdate\_state**- und **set\_firmwareupdate\_updateresult**-API. Im simulierten Beispiel ist dies im [Beispiel][lnk-github-firmware] implementiert:

## Nächste Schritte

Weitere Informationen zu den Geräteverwaltungsfeatures von Azure IoT Hub erhalten Sie in den folgenden Tutorials:

- Die Clientbibliothek der Azure IoT Hub-Geräteverwaltung bietet ein End-to-End-Beispiel mit einem [Intel Edison-Gerät][lnk-edison].

- [Verwenden des Gerätezwillings][lnk-twin-tutorial]

- [Suchen nach Gerätezwillingen mithilfe von Abfragen][lnk-tutorial-queries]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0518_2016-->