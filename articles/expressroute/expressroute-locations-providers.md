---
title: 'Localizações e fornecedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure. Ordenado por localização.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/15/2020
ms.author: cherylmc
ms.openlocfilehash: d6dba6f68238327c1c5c51c75cc5a65ceec2838f
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942380"
---
# <a name="expressroute-partners-and-peering-locations"></a>Parceiros e localizações de peering do ExpressRoute

> [!div class="op_single_selector"]
> * [Localizações por Fornecedor](expressroute-locations.md)
> * [Fornecedores por Localização](expressroute-locations-providers.md)


As tabelas deste artigo fornecem informações sobre cobertura geográfica expressRoute e localizações, fornecedores de conectividade ExpressRoute e Integradores do Sistema ExpressRoute (SIs).

> [!Note]
> As regiões azure e as localizações ExpressRoute são dois conceitos distintos e diferentes, compreender a diferença entre as duas é fundamental para explorar a conectividade híbrida azure em rede. 
>
>

## <a name="azure-regions"></a>Regiões do Azure
As regiões de Azure são centros de dados globais onde estão localizados os recursos de computação, networking e armazenamento do Azure. Ao criar um recurso Azure, um cliente precisa selecionar uma localização de recursos. A localização do recurso determina em que centro de dados Azure (ou zona de disponibilidade) o recurso é criado.

## <a name="expressroute-locations"></a>Localizações do ExpressRoute
As localizações expressRoute (por vezes referidas como locais de observação ou locais de encontro- me)são instalações de co-localização onde estão localizados dispositivos de borda da Microsoft Enterprise (MSEE). As localizações expressRoute são o ponto de entrada para a rede da Microsoft – e estão distribuídas globalmente, proporcionando aos clientes a oportunidade de se conectarem à rede da Microsoft em todo o mundo. Estas localizações são onde os parceiros ExpressRoute e os clientes ExpressRoute Direct emitem ligações cruzadas à rede da Microsoft. Em geral, a localização ExpressRoute não precisa de corresponder à região de Azure. Por exemplo, um cliente pode criar um circuito ExpressRoute com a localização de recursos *East US,* na localização *Seattle* Peering.

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica. 

## <a name="locations"></a>Regiões de Azure para localizações ExpressRoute dentro de uma região geopolítica
A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Governo da Austrália** | Austrália Central, Austrália Central 2 |Camberra, Camberra2 |
| **Europa** | France Central, França Sul, Alemanha Norte, Alemanha West Central, Norte Europa, Noruega Leste, Noruega Oeste, Suíça Norte, Suíça Oeste, Reino Unido Oeste, Reino Unido Sul, Europa Ocidental |Amesterdão, Amesterdão2, Copenhague, Dublin, Frankfurt, Genebra, Londres, Londres2, Marselha, Milão, Munique, Newport (País de Gales), Oslo, Paris, Stavanger, Estocolmo, Zurique, Munique |
| **América do Norte** | E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Leste do Canadá |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova Iorque, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Ásia** | Ásia Leste, Ásia Sudeste | Banguecoque, Hong Kong, Hong Kong2, Jacarta, Kuala Lumpur, Singapura, Singapura2, Taipei |
| **Índia** | Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | Oeste do Japão, Leste do Japão |Osaka, Tóquio, Tóquio2 |
| **Oceânia** | Austrália Sudeste, Leste da Austrália |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Coreia do Sul** | Coreia do Sul Central, Sul da Coreia do Sul |Busan, Seul|
| **Emirados Sea** | UAE Central, Eau Norte | Dubai |
| **África do Sul** | África do Sul Oeste, África do Sul Norte |Cidade do Cabo, Joanesburgo |
| **América do Sul** | Sul do Brasil |São Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões azuis e fronteiras geopolíticas para nuvens nacionais
A tabela abaixo fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Nuvem para a Administração Pública dos EUA** |US Gov - Arizona, US Gov - Iowa, US Gov - Texas, US Gov - Virginia, US DoD - Centro, US DoD - Leste  |Atlanta, Chicago, Dallas, Nova Iorque, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Leste da China** |Leste da China, Leste da China 2 |Xangai |
| **Norte da China** |Norte da China, Norte da China 2 |Pequim |
| **Alemanha** |Alemanha Central, Leste da Alemanha |Berlim, Frankfurt |

A conectividade em regiões geopolíticas não é suportada no SKU do ExpressRoute standard. Terá de ativar o suplemento ExpressRoute Premium para suportar a conetividade global. A conectividade com ambientes em nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade, se tal for necessário.

## <a name="partners"></a>Fornecedores de conectividade do ExpressRoute

A tabela seguinte mostra as localizações de conectividade e os fornecedores de serviços para cada localização. Se pretender ver os fornecedores de serviços e as localizações para as quais podem fornecer serviços, veja [Localizações por fornecedor de serviços](expressroute-locations.md).

* **Regiões locais** do Azure são as que o [ExpressRoute Local](expressroute-faqs.md) em cada local de observação pode aceder. **n/a** indica que o ExpressRoute Local não está disponível nesse local de observação.

* **A zona** refere-se aos [preços.](https://azure.microsoft.com/pricing/details/expressroute/)


### <a name="global-commercial-azure"></a>Azure comercial global
| **Localização** | **Endereço** | **Zona** | **Regiões locais de Azure** | **ER Direto** | **Fornecedores de serviços** |
| --- | --- | --- | --- | --- | --- |
| **Amesterdão** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa Ocidental | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Rio Zayo |
| **Amesterdão2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa Ocidental | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/d | n/d | Equinix, Megaport |
| **Auckland** | [Grupo Vocus NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/d | 10G | Devoli, Kordia, Megaport, Spark NZ, Grupo Vocus NZ |
| **Banguecoque** | [AIS](http://business.ais.co.th/solution/microsoft-azure.html?category=cloud) | 2 | n/d | 10G | AIS |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Coreia do Sul | n/d | LG CNS |
| **Camberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central | 10G, 100G | CDC |
| **Camberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central 2| 10G, 100G | CDC |
| **Cidade do Cabo** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | África do Sul Ocidental | 10G | BCX, Soluções de Internet - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Sul da Índia | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Sul da Índia | n/d | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | EUA Centro-Norte | 10G, 100G | Redes Aryaka, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenhague** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/d | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/d | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | EUA Centro-Oeste | n/d | CoreSite, Megaport, Zayo |
| **Rio Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Emirados Unidos norte | n/d | Emirados Emirados Emirados Unidos |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Emirados Unidos norte | n/d | du datamena, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa do Norte | 10G, 100G | Colt, eir, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Alemanha West Central | 10G, 100G | Colt, DE-CIX, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Genebra** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Suíça Oeste | 10G, 100G | Equinix, Megaport |
| **RAE de Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Ásia Oriental | n/d | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hong Kong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | n/d | 10G | |
| **Jacarta** | Telkom Indonésia | 4 | n/d | 10G | |
| **Joanesburgo** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | África do Sul Norte | 10G | British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/d | n/d | TIME dotCom |
| **Las Vegas** | [Switch LV](https://www.switch.com/las-vegas) | 1 | n/d | n/d | CenturyLink Cloud Connect, Megaport |
| **Londres** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Reino Unido Sul | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Londres2** | [Telehouse Norte Dois](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Reino Unido Sul | 10G, 100G | CenturyLink Cloud Connect, Colt, IX Reach, Equinix, Megaport, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/d | n/d | CoreSite, Equinix, Megaport, Redes Neutrona, NTT, Transtelco, Zayo |
| **Marselha** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Sul de França | n/d | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Sudeste da Austrália | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/d | 10G | C3ntro, Equinix, Megaport, Redes Neutrona |
| **Milão** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/d | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/d | 10G, 100G | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Oeste da Índia | n/d | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Oeste da Índia | n/d | Airtel, Sify, Vodafone Idea |
| **Munique** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/d | 10G, 100G | |
| **Nova Iorque** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/d | n/d | CenturyLink Cloud Connect, Colt, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (País de Gales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Reino Unido Oeste | n/d | British Telecom, Colt, Comunicações de Nível 3, Dados da Próxima Geração |
| **Osaca** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Oeste do Japão | 10G, 100G | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Rio Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Noruega Leste | 10G, 100G | Megaport, Telenor, Telia Carrier |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | França Central | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Rio Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/d | 10G | Megaport |
| **Cidade do Quebeque** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Leste do Canadá | n/d | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | EUA Centro-Sul | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **São Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Sul do Brasil | n/d | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | EUA Oeste 2 | 10G, 100G | Redes Aryaka, Equinix, Comunicações de Nível 3, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Coreia do Sul Central | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | EUA Oeste | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | EUA Oeste | 10G, 100G | Colt | 
| **Singapura** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Sudeste Asiático | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon |
| **Singapura2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Sudeste Asiático | 10G, 100G | China Unicom Global, Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Montanha Verde DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Noroeste da Noruega | 10G, 100G | |
| **Estocolmo** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/d | 10G | Equinix, Transportadora de Telia |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Leste da Austrália | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Leste da Austrália | 10G, 100G | Megaport |
| **Taipé** | Chefe de Telecomunicações | 2 | n/d | 10G | Chief Telecom |
| **Tóquio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Leste do Japão | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tóquio2** | [Em Tóquio](https://www.attokyo.com/) | 2 | Leste do Japão | 10G, 100G | |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canadá Central | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Rio Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n/d | 10G, 100G | |
| **Washington D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Leste dos EUA, Leste DOS EUA 2 | 10G, 100G | Redes Aryaka, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Leste dos EUA, Leste DOS EUA 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **Zurique** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Suíça Norte | 10G, 100G | Intercloud, Interxion, Megaport, Swisscom |

 **+** brevemente disponível

### <a name="national-cloud-environments"></a>Ambientes em nuvem nacionais

As nuvens nacionais azure estão isoladas umas das outras e do azure comercial global. A Rota Expressa para uma nuvem Azure não pode ligar-se às regiões de Azure nas outras.

### <a name="us-government-cloud"></a>Nuvem do US Gov
| **Localização** | **Endereço** | **Regiões locais de Azure**| **ER Direto** | **Fornecedores de serviços** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/d | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/d | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/d | 10G, 100G | Equinix, Megaport, Verizon |
| **Nova Iorque** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/d | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | Gov (US) - Arizona | n/d | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | Gov (US) - Texas | n/d | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/d | 10G, 100G | AT&T, Equinix, Nível 3 Comunicações, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/d | n/d | Equinix, Megaport |
| **Washington D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD East, US Gov Virginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Localização** | **Fornecedores de serviços** |
| --- | --- |
| **Pequim** |China Telecom |
| **Pequim2** | China Telecom, China Unicom, GDS |
| **Xangai** |China Telecom |
| **Xangai2** | China Telecom, GDS |

Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Alemanha
| **Localização** | **Fornecedores de serviços** |
| --- | --- |
| **Berlim** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Conectividade através de fornecedores de intercâmbio
Se o seu fornecedor de conectividade não está listado nas secções anteriores, pode criar na mesma uma ligação.

* Consulte o seu fornecedor de conectividade para saber se este está ligado a alguma das trocas na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizados pelos fornecedores de troca. Vários fornecedores de conectividade já estão ligados às trocas de Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Rio Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Solicite ao seu fornecedor de conectividade para expandir a sua rede para a localização de peering que pretende.
  * Certifique-se de que o fornecedor de conectividade expande a sua conectividade para um estado de elevada disponibilidade, de forma a não existirem pontos únicos de falha.
* Organize um circuito ExpressRoute com a troca como o seu fornecedor de conectividade para se ligar à Microsoft.
  * Siga os passos em [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

## <a name="connectivity-through-satellite-operators"></a>Conectividade através de operadores de satélite
Se você é remoto e não tem conectividade de fibra ou você quer explorar outras opções de conectividade você pode verificar os seguintes operadores de satélite. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Conectividade através de prestadores de serviços adicionais
| **Localização** | **Exchange** | **Fornecedores de conectividade** |
| --- | --- | --- |
| **Amesterdão** | Equinix, Interxion, Comunicações de Nível 3 | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castelo da Coroa
| **Cidade do Cabo** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburgo** | Equinix | Rio Cinia |
| **RaS, Hong Kong** | Equinix | Chefe, Macroview Telecom |
| **Joanesburgo** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **Nova Iorque** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Cidade do Quebeque** | Megaport | Fibrenoire |
| **São Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapura** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tóquio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington D.C.** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Integradores de sistemas ExpressRoute
A ativação da conectividade privada para atender as suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um dos integradores de sistemas listados na seguinte tabela para ajudá-lo com a integração do ExpressRoute.

| **Continente** | **Integradores de sistemas** |
| --- | --- |
| **Ásia** |Avanade Inc., OneAs1a |
| **Austrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **América do Norte** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **América do Sul** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
