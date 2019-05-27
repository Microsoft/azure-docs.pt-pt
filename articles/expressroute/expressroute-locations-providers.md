---
title: 'Localizações e fornecedores de conectividade: O Azure ExpressRoute | Documentos da Microsoft'
description: Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure. Ordenado por localização.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2019
ms.author: pareshmu
ms.openlocfilehash: 8253610bd0c329c952442044586236acc56d4957
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978090"
---
# <a name="expressroute-partners-and-peering-locations"></a>Parceiros e localizações de peering do ExpressRoute 

> [!div class="op_single_selector"]
> * [Localizações por Fornecedor](expressroute-locations.md)
> * [Fornecedores por Localização](expressroute-locations-providers.md)


As tabelas neste artigo fornecem informações sobre fornecedores de conectividade ExpressRoute, cobertura geográfica ExpressRoute, serviços em nuvem da Microsoft suportados no ExpressRoute e integradores de sistema ExpressRoute (SIs).

## <a name="partners"></a>Fornecedores de conectividade do ExpressRoute
O ExpressRoute é suportado em todas as regiões e localizações do Azure. O mapa seguinte fornece uma lista de regiões do Azure e localizações do ExpressRoute. As localizações do ExpressRoute mencionam os locais onde a Microsoft se junta a vários fornecedores de serviços.

![Mapa de localização][0]

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiões do Azure para localizações do ExpressRoute numa região geopolítica
A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

| **Região geopolítica** | **zona** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- | --- |
| **Governo da Austrália** | 1 | Austrália Central, Austrália Central 2 |Camberra, Camberra2 |
| **Europa** | 1 |França Central, Sul de França, Europa do Norte, Europa Ocidental, Oeste do Reino Unido, Sul do Reino Unido |Amesterdão, Amsterdam2, Dublin, Frankfurt, Londres, London2, Marselha, Newport(Wales), Paris, Zurique |
| **América do Norte** | 1 |E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Canadá Leste |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova Iorque, San Antonio, Seattle, Silicon Valley, Silicon Valley2, DC2 Washington DC, Washington, Montreal, cidade do Quebeque, Toronto |
| **Ásia** | 2 |Ásia Oriental, Sudeste Asiático |Hong Kong SAR, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **Austrália** | 2 |Sudeste da Austrália, Leste da Austrália |Melbourne, Perth, Sydney | 
| **Índia** | 2 |Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | 2 |Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Coreia do Sul** | 2 |Centro da Coreia, Sul da Coreia do Sul |Busan, Seul|
| **EMIRADOS ÁRABES UNIDOS** | 3 | EAU Central, Norte dos eau | Dubai, Dubai2 |
| **África do Sul** | 3 |África do Sul Ocidental, Norte da África do Sul |Cidade do Cabo, Joanesburgo |
| **América do Sul** | 3 |Sul do Brasil |São Paulo |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões e limites geopolíticos para nuvens nacionais
A tabela abaixo fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Nuvem para a Administração Pública dos EUA** |US Gov Arizona, US Gov - Iowa, US Gov Texas, Gov (US) - Virginia, US DoD Central, US DoD East  |Chicago, Dallas, Nova Iorque, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Leste da China** |Leste da China, Leste da China 2 |Shanghai, Shanghai2 |
| **Norte da China** |Norte da China, Norte da China 2 |Pequim, Beijing2 |
| **Alemanha** |Alemanha Central, Leste da Alemanha |Berlim, Frankfurt |

A conectividade em regiões geopolíticas não é suportada no SKU do ExpressRoute standard. Terá de ativar o suplemento ExpressRoute Premium para suportar a conetividade global. A conectividade com ambientes em nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade, se tal for necessário.

## <a name="locations"></a>Localizações dos fornecedores de conectividade

A tabela seguinte mostra as localizações de conectividade e os fornecedores de serviços para cada localização. Se pretender ver os fornecedores de serviços e as localizações para as quais podem fornecer serviços, veja [Localizações por fornecedor de serviços](expressroute-locations.md#locations). 

**Regiões do Azure locais** são aqueles que [ExpressRoute Local](expressroute-faqs.md) em cada localização de peering podem aceder. **n/d** indica que o Local do ExpressRoute não está disponível naquele local peering.


### <a name="production-azure"></a>Azure de Produção
| **Localização** | **Proprietário da Localização de Peering** | **Regiões do Azure locais** | **Fornecedores de Serviços** |
| --- | --- | --- | --- |
| **Amesterdão** | Equinix | Europa Ocidental | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amesterdão2** | Interxion | Europa Ocidental | DE-CIX, Interxion |
| **Atlanta** | Equinix | n/d | Equinix, Megaport |
| **Busan** |LG CNS | Sul da Coreia do Sul | LG CNS |
| **Camberra** | CDC | Austrália Central | CDC |
| **Camberra2** | CDC | Austrália Central 2| CDC |
| **Cidade do Cabo** | Teraco | Oeste da África do Sul | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Sul da Índia | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Sul da Índia | Airtel |
| **Chicago** | Equinix | EUA Centro-Norte | Aryaka Networks, AT&T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Dallas** | Equinix | n/d | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Verizon, Zayo|
| **Denver** | CoreSite | EUA Centro-Oeste | CoreSite, Megaport, Zayo |
| **Dubai** | UAE Etisalat | Norte dos E.A.U. | UAE Etisalat |
| **Dubai2** | du datamena | Norte dos E.A.U. | du datamena |
| **Dublin** | Equinix | Europa do Norte | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | n/d | DE-CIX, Interxion |
| **RAE de Hong Kong** | Equinix | Ásia Oriental | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Joanesburgo** | Teraco | Norte da África do Sul | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Kuala Lumpur** | TIME dotCom | n/d | TIME dotCom |
| **Las Vegas** | Switch | n/d | CenturyLink Cloud Connect, Megaport |
| **Londres** | Equinix | Reino Unido Sul | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Telehouse | Reino Unido Sul | Alcance IX, Equinix |
| **Los Angeles** | CoreSite | n/d | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marselha** |Interxion | Sul de França | Interxion |
| **Melbourne** | NextDC | Sudeste da Austrália | AARNet, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | n/d | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | n/d | Bell Canada, Cologix, Telus, Zayo |
| **Mumbai** | Tata Communications | Oeste da Índia | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, |
| **Mumbai2** | Airtel | Oeste da Índia | Airtel, Sify, Vodafone Idea |
| **Nova Iorque** | Equinix | n/d | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (País de Gales)** | Next Generation Data | Oeste do R.U. | British Telecom, comunicações de nível 3, dados da próxima geração |
| **Osaca** | Equinix | Oeste do Japão | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** | Interxion | França Central | Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | n/d | Megaport +, NextDC |
| **Cidade do Quebeque** | 4Degrees | Leste do Canadá | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | EUA Centro-Sul | CenturyLink Cloud Connect, Megaport |
| **São Paulo** | Equinix | Sul do Brasil | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | E.U.A. Oeste 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Zayo |
| **Seul** | KINX | Coreia do Sul Central | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | EUA Oeste | Redes Aryaka, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, InterCloud, chegar a IX, pacotes, PacketFabric, Level 3 Communications, megaport +, laranja, Sprint, Tata Communications, Verizon, Zayo |
| **Silicon Valley2** | Coresite | EUA Oeste | Coresite | 
| **Singapura** | Equinix | Sudeste Asiático | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapura2** | Global Switch | Sudeste Asiático | Colt, épsilon comunicações globais, megaport +, SingTel |
| **Sydney** | Equinix | Leste da Austrália | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, TPG Telecom, Verizon |
| **Taipei** | Diretor de telecomunicações | n/d | Diretor de telecomunicações, FarEasTone |
| **Tóquio** | Equinix | Leste do Japão | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Canadá Central | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Zayo |
| **Washington D.C.** | Equinix | E.U.A. leste, E.U.A. Leste 2 | Aryaka Networks, AT & T NetBond, British Telecom, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, megaport +, Neutrona redes, NTT Communications, Orange, PacketFabric, Sprint, Tata Communications, Telia operadora, Verizon, Zayo |
| **Washington DC2** | Coresite | E.U.A. leste, E.U.A. Leste 2 |Coresite | 
| **Zurich** | Interxion | n/d | Interxion |

 **+** brevemente disponível

### <a name="national-cloud-environments"></a>Ambientes em nuvem nacionais

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Localização** | **Fornecedores de Serviços** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **Nova Iorque** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington D.C.** |AT&T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Localização** | **Fornecedores de Serviços** |
| --- | --- |
| **Pequim** |China Telecom |
| **Beijing2** | GDS |
| **Xangai** |China Telecom |
| **Shanghai2** | GDS |

Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Alemanha
| **Localização** | **Fornecedores de Serviços** |
| --- | --- |
| **Berlim** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Conectividade Através de Fornecedores do Exchange
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

## <a name="c1partners"></a>Conectividade Através de Fornecedores de Serviços Adicionais
| **Localização** | **Exchange** | **Fornecedores de Conetividade** |
| --- | --- | --- |
| **Amesterdão** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telia |
| **Cidade do Cabo** | Teraco | MTN |
| **Chicago** | Equinix | Lightower, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Frankfurt** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **Hamburgo** | Equinix | Cinia |
| **RAE de Hong Kong** | Equinix | Diretor, Macroview telecomunicações |
| **Joanesburgo** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **Nova Iorque** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Paris** | Equinix | Proximus |
| **Cidade do Quebeque** | Megaport | Fibrenoire |
| **São Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Equinix | Cox Business, Windstream |
| **Singapura** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tóquio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies, Inc. Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington D.C.** |Equinix | Altice Business, BICS, Cox Business, Gtt comunicações Inc, épsilon telecomunicações limitado, Lightower, Masergy, Windstream |

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
