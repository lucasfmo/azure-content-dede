<properties 
   pageTitle="Mit PowerShell im Ressourcen-Manager Ihre Reverse-DNS-Einträge für Ihre Dienste verwalten | Microsoft Azure"
   description="Mit PowerShell im Ressourcen-Manager Reverse-DNS-Einträge oder PTR-Einträge für Azure verwalten"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# Mit PowerShell Ihre Reverse-DNS-Einträge für Ihre Dienste verwalten

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md)

## Überprüfung der Reverse-DNS-Einträge 
Um sicherzustellen, dass kein Dritter Reverse-DNS-Einträge erstellen kann, die Ihren Domänen zugeordnet sind, erlaubt Azure die Erstellung von Reverse-DNS-Einträgen nur, wenn Folgendes zutrifft:

- Der ReverseFqdn ist der gleiche wie der vollqualifizierte Domänenname (Fqdn) der öffentlichen IP-Adressressource, für den er angegeben wurde. Oder er entspricht dem „Fqdn“ jeder öffentlichen IP-Adresse innerhalb desselben Abonnements, lautet also beispielsweise „contosoapp1.northus.cloudapp.azure.com.“.

- Der ReverseFqdn löst sich vorwärts in den Namen oder die IP-Adresse derjenigen öffentlichen IP-Adresse auf, für die er angegeben wurde, oder in eine beliebige öffentliche IP-Adresse „Fqdn“, oder in eine IP innerhalb des gleichen Abonnements. Beispielsweise, wenn ReverseFqdn dem Namen „app1.contoso.com.“ entspricht, was ein CName alias für „contosoapp1.northus.cloudapp.azure.com“ ist.

Überprüfungen werden nur durchgeführt, wenn die Reverse-DNS-Eigenschaft für eine öffentliche IP-Adresse eingerichtet oder geändert wird. Es finden keine regelmäßigen Neuüberprüfungen statt.

## Hinzufügen von Reverse-DNS zu vorhandenen öffentlichen IP-Adressen
Sie können Reverse-DNS mit dem Cmdlet „Set-AzureRmPublicIpAddress“ einer vorhandenen öffentlichen IP-Adresse hinzufügen:

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip
	
Wenn Sie Reverse-DNS einer vorhandenen öffentlichen IP-Adresse hinzufügen möchten, die noch keinen DNS-Namen besitzt, müssen Sie auch einen DNS-Namen angeben. Hierzu können Sie das Cmdlet „Set-AzureRmPublicIpAddress“ verwenden:

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings
	PS C:\> $pip.DnsSettings.DomainNameLabel = "contosoapp1"
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

## Erstellen einer öffentlichen IP-Adresse mit Reverse-DNS
Sie können eine neue öffentliche IP-Adresse mit der angegebenen Reverse-DNS-Eigenschaft hinzufügen, indem Sie das Cmdlet „New-AzureRmPublicIpAddress“ verwenden:

	PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
 
## Anzeigen von Reverse-DNS für vorhandene öffentliche IP-Adressen
Mithilfe des Cmdlets „Get-AzureRmPublicIpAddress“ können Sie den konfigurierten Wert für eine vorhandene öffentliche IP-Adresse anzeigen:

	PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS 

## Entfernen Sie eine Reverse-DNS aus vorhandenen öffentlichen IP-Adressen.
Mithilfe des Cmdlets Set-AzureRmPublicIpAddress können Sie eine Reverse-DNS-Eigenschaft aus einer öffentlichen IP-Adresse entfernen. Dies erfolgt, indem der ReverseFqdn-Eigenschaftswert auf „leer“ gesetzt wird:

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = ""
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip 

[AZURE.INCLUDE [HÄUFIG GESTELLTE FRAGEN](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!-----HONumber=AcomDC_0316_2016-->