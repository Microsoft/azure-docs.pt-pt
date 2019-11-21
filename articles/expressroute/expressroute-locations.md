---
title: 'Localizações e os fornecedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure. Ordenado pelo fornecedor de conectividade.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: cherylmc
ms.openlocfilehash: 793d88ec67fbd839851894077e8f5c481b9ebda7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217084"
---
# <a name="expressroute-partners-and-peering-locations"></a>Parceiros e localizações de peering do ExpressRoute

> [!div class="op_single_selector"]
> * [Localizações por Fornecedor](expressroute-locations.md)
> * [Fornecedores por Localização](expressroute-locations-providers.md)


The tables in this article provide information on ExpressRoute geographical coverage and locations, ExpressRoute connectivity providers,and ExpressRoute System Integrators (SIs).

> [!Note]
> Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## <a name="azure-regions"></a>Regiões do Azure
Azure regions are global datacenters where Azure compute, networking and storage resources are located. When creating an Azure resource, a customer needs to select a resource location. The resource location determines which Azure datacenter (or availability zone) the resource is created in.

## <a name="expressroute-locations"></a>Localizações do ExpressRoute
ExpressRoute locations (sometimes referred to as peering locations or meet-me-locations) are co-location facilities where Microsoft Enterprise Edge (MSEE) devices are located. ExpressRoute locations are the entry point to Microsoft’s network – and are globally distributed, providing customers the opportunity to connect to Microsoft’s network around the world. These locations are where ExpressRoute partners and ExpressRoute Direct customers issue cross connections to Microsoft’s network. In general, the ExpressRoute location does not need to match the Azure region. For example, a customer can create an ExpressRoute circuit with the resource location *East US*, in the *Seattle* Peering location.

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica.

## <a name="locations"></a>Azure regions to ExpressRoute locations within a geopolitical region.
A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Governo da Austrália** |Austrália Central, Austrália Central 2 |Camberra, Camberra2 |
| **Europa** | França Central, Sul de França, Europa do Norte, Europa Ocidental, Oeste do Reino Unido, Sul do Reino Unido |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, Geneva, London, London2, Marseille, Milan, Munich, Newport(Wales), Oslo, Paris, Stavanger, Stockholm, Zurich |
| **América do Norte** |E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Canadá Leste |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Ásia** | Ásia Oriental, Sudeste Asiático |Hong Kong SAR, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **Índia** | Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | Oeste do Japão, Leste do Japão |Osaka, Tóquio |
| **Oceania** | Sudeste da Austrália, Leste da Austrália |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Coreia do Sul** | Centro da Coreia, Sul da Coreia do Sul |Busan, Seul|
| **UAE** | UAE Central, UAE North | Dubai, Dubai2 |
| **África do Sul** | South Africa West, South Africa North |Cidade do Cabo, Joanesburgo |
| **América do Sul** | Sul do Brasil |São Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões e limites geopolíticos para nuvens nacionais
A tabela abaixo fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Nuvem para a Administração Pública dos EUA** |US Gov Arizona, US Gov - Iowa, US Gov Texas, Gov (US) - Virginia, US DoD Central, US DoD East  |Chicago, Dallas, Nova Iorque, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Leste da China** |Leste da China, Leste da China 2 |Shanghai, Shanghai2 |
| **Norte da China** |Norte da China, Norte da China 2 |Beijing, Beijing2 |
| **Alemanha** |Alemanha Central, Leste da Alemanha |Berlim, Frankfurt |

A conectividade em regiões geopolíticas não é suportada no SKU do ExpressRoute standard. Terá de ativar o suplemento ExpressRoute Premium para suportar a conetividade global. A conectividade com ambientes em nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade, se tal for necessário.

## <a name="partners"></a>Fornecedores de conectividade do ExpressRoute

A tabela seguinte mostra as localizações por fornecedor de serviços. Se quiser ver os fornecedores disponíveis por localização, veja [Fornecedores de serviços por localização](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Global commercial Azure

| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365**  | **Localizações** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Suportadas |Suportadas |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Suportadas | Suportadas | Chennai2, Mumbai2 |
| **[Redes Aryaka](https://www.aryaka.com/)** |Suportadas |Suportadas |Amsterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Datacenters da Ascenty](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Suportadas |Suportadas |São Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportadas |Suportadas |Amesterdão, Chicago, Dallas, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Toronto, Washington D.C. |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Suportadas |Suportadas |Montreal, Toronto, Cidade do Quebeque |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Suportadas |Suportadas |Amsterdam, Hong Kong SAR, Johannesburg, London, Newport(Wales), Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Suportadas |Suportadas |Miami |
| **CDC** | Suportadas | Suportadas | Camberra, Camberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Suportadas |Suportadas |Amsterdam2, Chicago, Hong Kong, Las Vegas, New York, Paris, San Antonio, Silicon Valley, Tokyo, Toronto, Washington DC |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Suportadas |Suportadas |Hong Kong, Taipei |
| **China Telecom Global** |Suportadas |Suportadas |RAE de Hong Kong |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Suportadas |Suportadas |Chicago, Dallas, Montreal, Toronto, Washington D.C. |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportadas |Suportadas |Amsterdam, Amsterdam2, Dublin, London, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapore2, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Suportadas |Suportadas |Chicago, Silicon Valley, Washington D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Suportadas |Suportadas |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Suportadas |Suportadas |Amsterdam2, Frankfurt, Marseille|
| **[Devoli](https://devoli.com/expressroute)** | Suportadas |Suportadas | Auckland, Melbourne, Sydney |
| **du datamena** |Suportadas |Suportadas | Dubai2 |
| **eir** |Suportadas |Suportadas |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Suportadas |Suportadas |Singapura, Singapura2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Suportadas |Suportadas |Amsterdam, Atlanta, Chicago, Dallas, Dublin,  Hong Kong SAR, London, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapore, Stockholm, Sydney, Tokyo, Toronto, Washington DC |
| **Etisalat UAE** |Suportadas |Suportadas |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Suportadas |Suportadas |Amsterdam, Amsterdam2, Dublin, London |
| **FarEasTone** |Suportadas |Suportadas |Taipé|
| **GÉANT** |Suportadas |Suportadas |Amesterdão |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Suportadas| Suportadas | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Suportadas |Suportadas |Amsterdam, Chicago, London, New York, Paris, Silicon Valley, Singapore, Washington DC, Zurich |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Suportadas |Suportadas |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. – IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Suportadas |Suportadas |Osaka, Tóquio |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Suportadas |Suportadas |Cidade do Cabo, Joanesburgo, Londres |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Suportadas |Suportadas |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, London, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Suportadas |Suportadas | Amsterdam, London2, Silicon Valley, Toronto |
| **Jaguar Network** |Suportadas |Suportadas |Marselha|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Suportadas |Suportadas |Londres |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Suportadas |Suportadas |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Suportadas |Suportadas |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Suportadas | Suportadas | Amesterdão | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportadas |Suportadas |Amsterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Suportadas |Suportadas |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Suportadas |Suportadas |Cidade do Cabo, Joanesburgo |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportadas |Suportadas |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Hong Kong SAR, Las Vegas, London, Los Angeles, Melbourne, Miami, Montreal, New York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Suportadas |Suportadas |Londres |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Suportadas |Suportadas |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Suportadas |Suportadas |Newport (País de Gales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Suportadas |Suportadas |Melbourne, Perth, Sydney, Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Suportadas |Suportadas |Amsterdam, Hong Kong SAR, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Suportadas |Suportadas |Tóquio |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Suportadas |Suportadas |Osaca |
| **[Optus](https://www.optus.com.au/enterprise/)** |Suportadas |Suportadas |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Suportadas |Suportadas |Amsterdam, Frankfurt, Hong Kong SAR, Johannesburg, London, Paris, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Suportadas | Suportadas | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Suportadas |Suportadas |Chicago, Silicon Valley, Washington D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Suportadas |Suportadas |Chicago, Hong Kong SAR, London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Suportadas |Suportadas |Seul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Suportadas |Suportadas | Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Suportadas |Suportadas |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Suportadas |Suportadas |Singapura, Singapura2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Suportadas |Suportadas |Osaka, Tóquio |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Suportadas |Suportadas |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Suportadas |Suportadas |Chicago, Silicon Valley, Washington D.C. |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Suportadas | Suportadas | Zurique |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Suportadas |Suportadas |Amsterdam, Chennai, Hong Kong SAR, London, Mumbai, Sao Paulo, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Suportadas |Suportadas |Amesterdão, São Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Suportadas |Suportadas |London, London2 |
| **Telenor** |Suportadas |Suportadas |Amsterdam, London, Oslo |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Suportadas | Suportadas |Amsterdam, Chicago, Dallas, Hong Kong, London, Paris, Silicon Valley, Stockholm, Washington DC |
| **TELMEX Uninet**| Suportadas | Suportadas | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Suportadas |Suportadas |Melbourne, Singapura, Sydney |
| **[Telus](https://www.telus.com)** |Suportadas |Suportadas |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Suportadas |Suportadas |Cidade do Cabo, Joanesburgo |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Suportadas | Suportadas | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Suportadas |Suportadas |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Suportadas |Suportadas |São Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Suportadas |Suportadas |Amsterdam, Chicago, Dallas, Hong Kong SAR, London, Mumbai, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Suportadas | Suportadas | Washington DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Suportadas | Suportadas | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Suportadas |Suportadas |Amsterdam2, London, Singapore |
| **[Vodafone Idea](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Suportadas | Suportadas | Mumbai, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Suportadas |Suportadas |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** brevemente disponível

### <a name="national-cloud-environment"></a>Ambientes em nuvens nacionais

Azure national clouds are isolated from each other and from global commerical Azure. ExpressRoute for one Azure cloud can't connect to the Azure regions in the others. 

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA

| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportadas |Suportadas |Chicago, Phoenix,  Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Suportadas |Suportadas |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Suportadas |Suportadas |Chicago, Dallas, Nova Iorque, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportadas |Suportadas |Chicago, Silicon Valley, Washington D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportadas | Suportadas | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Suportadas |Suportadas |Chicago, Dallas, Nova Iorque, Silicon Valley, Washington DC |

### <a name="china"></a>China

| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **China Telecom** |Suportadas |Não suportado |Beijing, Beijing2, Shanghai, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Suportadas |Não suportado |Beijing2, Shanghai2 |

Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemanha

| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Suportadas |Não suportado |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Suportadas |Não suportado |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Suportadas |Não suportado |Berlim |
| **Interxion** |Suportadas |Não suportado |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportadas  | Não suportado | Berlim |
| **T-Systems** |Suportadas |Não suportado |Berlim |

## <a name="connectivity-through-exchange-providers"></a>Connectivity through Exchange providers

Se o seu fornecedor de conectividade não está listado nas secções anteriores, pode criar na mesma uma ligação.

* Consulte o seu fornecedor de conectividade para saber se este está ligado a alguma das trocas na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizados pelos fornecedores de troca. Vários fornecedores de conectividade já estão ligados às trocas de Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Solicite ao seu fornecedor de conectividade para expandir a sua rede para a localização de peering que pretende.
  * Certifique-se de que o fornecedor de conectividade expande a sua conectividade para um estado de elevada disponibilidade, de forma a não existirem pontos únicos de falha.
* Organize um circuito ExpressRoute com a troca como o seu fornecedor de conectividade para se ligar à Microsoft.
  * Siga os passos em [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

## <a name="connectivity-through-satellite-operators"></a>Connectivity through satellite operators
If you are remote and don't have fiber connectivity or you want to explore other connectivity options you can check the following satellite operators. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connectivity through additional service providers

| **Fornecedor de conectividade** | **Exchange** | **Localizações** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapura |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nova Iorque, Washington D.C. |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tóquio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londres |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, London, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tóquio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | RAE de Hong Kong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburgo |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amesterdão | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapura | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | Londres |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londres, Singapura, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amesterdão |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amesterdão |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Cidade do Quebeque |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amesterdão |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Comunicações de nível 3 |Amesterdão |
| **LGA Telecom** |Equinix |Singapura|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |RAE de Hong Kong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amesterdão |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Cidade do Cabo, Joanesburgo |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londres |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amesterdão, Frankfurt |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amesterdão |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amesterdão, Dublin, Londres, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londres | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amesterdão | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amesterdão |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amesterdão, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amesterdão |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapura |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | São Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nova Iorque |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington D.C. |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Londres|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Connectivity through datacenter providers

| **Fornecedor** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectivity through National Research and Education Networks (NREN)

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
| **[Avanade Inc.](https://www.avanade.com/)** | Ásia, Europa, América do Norte, América do Sul |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Ásia
| **[Serviços Profissionais Equinix](https://www.equinix.com/services/consulting/)** | América do Norte |
| **[FlexManage](https://www.flexmanage.com/cloud)** | América do Norte |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | América do Norte |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Austrália |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Austrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemanha) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Ásia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | América do Norte |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | América do Norte |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | América do Sul |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Austrália |

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
