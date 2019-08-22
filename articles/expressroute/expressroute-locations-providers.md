---
title: 'Locais e provedores de conectividade: Azure ExpressRoute | Microsoft Docs'
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
ms.date: 08/14/2019
ms.author: cherylmc
ms.openlocfilehash: be3917d7943f808292e5ed15978a3d6aefd3afb5
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891836"
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

| **Região geopolítica** | **Zone** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- | --- |
| **Governo da Austrália** | 1 | Austrália Central, Austrália Central 2 |Camberra, Camberra2 |
| **Europa** | 1 |França Central, Sul de França, Europa do Norte, Europa Ocidental, Oeste do Reino Unido, Sul do Reino Unido |Amsterdã, Amsterdam2, Copenhague, Dublin, Frankfurt, Londres, London2, Marselha, Newport (Gales), Paris, Estocolmo, Zurique |
| **América do Norte** | 1 |E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Canadá Leste |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova York, San Antonio, Seattle, vale do silício, silício Valley2, Washington DC, Washington DC2, Montreal, cidade de Quebec, Toronto |
| **Ásia** | 2 |Ásia Oriental, Sudeste Asiático |Rae de Hong Kong, Kuala Lumpur, Cingapura, Cingapura2, Taipé |
| **Índia** | 2 |Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | 2 |Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Oceania** | 2 |Sudeste da Austrália, Leste da Austrália |Auckland, Melbourne, Perth, Sydney | 
| **Coreia do Sul** | 2 |Centro da Coreia, Sul da Coreia do Sul |Busan, Seul|
| **DOS EAU** | 3 | EAU Central, Norte dos EAU | Dubai, Dubai2 |
| **África do Sul** | 3 |Oeste da África do Sul, norte da África do Sul |Cidade do Cabo, Joanesburgo |
| **América do Sul** | 3 |Sul do Brasil |São Paulo |

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

As **regiões locais do Azure** são aquelas que o [ExpressRoute local](expressroute-faqs.md) em cada local de emparelhamento pode acessar. **n/a** indica que o ExpressRoute local não está disponível nesse local de emparelhamento.


### <a name="production-azure"></a>Azure de Produção
| **Location** | **Proprietário da Localização de Peering** | **Regiões locais do Azure** | **Fornecedores de Serviços** |
| --- | --- | --- | --- |
| **Amesterdão** | Equinix | Europa Ocidental | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amesterdão2** | Interxion | Europa Ocidental | CenturyLink Cloud Connect, DE-CIX, Interxion, Vodafone |
| **Atlanta** | Equinix | n/d | Equinix, Megaport |
| **Auckland** | NZ de grupo Vocus | n/d | Devoli, Kordia, Megaport, Spark NZ, Vocus grupo NZ |
| **Busan** |LG CNS | Coreia do Sul | LG CNS |
| **Camberra** | CDC | Austrália Central | CDC |
| **Camberra2** | CDC | Austrália Central 2| CDC |
| **Cidade do Cabo** | Teraco | Oeste da África do Sul | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Sul da Índia | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Sul da Índia | Airtel |
| **Chicago** | Equinix | EUA Centro-Norte | Redes aryaka networkss, em & T netbonde, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, entre Cloud, Internet2, comunicações de nível 3, Megaport, PacketFabric, PCCW global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenhague** | Interxion | n/d | Interxion |
| **Dallas** | Equinix | n/d | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | CoreSite | EUA Centro-Oeste | CoreSite, Megaport, Zayo |
| **Dubai** | Etisalat dos EAU | Norte dos E.A.U. | Etisalat dos EAU |
| **Dubai2** | du datamena | Norte dos E.A.U. | du datamena, Orixcom |
| **Dublin** | Equinix | Europa do Norte | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | n/d | CIX, Interxion, laranja |
| **Rae de Hong Kong** | Equinix | Ásia Oriental | Redes de aryaka Networks, telecomunicações britânicas, CenturyLink Cloud Connect, diretor de telecomunicações, China Telecom global, Equinix, Megaport, comunicação de NTT, laranja, PCCW global Limited, Tata Communications, Telia Carrier, Verizon |
| **Joanesburgo** | Teraco | Norte da África do Sul | British Telecom, soluções de Internet – conexão em nuvem, Liquid Telecom, laranja, teraco |
| **Kuala Lumpur** | TIME dotCom | n/d | TIME dotCom |
| **Las Vegas** | Comutador | n/d | CenturyLink Cloud Connect, Megaport |
| **Londres** | Equinix | Reino Unido Sul | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Telehouse | Reino Unido Sul | IX REACH, Equinix |
| **Los Angeles** | CoreSite | n/d | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marselha** |Interxion | Sul de França | Rede DE CIX, Interxion, Jaguar |
| **Melbourne** | NextDC | Sudeste da Austrália | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | n/d | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | n/d | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | Oeste da Índia | CloudXchange global (GCX), dependência Jio, Sify, comunicações Tata, Verizon |
| **Mumbai2** | Airtel | Oeste da Índia | Airtel, Sify, Vodafone Idea |
| **Nova Iorque** | Equinix | n/d | CenturyLink Cloud Connect, Coresite, Equinix, discloud, Megaport, pacote, Zayo |
| **Newport (País de Gales)** | Next Generation Data | Reino Unido Oeste | Telecomunicações britânicas, Colt, comunicações de nível 3, dados da próxima geração |
| **Osaca** | Equinix | Oeste do Japão | Colt, Equinix, Internet Initiative Japão Inc.-IIJ, comunicação NTT, NTT SmartConnect, Softbank |
| **Paris** | Interxion | França Central | CenturyLink Cloud Connect, Colt, Equinix, entre Cloud, Interxion, laranja, Telia Carrier, Zayo |
| **Perth** | NextDC | n/d | Megaport, NextDC |
| **Cidade do Quebeque** | 4Degrees | Leste do Canadá | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | EUA Centro-Sul | CenturyLink Cloud Connect, Megaport |
| **São Paulo** | Equinix | Sul do Brasil | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | EUA Oeste 2 | Redes aryaka Networks, Equinix, comunicações de nível 3, Megaport, Telus, Zayo |
| **Seul** | KINX | Coreia do Sul Central | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | EUA Oeste | Redes aryaka networkss, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, internuvem, Internet2, IX REACH, pacote, PacketFabric, comunicações de nível 3, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Valley2 de silício** | Coresite | EUA Oeste | Coresite | 
| **Singapura** | Equinix | Sudeste Asiático | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapura2** | Global Switch | Sudeste Asiático | Colt, Épsilon Global Communications, Megaport, SingTel |
| **Estocolmo** | Equinix | n/d | Operadora de Telia |
| **Sydney** | Equinix | Leste da Austrália | AARNet, em & T netbonde, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Horário** | Diretor de telecomunicações | n/d | Diretor de telecomunicações, FarEasTone |
| **Tóquio** | Equinix | Leste do Japão | Aryaka Networks Networks, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japão Inc.-IIJ, NTT Communications, NTT leste, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Canadá Central | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington D.C.** | Equinix | Leste dos EUA, leste dos EUA 2 | Redes aryaka networkss, em & T netbonde, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, Internet2, entre Cloud, comunicações de nível 3, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, Sprint, Tata Comunicações, Telia Carrier, Verizon, Zayo |
| **São Paulo DC2** | Coresite | Leste dos EUA, leste dos EUA 2 |Coresite | 
| **Zurique** | Interxion | n/d | Internuvem, Interxion |

 **+** brevemente disponível

### <a name="national-cloud-environments"></a>Ambientes em nuvem nacionais

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Localização** | **Fornecedores de Serviços** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **Nova Iorque** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | EM & T netbonde, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington D.C.** |AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>China
| **Localização** | **Fornecedores de Serviços** |
| --- | --- |
| **Pequim** |China Telecom |
| **Beijing2** | Telecomunicações da China, GDS |
| **Xangai** |China Telecom |
| **Shanghai2** | Telecomunicações da China, GDS |

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
| **Amesterdão** | Equinix, Interxion, comunicações de nível 3, telecity | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, The Golfo Bridge International, Kalaam Telecom Bahrein B. S. C, MainOne, Nianet, POST Telecom Luxemburgo, Proximus, TDC erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle de coroa
| **Cidade do Cabo** | Teraco | MTN |
| **Chicago** | Equinix| Coroa Castle, espectro Enterprise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, coroa Castle, dados encontrados, espectro empresarial, transtelco |
| **Frankfurt** | Interxion, telecity | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburgo** | Equinix | Cinia |
| **Rae de Hong Kong** | Equinix | Diretor, Macroview Telecom |
| **Joanesburgo** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks, telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Coroa Castle, espectro empresarial, transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco par 1, Rogers, Zirro |
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
| **Toronto** | Equinix, Megaport | Porta-portão Technologies Inc., Beanfield Metroconnect, Cogeco par 1, IVedha Inc, Rogers, Thinktel, Zirro|
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
