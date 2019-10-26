---
title: 'Localizações e fornecedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure. Ordenado por localização.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: cherylmc
ms.openlocfilehash: 83ee20203cdd6a5afefd9d7e6e6e884fd142cf3c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901746"
---
# <a name="expressroute-partners-and-peering-locations"></a>Parceiros e localizações de peering do ExpressRoute

> [!div class="op_single_selector"]
> * [Localizações por Fornecedor](expressroute-locations.md)
> * [Fornecedores por Localização](expressroute-locations-providers.md)


As tabelas neste artigo fornecem informações sobre os locais e a cobertura geográfica do ExpressRoute, provedores de conectividade do ExpressRoute e SIs (integradores de sistema do ExpressRoute).

> [!Note]
> As regiões do Azure e locais de ExpressRoute são dois conceitos distintos e diferentes, entender a diferença entre os dois é essencial para explorar a conectividade de rede híbrida do Azure. 
>
>

## <a name="azure-regions"></a>Regiões do Azure
As regiões do Azure são data centers globais em que os recursos de computação, rede e armazenamento do Azure estão localizados. Ao criar um recurso do Azure, um cliente precisa selecionar um local de recurso. O local do recurso determina qual Datacenter do Azure (ou zona de disponibilidade) o recurso é criado.

## <a name="expressroute-locations"></a>Localizações do ExpressRoute
Locais de ExpressRoute (às vezes chamados de locais de emparelhamento ou de encontro-me – localizações) são instalações de colocalização em que os dispositivos Microsoft Enterprise Edge (MSEE) estão localizados. Os locais de ExpressRoute são o ponto de entrada para a rede da Microsoft – e são distribuídos globalmente, fornecendo aos clientes a oportunidade de se conectar à rede da Microsoft em todo o mundo. Esses locais são onde os parceiros do ExpressRoute e os clientes do ExpressRoute Direct emitem conexões cruzadas com a rede da Microsoft. Em geral, o local do ExpressRoute não precisa corresponder à região do Azure. Por exemplo, um cliente pode criar um circuito do ExpressRoute com o local do recurso *leste dos EUA*, no local de emparelhamento de *Seattle* .

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica. 

## <a name="locations"></a>Regiões do Azure para locais de ExpressRoute em uma região geopolítica
A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Governo da Austrália** | Austrália Central, Austrália Central 2 |Camberra, Camberra2 |
| **Europa** | França Central, Sul de França, Europa do Norte, Europa Ocidental, Oeste do Reino Unido, Sul do Reino Unido |Amsterdã, Amsterdam2, Copenhague, Dublin, Frankfurt, Londres, London2, Marselha, Newport (Gales), Paris, Estocolmo, Zurique, Munique |
| **América do Norte** | E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Canadá Leste |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova York, San Antonio, Seattle, vale do silício, silício Valley2, Washington DC, Washington DC2, Montreal, cidade de Quebec, Toronto |
| **Ásia** | Ásia Oriental, Sudeste Asiático |Rae de Hong Kong, Kuala Lumpur, Cingapura, Cingapura2, Taipé |
| **Índia** | Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Oceania** | Sudeste da Austrália, Leste da Austrália |Auckland, Melbourne, Perth, Sydney | 
| **Coreia do Sul** | Centro da Coreia, Sul da Coreia do Sul |Busan, Seul|
| **DOS EAU** | EAU Central, Norte dos EAU | Dubai, Dubai2 |
| **África do Sul** | Oeste da África do Sul, norte da África do Sul |Cidade do Cabo, Joanesburgo |
| **América do Sul** | Sul do Brasil |São Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões do Azure e limites de geopolítica para nuvens nacionais
A tabela abaixo fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Nuvem para a Administração Pública dos EUA** |US Gov Arizona, US Gov - Iowa, US Gov Texas, Gov (US) - Virginia, US DoD Central, US DoD East  |Chicago, Dallas, Nova Iorque, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Leste da China** |Leste da China, Leste da China 2 |Xangai, Shanghai2 |
| **Norte da China** |Norte da China, Norte da China 2 |Pequim, Beijing2 |
| **Alemanha** |Alemanha Central, Leste da Alemanha |Berlim, Frankfurt |

A conectividade em regiões geopolíticas não é suportada no SKU do ExpressRoute standard. Terá de ativar o suplemento ExpressRoute Premium para suportar a conetividade global. A conectividade com ambientes em nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade, se tal for necessário.

## <a name="partners"></a>Fornecedores de conectividade do ExpressRoute

A tabela seguinte mostra as localizações de conectividade e os fornecedores de serviços para cada localização. Se pretender ver os fornecedores de serviços e as localizações para as quais podem fornecer serviços, veja [Localizações por fornecedor de serviços](expressroute-locations.md).

* As **regiões locais do Azure** são aquelas que o [ExpressRoute local](expressroute-faqs.md) em cada local de emparelhamento pode acessar. **n/a** indica que o ExpressRoute local não está disponível nesse local de emparelhamento.

* **Zona** refere-se aos [preços](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="production-azure"></a>Azure de Produção
| **Localização** | **Corrigir** | **Zone** | **Regiões locais do Azure** | **ER direto** | **Fornecedores de serviços** |
| --- | --- | --- | --- | --- | --- |
| **Amesterdão** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa Ocidental | 10G, 100G | Redes aryaka Networks, em & T netbonde, British Telecom, Colt, Equinix, euNetworks, GÉANT, entre nuvens, Interxion, KPN, IX REACH, comunicações de nível 3, Megaport, comunicações de NTT, Orange, Tata Communications, Telefonica +, Telenor, Telia Carrier, Verizon, Zayo |
| **Amesterdão2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa Ocidental | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/d | n/d | Equinix, Megaport |
| **Auckland** | [Albany Vocus de NZ de grupo](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/d | Database | Devoli, Kordia, Megaport, Spark NZ, Vocus grupo NZ |
| **Busan** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | 2 | Sul da Coreia do Sul | n/d | LG CNS |
| **Camberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central | n/d | CDC |
| **Camberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Austrália Central 2| n/d | CDC |
| **Cidade do Cabo** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Oeste da África do Sul | Database | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Sul da Índia | n/d | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Sul da Índia | n/d | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | E.U.A. Centro-Norte | 10G, 100G | Redes aryaka networkss, em & T netbonde, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, entre Cloud, Internet2, comunicações de nível 3, Megaport, PacketFabric, PCCW global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenhague** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/d | Database | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/d | 10G, 100G | Redes aryaka Networks, em & T netbonde, Cologix, Equinix, Internet2, comunicações de nível 3, Megaport, Neutrona Networks, Telmex UniNet, Telia Carrier, transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | E.U.A. Centro-Oeste | n/d | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Norte dos E.A.U. | n/d | Etisalat dos EAU |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Norte dos E.A.U. | n/d | du datamena, Megaport, Orixcom |
| **Dublin** | [DB3 Equinix](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa do Norte | 10G, 100G | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Alemanha Oeste-Central | n/d | CIX, Interxion, laranja |
| **Estrutura** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Oeste da Suíça | 10G, 100G | |
| **Rae de Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Este Asiático | n/d | Redes de aryaka Networks, telecomunicações britânicas, CenturyLink Cloud Connect, diretor de telecomunicações, China Telecom global, Equinix, Megaport, comunicação de NTT, laranja, PCCW global Limited, Tata Communications, Telia Carrier, Verizon |
| **Joanesburgo** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Norte da África do Sul | n/d | British Telecom, soluções de Internet – conexão em nuvem, Liquid Telecom, laranja, teraco |
| **Kuala Lumpur** | [DotCom de tempo Menara objetivos](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/d | n/d | TIME dotCom |
| **Las Vegas** | [Switch LV](https://www.switch.com/las-vegas) | 1 | n/d | n/d | CenturyLink Cloud Connect, Megaport |
| **Londres** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Sul do Reino Unido | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse norte dois](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1 | Sul do Reino Unido | 10G, 100G | IX REACH, Equinix, The Telehouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/d | n/d | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, transtelco, Zayo |
| **Marselha** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Sul de França | n/d | Rede DE CIX, Interxion, Jaguar |
| **Melbourne** | [M1 NextDC](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Sudeste da Austrália | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/d | Database | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Milão** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/d | Database | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/d | n/d | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Oeste da Índia | n/d | CloudXchange global (GCX), dependência Jio, Sify, comunicações Tata, Verizon |
| **Mumbai2** | Airtel | 2 | Oeste da Índia | n/d | Airtel, Sify, Vodafone Idea |
| **Munique** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/d | 10G, 100G | |
| **Nova Iorque** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/d | 10G, 100G | CenturyLink Cloud Connect, Colt, Coresite, Equinix, MultiCloud, Megaport, pacote, Zayo |
| **Newport (País de Gales)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Oeste do Reino Unido | n/d | Telecomunicações britânicas, Colt, comunicações de nível 3, dados da próxima geração |
| **Osaca** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Oeste do Japão | n/d | Colt, Equinix, Internet Initiative Japão Inc.-IIJ, comunicação NTT, NTT SmartConnect, Softbank |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | França Central | n/d | CenturyLink Cloud Connect, Colt, Equinix, entre Cloud, Interxion, laranja, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/d | Database | Megaport, NextDC |
| **Cidade do Quebeque** | [Privilegiando](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Leste do Canadá | n/d | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | E.U.A. Centro-Sul | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **São Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Sul do Brasil | n/d | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | E.U.A. Oeste 2 | 10G, 100G | Redes aryaka Networks, Equinix, comunicações de nível 3, Megaport, Telus, Zayo |
| **Seul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Coreia do Sul Central | 10G, 100G | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Oeste dos E.U.A. | 10G, 100G | Redes aryaka networkss, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, entre nuvens, Internet2, IX REACH, pacote, PacketFabric, comunicações de nível 3, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Valley2 de silício** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Oeste dos E.U.A. | 10G, 100G | Colt, Coresite | 
| **Singapura** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Sudeste Asiático | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapura2** | [Opção global Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Sudeste Asiático | 10G, 100G | Colt, Épsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Montanhas verdes DC1](https://greenmountain.no/dc1-stavanger/) | 1 | n/d | 10G, 100G | |
| **Estocolmo** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/d | Database | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Leste da Austrália | 10G, 100G | AARNet, em & T netbonde, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Horário** | Diretor de telecomunicações | 2 | n/d | Database | Diretor de telecomunicações, FarEasTone |
| **Tóquio** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Este do Japão | 10G, 100G | Aryaka Networks Networks, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japão Inc.-IIJ, NTT Communications, NTT leste, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canadá Central | 10G, 100G | EM & T netbonde, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Washington D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Leste dos EUA, leste dos EUA 2 | 10G, 100G | Redes aryaka networkss, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, Internet2, entre Cloud, comunicações de nível 3, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Comunicações, Telia Carrier, Verizon, Zayo |
| **São Paulo DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Leste dos EUA, leste dos EUA 2 | 10G, 100G | Coresite, Viasat, Zayo | 
| **Zurique** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | n/d | n/d | Internuvem, Interxion, Swisscom |

 **+** brevemente disponível

### <a name="national-cloud-environments"></a>Ambientes em nuvem nacionais

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Localização** | **Corrigir** | **Regiões locais do Azure**| **ER direto** | **Fornecedores de serviços** |
| --- | --- | --- | --- | --- |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/d | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/d | 10G, 100G | Equinix, Megaport, Verizon |
| **Nova Iorque** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/d | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov - Texas | n/d | EM & T netbonde, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | Gov (US) - Texas | n/d | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/d | 10G, 100G | Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/d | n/d | Equinix, Megaport |
| **Washington D.C.** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD Leste, US Gov-Virgínia | 10G, 100G | EM & T netbonde, CenturyLink Cloud Connect, Equinix, comunicações de nível 3, Megaport, Verizon |

### <a name="china"></a>China
| **Localização** | **Fornecedores de serviços** |
| --- | --- |
| **Pequim** |China Telecom |
| **Beijing2** | Telecomunicações da China, GDS |
| **Xangai** |China Telecom |
| **Shanghai2** | Telecomunicações da China, GDS |

Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Alemanha
| **Localização** | **Fornecedores de serviços** |
| --- | --- |
| **Berlim** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Conectividade por meio de provedores do Exchange
Se o seu fornecedor de conectividade não está listado nas secções anteriores, pode criar na mesma uma ligação.

* Consulte o seu fornecedor de conectividade para saber se este está ligado a alguma das trocas na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizados pelos fornecedores de troca. Vários fornecedores de conectividade já estão ligados às trocas de Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Solicite ao seu fornecedor de conectividade para expandir a sua rede para a localização de peering que pretende.
  * Certifique-se de que o fornecedor de conectividade expande a sua conectividade para um estado de elevada disponibilidade, de forma a não existirem pontos únicos de falha.
* Organize um circuito ExpressRoute com a troca como o seu fornecedor de conectividade para se ligar à Microsoft.
  * Siga os passos em [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

## <a name="connectivity-through-satellite-operators"></a>Conectividade por meio de operadores satélite
Se você for remoto e não tiver conectividade de fibra ou se quiser explorar outras opções de conectividade, poderá verificar os seguintes operadores de satélite. 

* Intelsat
* [EAS](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Conectividade por meio de provedores de serviço adicionais
| **Localização** | **Exchange** | **Provedores de conectividade** |
| --- | --- | --- |
| **Amesterdão** | Equinix, Interxion, comunicações de nível 3 | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, The Golfo Bridge International, Kalaam Telecom Bahrein B. S. C, MainOne, Nianet, POST Telecom Luxemburgo, Proximus, TDC erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle de coroa
| **Cidade do Cabo** | Teraco | MTN |
| **Chicago** | Equinix| Coroa Castle, espectro Enterprise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, coroa Castle, dados encontrados, espectro empresarial, transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburgo** | Equinix | Cinia |
| **Rae de Hong Kong** | Equinix | Diretor, Macroview Telecom |
| **Joanesburgo** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, epsílon de telecomunicações limitada, exponencial E HSO, NexGen redes, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Coroa Castle, espectro empresarial, transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Tecnologias de portão da porta, Inc. Aptum, Rogers, Zirro |
| **Nova Iorque** |Equinix, Megaport | Altice Business, coroa Castle, espectro Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Cidade do Quebeque** | Megaport | Fibrenoire |
| **São Paula** | Equinix | Venha pra nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, espectro Enterprise, windstream, X2nsat Inc. |
| **Singapura** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, telecomunicações de Épsilon limitado, LGA de telecomunicações, rodovia de informações United (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tóquio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Porta-portão Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington D.C.** |Equinix | Altice Business, BICS, Cox Business, coroa Castle, GTT Communications Inc, Épsilon Communications Limited, Masergy, windstream |

## <a name="expressroute-system-integrators"></a>Integradores de sistemas ExpressRoute
A ativação da conectividade privada para atender as suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um dos integradores de sistemas listados na seguinte tabela para ajudá-lo com a integração do ExpressRoute.

| **Continente** | **Integradores de sistemas** |
| --- | --- |
| **Ásia** |Avanade Inc., OneAs1a |
| **Austrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **América do Norte** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **América do Sul** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
