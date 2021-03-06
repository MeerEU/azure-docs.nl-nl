---
title: "Netwerk-Integratieoverwegingen voor het Azure-Stack geïntegreerd systemen | Microsoft Docs"
description: Meer informatie over wat u kunt doen om te plannen voor netwerkintegratie datacenter met meerdere knooppunten Azure Stack.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 43d79a8c14751ee25eaca7a3b50649702d159d76
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2018
---
## <a name="network-connectivity"></a>Netwerkverbinding
Dit artikel bevat informatie over de infrastructuur Azure netwerkstack kunt u het beste Azure Stack integreren in uw bestaande netwerkomgeving bepalen. 

> [!NOTE]
> Om op te lossen externe DNS-namen uit Azure-Stack (bijvoorbeeld www.bing.com), moet u DNS-servers voor het doorsturen van DNS-aanvragen te bieden. Zie voor meer informatie over de vereisten voor Azure Stack DNS [Stack Azure datacenter integratie - DNS-](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fysieke netwerkontwerp
De Azure-Stack-oplossing vereist een robuuste en maximaal beschikbare fysieke infrastructuur ter ondersteuning van de toepassing en services. Het volgende diagram geeft aan onze aanbevolen ontwerp:

![Aanbevolen ontwerp voor Azure-Stack-netwerk](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logische netwerken
Logische netwerken vertegenwoordigen een abstractie van de onderliggende fysieke netwerkinfrastructuur. Ze worden gebruikt voor het ordenen en vereenvoudigen Netwerktoewijzingen voor hosts, virtuele machines en services. Als onderdeel van het maken van logische netwerken, netwerksites gemaakt voor het definiëren van de virtuele LAN (VLAN's), IP-subnetten en IP-subnet/VLAN-paren die gekoppeld aan het logische netwerk op elke fysieke locatie zijn.

De volgende tabel ziet u de logische netwerken en bijbehorende IPv4-subnet bereiken die u moet plannen:

| Logisch netwerk | Beschrijving | Grootte | 
| -------- | ------------- | ------------ | 
| Openbare VIP | Openbare IP-adressen voor een kleine set van Stack Azure-services, met de rest gebruikt door virtuele machines van tenants. 32-adressen binnen dit netwerk maakt gebruik van de Azure-Stack-infrastructuur. Als u van plan bent gebruik van App Service en de SQL-resourceproviders, worden 7 meer adressen gebruikt. | / 26 (62 hosts) - /22 (1022 hosts)<br><br>Aanbevolen = /24 (254 hosts) | 
| Switch-infrastructuur | Point-to-Point IP-adressen voor de routering, toegewezen switch beheerinterfaces en loopback-adressen toegewezen aan de switch. | /26 | 
| Infrastructuur | Gebruikt voor interne Azure Stack-onderdelen communiceren. | /24 |
| Privé | Gebruikt voor het opslagnetwerk en persoonlijke VIP's. | /24 | 
| BMC | Gebruikt voor communicatie met de BMC's op de fysieke hosts. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Netwerkinfrastructuur
De netwerkinfrastructuur voor Azure-Stack bestaat uit verschillende logische netwerken die zijn geconfigureerd op de switches. Het volgende diagram toont deze logische netwerken, en hoe ze integreren met de top-of-rack (TOR), BMC (baseboard management controller) en (klant) netwerkswitches rand.

![Logisch netwerk en de switch-verbindingen](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-netwerk
Dit netwerk is op het verbinden van alle de baseboard management controllers (ook wel bekend als serviceprocessors, bijvoorbeeld iDRAC, iLO, iBMC, enz.) toegewezen aan het beheernetwerk. Indien aanwezig, wordt de host (HLH) hardware lifecycle bevindt zich op dit netwerk en OEM-specifieke software voor onderhoud van hardware-en/of bewaking kan bieden. 

### <a name="private-network"></a>Privénetwerk
Deze /24 (254 host IP van) netwerk is gebonden aan de Stack van Azure-regio (wordt niet uitgebreid dan de apparaten van de switch rand van de Stack van Azure-regio) en is onderverdeeld in twee subnetten:

- **Opslagnetwerk**. Livemigratie van een /25 126 host IP van) netwerk (gebruikt ter ondersteuning van het gebruik van opslagruimten Direct en Server Message Block (SMB) opslagverkeer en de virtuele machine. 
- **Interne virtuele IP-netwerk**. A/25 netwerk gereserveerd voor intern gebruik bestemde VIP's voor de software load balancer.

### <a name="azure-stack-infrastructure-network"></a>Netwerk van Azure Stack-infrastructuur
Dit/24 netwerk is toegewezen aan interne Azure Stack-onderdelen zodat ze kunnen communiceren en uitwisselen van gegevens onderling. Dit subnet routeerbare IP-adressen vereist, maar worden privé gehouden met de oplossing met behulp van toegangsbeheerlijsten (ACL's), dit wordt niet verwacht te routeren afgezien van de rand switches, met uitzondering van een zeer kleine bereik overeen in grootte met een/27 netwerk door enkele van deze gebruikt wanneer ze nodig toegang tot externe bronnen en/of de internet hebben-Services. 

### <a name="public-infrastructure-network"></a>Infrastructuur voor openbare netwerk
Dit/27 netwerk is het zeer kleine bereik van het Azure-Stack subnet eerder vermeld, vereist geen openbare IP-adressen, maar hoeft toegang tot internet via een NAT of een transparentproxy. Dit netwerk voor de Emergency Recovery-Console (ERCS) worden toegewezen, de ERCS VM is internettoegang vereist tijdens de registratie in Azure en moet routeerbaar zijn met uw beheernetwerk voor het oplossen van problemen.

### <a name="public-vip-network"></a>Openbare VIP-netwerk
Het openbare VIP-netwerk is toegewezen aan de netwerkcontroller in Azure-Stack. Het is niet een logisch netwerk op de switch. De SLB maakt gebruik van de groep met adressen en wijst/32 voor bedrijfstoepassingen huurder netwerken. Deze 32 IP-adressen zijn op de switch-routeringstabel geadverteerd als een beschikbare route via BGP. Dit netwerk bevat de externe toegankelijk of openbare IP-adressen. De infrastructuur van Azure-Stack maakt gebruik van ten minste 8 adressen van deze openbare VIP-netwerk terwijl de rest wordt gebruikt door de tenant-VM's. De netwerkgrootte op dit subnet kan variëren van een minimum van /26 (64-hosts) tot maximaal /22 (1022 hosts), is het raadzaam dat u van plan bent voor een/24 netwerk.

### <a name="switch-infrastructure-network"></a>Switch-infrastructuurnetwerk
Dit/26 netwerk is het subnet met subnetten van het point-to-point routeerbare IP-/ 30 (2 host IP van) en de loopbacks die zijn toegewezen/32 subnetten voor in-band overschakelen beheer- en BGP-router-ID. Dit bereik van IP-adressen moeten routeerbaar extern van de Azure-Stack-oplossing voor uw datacenter, ze kunnen persoonlijke of openbare IP-adressen zijn.

### <a name="switch-management-network"></a>Switch-management-netwerk
Deze slechts/29 (6 host IP-adressen) netwerk is toegewezen aan de poorten van de schakelopties verbinding te maken. Het biedt out-of-band-toegang voor implementatie, beheer en probleemoplossing. Het is berekend op basis van de hierboven genoemde infrastructuurnetwerk in de switch.

## <a name="publish-azure-stack-services"></a>Azure-Stack services publiceren
U moet Azure Stack services beschikbaar stellen aan gebruikers van buiten Azure-Stack. Azure Stack stelt u verschillende eindpunten voor de functies van de infrastructuur. Deze eindpunten toegewezen VIP's uit de openbare IP-adresgroep. Een DNS-vermelding wordt voor elk eindpunt voor de externe DNS-zone, is opgegeven tijdens de implementatie gemaakt. De gebruikersportal is bijvoorbeeld toegewezen dat de host DNS-vermelding van de portal.  *&lt;regio >.&lt; FQDN-naam >*.

### <a name="ports-and-urls"></a>Poorten en URL 's
Om ervoor te Stack Azure-services (zoals de portals Azure Resource Manager, DNS, etc.) beschikbaar met externe netwerken, moet u binnenkomend verkeer op deze eindpunten toestaan voor specifieke URL's, poorten en protocollen.
 
In een implementatie waarbij een transparentproxy uplinkpoortprofiel met een traditioneel proxyserver, moet u toestaan bepaalde poorten en URL's voor uitgaande communicatie. Deze omvatten poorten en URL's voor identiteit, marketplace syndication, patch en update, registratie en gebruiksgegevens.

Zie voor meer informatie:
- [Poorten voor inkomend verkeer en protocollen](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Uitgaande poorten en URL 's](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)


## <a name="next-steps"></a>Volgende stappen
[Azure Stack rand connectiviteit](azure-stack-border-connectivity.md)