---
title: 'Localizações e fornecedores de conectividade: O Azure ExpressRoute | Documentos da Microsoft'
description: Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure. Ordenado pelo fornecedor de conectividade.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: pareshmu
ms.openlocfilehash: 4520054eeb3ebc63f3aaaa2638b9e70122f1f3f9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455450"
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

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiões do Azure para localizações do ExpressRoute numa região geopolítica.
A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

| **Região geopolítica** | **zona** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- | --- |
| **Governo da Austrália** | 1 | Austrália Central, Austrália Central 2 |Camberra, Camberra2 |
| **Europa** | 1 |França Central, Sul de França, Europa do Norte, Europa Ocidental, Oeste do Reino Unido, Sul do Reino Unido |Amesterdão, Amsterdam2, Dublin, Londres, London2, Marselha, Newport(Wales), Paris, Zurique |
| **América do Norte** | 1 |E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Canadá Leste |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova Iorque, San Antonio, Seattle, Silicon Valley, Washington DC, Washington DC2, Montreal, cidade do Quebeque, Toronto |
| **Ásia** | 2 |Ásia Oriental, Sudeste Asiático |Hong Kong, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **Austrália** | 2 |Sudeste da Austrália, Leste da Austrália |Melbourne, Perth, Sydney | 
| **Índia** | 2 |Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | 2 |Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Coreia do Sul** | 2 |Centro da Coreia, Sul da Coreia do Sul |Busan, Seul|
| **África do Sul** | 3 |[Oeste da África do Sul+, Norte da África do Sul+](https://blogs.microsoft.com/blog/2017/05/18/microsoft-deliver-microsoft-cloud-datacenters-africa/) |Cidade do Cabo, Joanesburgo |
| **América do Sul** | 3 |Sul do Brasil |São Paulo |


 **+** brevemente disponível

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

A tabela seguinte mostra as localizações por fornecedor de serviços. Se quiser ver os fornecedores disponíveis por localização, veja [Fornecedores de serviços por localização](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Azure de Produção
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365 e Dynamics 365** | **Localizações** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Suportado |Suportado |Melbourne, Sydney |
| **[Airtel](http://www.airtel.in/creatingsmiles/)** | Suportado | Suportadas | Chennai2, Mumbai2 |
| **[Redes Aryaka](http://www.aryaka.com/)** |Suportado |Suportadas |Amesterdão, Chicago, Dallas, Hong Kong, São Paulo, Seattle, Silicon Valley, Singapura, Tóquio, Washington D.C. |
| **[Datacenters da Ascenty](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Suportadas |Suportado |São Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Toronto, Washington D.C. |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Suportado |Suportado |Montreal, Toronto, Cidade do Quebeque |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Suportado |Suportadas |Amesterdão, RAE de Hong Kong, Londres, Newport(Wales), são Paulo, Silicon Valley, Singapura, Sydney, Tóquio, Washington D.C. |
| **C3ntro** |Brevemente |Brevemente |Miami |
| **CDC** | Suportadas | Suportadas | Camberra, Camberra2 |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Suportado |Suportadas |Las Vegas, Nova Iorque, San Antonio, Silicon Valley, Tóquio, Toronto |
| **Diretor de telecomunicações** |Suportadas |Suportadas |Taipé |
| **China Telecom Global** |Suportado |Não suportado |RAE de Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Suportado |Suportadas |Chicago, Dallas, Montreal, Toronto, Washington D.C. |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportado |Suportadas |Amesterdão, Dublin, Londres, Paris, singapura2, Tóquio |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Suportado |Suportadas |Chicago, Denver, Los Angeles, Nova Iorque, Silicon Valley, Washington DC, Washington DC2 |
| **eir** |Suportado |Suportado |Dublin|
| **Epsilon Global Communications** |Suportado |Suportado |Singapura |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportadas |Amesterdão, Atlanta, Chicago, Dallas, Dublin, Hong Kong, Londres, London2, Los Angeles, Melbourne, Miami, Nova Iorque, Osaka, Paris, são Paulo, Seattle, Silicon Valley, Singapura, Sydney, Tóquio, Toronto, Washington D.C. |
| **euNetworks** |Suportado |Suportado |Amesterdão, Dublin, Londres |
| **GÉANT** |Suportado |Suportado |Amesterdão |
| **[Global Cloud Xchange (GCX)](http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Suportadas| Suportado | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Suportado |Suportadas |Amesterdão, Londres, Nova Iorque, Paris, Silicon Valley, Singapura, Washington D.C. |
| **Internet2** |Suportado |Suportadas |Chicago, Dallas, Washington D.C. |
| **[Internet Initiative Japan Inc. – IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Suportado |Suportado |Osaka, Tóquio |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Suportadas |Suportadas |Cidade do Cabo, Joanesburgo, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Suportado |Suportadas |Amesterdão, Amsterdam2, Dublin, Londres, Marselha, Paris, Zurique |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Suportado |Suportadas | Amesterdão, Silicon Valley, Toronto |
| **Jisc** |Suportado |Suportado |Londres |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Suportadas |Suportado |Seoul |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Suportado | Suportado | Amesterdão | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportadas |Amesterdão, Chicago, Dallas, Londres, Newport (Gales), são Paulo, Seattle, Silicon Valley, Singapura, Washington D.C. |
| **LG CNS** |Suportado |Suportado |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Suportadas |Suportadas |Cidade do Cabo, Joanesburgo |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado |Suportadas |Amesterdão, Atlanta, Chicago, Cidade do Quebeque, Dallas, Denver, Dublin, Hong Kong, Las Vegas, Londres, Los Angeles, Melbourne, Miami, Nova Iorque, San Antonio, Seattle, Silicon Valley, Singapura, Singapura2, Sydney, Toronto, Washington D.C. |
| **[MTN](http://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Suportadas |Suportado |Londres |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Suportado |Suportadas |Dallas, Los Angeles, Miami, são Paulo |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Suportado |Suportado |Newport (País de Gales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Suportadas |Suportadas |Melbourne, Perth, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Suportado |Suportado |Amesterdão, RAE de Hong Kong, Londres, Los Angeles, Osaka, Singapura, Sydney, Tóquio, Washington D.C. |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Suportadas |Suportadas |Tóquio |
| **[NTT SmartConnect](http://cloud.nttsmc.com/cxc/azure.html)** |Suportado |Suportado |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Suportado |Suportado |Melbourne, Sydney |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Suportado |Suportadas |Amesterdão, RAE de Hong Kong, Londres, Paris, são Paulo, Silicon Valley, Singapura, Sydney, Tóquio, Washington D.C. |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Suportadas |Suportadas |Chicago, RAE de Hong Kong, Londres |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Suportadas |Suportado |Seoul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Suportado |Suportadas |Chennai, Mumbai2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Suportado |Suportado |Singapura, Singapura2 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Suportado |Suportado |Osaka, Tóquio |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Suportado |Suportadas |Amesterdão, Chennai, RAE de Hong Kong, Londres, Mumbai, São Paulo, Silicon Valley, Singapura, Washington D.C. |
| **Telecity Group** |Suportadas |Suportado |Amesterdão |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Suportado |Suportado |Amesterdão, São Paulo |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Suportadas |Suportado |Londres |
| **Telenor** |Suportado |Suportado |Amesterdão, Londres |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Suportadas | Suportadas |Amesterdão, Chicago, Dallas, Londres, Washington D.C. |
| **TELMEX Uninet**| Brevemente | Brevemente | Dallas + |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Suportado |Suportadas |Melbourne, Singapura, Sydney |
| **[Telus](http://www.telus.com)** |Suportado |Suportado |Montreal, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Suportadas |Suportadas |Cidade do Cabo, Joanesburgo |
| **[DotCom de tempo](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Suportadas | Suportadas | Kuala Lumpur |
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Suportado |Suportado |São Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Hong Kong, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Washington D.C |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Suportado |Não suportado |Londres, Singapura |
| **Vodafone Idea** | Suportadas | Suportadas | Mumbai, Mumbai2 |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Suportado |Suportadas |Amesterdão, Chicago, Dallas, Londres, Los Angeles, Montreal, Nova Iorque, Nova Iorque, Silicon Valley, Toronto, Washington D.C. |

 **+** brevemente disponível

### <a name="national-cloud-environment"></a>Ambientes em nuvens nacionais

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Chicago, Washington D.C. |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Suportado |Suportadas |Nova Iorque, Phoenix |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportado |Chicago, Dallas, Nova Iorque, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado | Suportadas | Chicago, Dallas, San Antonio, Seattle |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Suportado |Suportadas |Chicago, Dallas, Nova Iorque, Silicon Valley, Washington DC |

### <a name="china"></a>China
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **China Telecom** |Suportado |Não suportado |Pequim, Xangai |
| **GDS** |Suportadas |Não suportado |Beijing2, Shanghai2 |

Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemanha
| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportado |Não suportado |Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Suportado |Não suportado |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Suportado |Não suportado |Berlim |
| **Interxion** |Suportado |Não suportado |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado  | Não suportado | Berlim |
| **T-Systems** |Suportado |Não suportado |Berlim |

## <a name="connectivity-through-exchange-providers"></a>Conectividade Através de Fornecedores do Exchange

Se o seu fornecedor de conectividade não está listado nas secções anteriores, pode criar na mesma uma ligação.

* Consulte o seu fornecedor de conectividade para saber se este está ligado a alguma das trocas na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizados pelos fornecedores de troca. Vários fornecedores de conectividade já estão ligados às trocas de Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Solicite ao seu fornecedor de conectividade para expandir a sua rede para a localização de peering que pretende.
  * Certifique-se de que o fornecedor de conectividade expande a sua conectividade para um estado de elevada disponibilidade, de forma a não existirem pontos únicos de falha.
* Organize um circuito ExpressRoute com a troca como o seu fornecedor de conectividade para se ligar à Microsoft.
  * Siga os passos em [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

## <a name="connectivity-through-additional-service-providers"></a>Conectividade Através de Fornecedores de Serviços Adicionais

| **Fornecedor de conectividade** | **Exchange** | **Localizações** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapura |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nova Iorque, Washington D.C. |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tóquio |
| **[Axtel](http://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londres |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amesterdão, Frankfurt, Londres, Singapura, Washington D.C. |
| **[BroadBand Tower, Inc.](http://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tóquio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | RAE de Hong Kong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburgo |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amesterdão | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | Londres |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londres, Singapura, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amesterdão |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amesterdão |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Cidade do Quebeque |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Bridge de longa distância internacional](http://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amesterdão |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Singapura|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, Nova Iorque, Washington D.C. |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |RAE de Hong Kong |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amesterdão |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](http://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Cidade do Cabo, Joanesburgo | 
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londres |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amesterdão, Frankfurt |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amesterdão |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amesterdão, Dublin, Londres, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londres | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amesterdão | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amesterdão |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amesterdão |
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapura |
| **[Venha Pra Nuvem](http://venhapranuvem.com.br/)** | Equinix | São Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nova Iorque |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington D.C. |
| **Zain** |Equinix |Londres|
| **[Zertia](http://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Conectividade Através de Fornecedores de Datacenters
| **Fornecedor** | **Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire Data Centers](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 Datacenters](http://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Conectividade Através de National Research and Education Networks (NREN, Redes de Investigação e Ensino Nacionais)

| **Fornecedor**|
| --- |
| **AARNET**| 
| **DeIC, através da GÉANT**|
| **GARR, através da GÉANT**|
| **GÉANT**|
| **HEAnet, através da GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, através da GÉANT**|
| **SINET**|
| **Surfnet, através da GÉANT**|

* Se o seu fornecedor de conectividade não estiver listado aqui, verifique se está ligado a algum dos Parceiros do Exchange do ExpressRoute apresentados acima.

## <a name="expressroute-system-integrators"></a>Integradores de sistemas ExpressRoute
A ativação da conectividade privada para atender as suas necessidades pode ser um desafio, com base na escala da sua rede. Pode trabalhar com qualquer um dos integradores de sistemas listados na seguinte tabela para ajudá-lo com a integração do ExpressRoute.

| **Integrador de sistemas** | **Continente** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](http://www.avanade.com/)** | Ásia, Europa, América do Norte, América do Sul |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europa
| **[Ensyst](http://www.ensyst.com.au)** | Ásia
| **[Serviços Profissionais Equinix](http://www.equinix.com/services/consulting/)** | América do Norte |
| **[FlexManage](http://www.flexmanage.com/cloud)** | América do Norte |
| **[Lightstream](https://www.ltstream.com/expressroute)** | América do Norte |
| **[The IT Consultancy Group](http://itconsult.com.au/)** | Austrália |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Austrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemanha) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/connectivity.html)** | Ásia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | América do Norte |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | América do Norte |
| **[sol-tec](https://www.sol-tec.com/services)** | Europa |
| **[Venha Pra Nuvem](http://venhapranuvem.com.br/)** | América do Sul |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Austrália |

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
