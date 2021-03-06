<properties 
	pageTitle="Media Encoder Premium Workflow-Formate und -Codecs" 
	description="Dieses Thema bietet eine Übersicht über Media Encoder Premium Workflow-Formate und -Codecs" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erik43" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="04/18/2016"    
	ms.author="juliako;anilmur"/>

#Media Encoder Premium Workflow-Formate und -Codecs


>[AZURE.NOTE]Fragen zu Encoder Premium senden Sie per E-Mail an „mepd at Microsoft.com“.
>
>Der in diesem Thema beschriebene Media Encoder Premium Workflow-Medienprozessor ist in China nicht verfügbar.

Dieses Dokument enthält eine Liste von Eingabe- und Ausgabedateiformaten sowie Codecs, die von der öffentlichen Vorschauversion des **Medienencoder-Premium-Workflow**-Encoders unterstützt werden.

[Media Encoder Premium Workflow-Eingabeformate und -Codecs](#input_formats)

[Media Encoder Premium Workflow-Ausgabeformate und -Codecs](#output_formats)

**Medienencoder-Premium-Workflow** unterstützt Untertitel, wie in [diesem](#closed_captioning) Abschnitt beschrieben.


##<a id="input_formats"></a>Medienencoder-Premium-Workflow-Eingabeformate und -Codecs

Im folgenden Abschnitt werden die Codecs und Formate aufgeführt, die dieser Medienprozessor als Eingabe unterstützt.

###Eingabecontainer/Dateiformate

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- MPEG-2-Transportdatenstrom
- MPEG-2-Programmdatenstrom
- MPEG-4/MP4
- Windows Media/ASF
- AVI (unkomprimiert, 8-Bit/10-Bit)

###Codecs für Videoeingang

- AVC 8-Bit/10-Bit, bis zu 4:2:2, einschließlich AVCIntra
- Avid DNxHD (in MXF)
- DVCPro/DVCProHD (in MXF)
- JPEG2000
- MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)
- MPEG-1
- Windows Media Video/VC-1

###Codecs für Audioeingang

- AES (SMPTE 331M und 302M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Medienencoder-Premium-Workflow-Ausgabeformate und -Codecs

Im folgenden Abschnitt werden die Codecs und Dateiformate aufgelistet, die als Ausgabe für diesen Medienprozessor unterstützt werden.

###Ausgabecontainer/Dateiformate

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM und AS02)
- DPP (einschließlich AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (unkomprimiert, 8-Bit/10-Bit)
- Smooth Streaming-Dateiformat (PIFF 1.3)
- MPEG-TS 


###Codecs für Videoausgang

- AVC (H.264; 8-Bit; bis High Profile, Level 5.2; 4K Ultra HD; AVC Intra)
- Avid DNxHD (in MXF)
- DVCPro/DVCProHD (in MXF)
- MPEG-2 (bis zu 422 Profile und High Level; Varianten wie XDCAM, XDCAM HD, XDCAM IMX, CableLabs® und D10 eingeschlossen)
- MPEG-1
- Windows Media Video/VC-1
- Erstellung von JPEG-Miniaturansichten

###Codecs für Audioausgabe

- AES (SMPTE 331M und 302M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) bis 7.1
- AAC (AAC-LC, AAC-HE und AAC-HEv2; bis 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Unterstützung für Untertitel

Bei der Erfassung unterstützt **Medienencoder-Premium-Workflow** Folgendes:

1. SCC-Dateien
1. SMPTE-TT-Dateien
1. CEA-608/CEA-708 – als Benutzerdaten (SEI-Nachrichten von H.264-Basisstreams, ATSC/53, SCTE20) oder als Übertragung als Zusatzdaten in MXF-/GXF-Dateien
1. SRT-Untertiteldateien

Bei der Ausgabe sind die folgenden Optionen verfügbar:

1. Übersetzung von CEA-608 nach CEA-708
1. CEA-608/CEA-708-Passthrough (eingebettet in SEI-Nachrichten von H.264-Basisstreams oder Übertragung als Zusatzdaten in MXF-Dateien)
1. SCC
1. SMPTE Timed Text (aus Quelle CEA-608 pro SMPTE RP2052; DFXP-Dateierstellung eingeschlossen)
1. SRT-Untertiteldatei
1. DVB-Untertitelstreams

Hinweis: Nicht alle der oben genannten Ausgabeformate werden zur Übermittlung per Streaming in Azure Media Services unterstützt.

##Bekannte Probleme

Wenn Ihr Eingabevideo keine Untertitel enthält, enthält das Ausgabemedienobjekt trotzdem eine leere TTML-Datei.


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0420_2016-->