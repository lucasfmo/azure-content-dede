<properties 
	pageTitle="Media Encoder Standard-Formate und -Codecs" 
	description="Dieses Thema bietet eine Übersicht über Media Encoder Standard-Formate und -Codecs." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016"
	ms.author="juliako;anilmur"/>

#Media Encoder Standard-Formate und -Codecs


Dieses Dokument enthält eine Liste der gängigsten Import- und Exportdateiformate, die Sie mit Media Encoder Standard verwenden können.


##Eingabecontainer/Dateiformate

Dateiformate (Dateierweiterungen)|Unterstützt
---|---|---|---
FLV (mit H.264- und AAC-Codecs) (.flv) |Ja 
MXF (.mxf) |Ja 
GXF (.gxf) |Ja 
MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Ja 
Windows Media Video (WMV)/ASF (.wmv, .asf) |Ja 
AVI (unkomprimiert, 8-Bit/10-Bit) (.avi)|Ja 
MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv)|Ja 
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Ja 
Matroska/WebM (.mkv) |Ja 
WAVE/WAV (.wav) |Ja 
QuickTime (.mov) |Ja
 
###Audioformate in Eingabecontainern 

Media Encoder Standard unterstützt das Befördern der folgenden Audioformate in Eingabecontainern:

- MXF-, GXF- und QuickTime-Dateien, die Audiospuren mit verschachteltem Stereo- oder 5.1-Beispielen haben.

oder

- MXF-, GXF- und QuickTime-Dateien, in denen die Audiospuren als separate PCM-Tracks befördert, die Kanalzuordnung jedoch (für Stereo oder 5.1) aus den Dateimetadaten abgeleitet werden kann.

Beachten Sie, dass der Support für die explizite oder vom Benutzer angegebene Kanalzuordnung in naher Zukunft bereitgestellt wird.


##Codecs für Videoeingang

Codecs für Videoeingang|Unterstützt
---|---|---|---
AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra |8-Bit 4:2:0 und 4:2:2 
Avid DNxHD (in MXF) |Ja 
DVCPro/DVCProHD (in MXF) |Ja 
Digitales Video (DV) (in AVI-Dateien) |Ja
JPEG 2000 |Ja 
MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)|Bis zu 422 Profile 
MPEG-1 |Ja 
VC-1/WMV9 |Ja 
Canopus HQ/HQX |Nein 
MPEG-4 Teil 2 |Ja 
[Theora](https://en.wikipedia.org/wiki/Theora) |Ja 
YUV420, nicht komprimiert oder Mezzanine |Ja
Apple ProRes 422 |Ja
Apple ProRes 422 LT |Ja
Apple ProRes 422 HQ |Ja
Apple ProRes Proxy|Ja
Apple ProRes 4444 |Ja
Apple ProRes 4444 XQ |Ja



##Codecs für Audioeingang

Codecs für Audioeingang|Unterstützt
---|---|---|---
AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)|Ja 
MPEG Layer 2|Ja 
MP3 (MPEG-1 Audio Layer 3)|Ja 
Windows Media Audio|Ja 
WAV/PCM|Ja 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Ja 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Ja 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Ja 
AMR (Adaptive Multi-Rate)|Ja
AES (SMPTE 331M und 302M, AES3-2003) |Nein 
Dolby® E |Nein 
Dolby® Digital (AC3) |Nein 
Dolby® Digital Plus (E-AC3) |Nein 


##Ausgabeformate und Codecs

In der folgenden Tabelle werden die Codecs und Dateiformate aufgeführt, die für den Export unterstützt werden.


Dateiformat|Videocodec|Audiocodec
---|---|---
MP4 <br/><br/>(einschließlich Multi-Bitrate-MP4-Container) |H.264 (High, Main und Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (High, Main und Baseline Profile)|AAC-LC, HE-AAC v1, HE-AAC v2 



##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Weitere Informationen

[Codieren von On-Demand-Inhalten mit Azure Media Services](media-services-encode-asset.md)

[Gewusst wie: Codieren mit Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=AcomDC_0420_2016-->