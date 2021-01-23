---
title: 'Localizações e os fornecedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: Este artigo fornece uma descrição geral detalhada das localizações onde os serviços são oferecidos e como ligar a regiões do Azure. Ordenado pelo fornecedor de conectividade.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 4856b572182646827b8016212bbb4c1b28f440ab
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740878"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>Parceiros de conectividade e localizações de peering do ExpressRoute

> [!div class="op_single_selector"]
> * [Localizações por Fornecedor](expressroute-locations.md)
> * [Fornecedores por Localização](expressroute-locations-providers.md)


Os quadros deste artigo fornecem informações sobre cobertura geográfica ExpressRoute e locais, fornecedores de conectividade ExpressRoute e Integradores de Sistemas ExpressRoute (SIs).

> [!Note]
> As regiões azure e as localizações ExpressRoute são dois conceitos distintos e diferentes, entendendo a diferença entre os dois é fundamental para explorar a conectividade híbrida Azure. 
>
>

## <a name="azure-regions"></a>Regiões do Azure
As regiões do Azul são centros de dados globais onde estão localizados os recursos de computação, networking e armazenamento do Azure. Ao criar um recurso Azure, um cliente precisa de selecionar uma localização de recursos. A localização do recurso determina em que centro de dados Azure (ou zona de disponibilidade) o recurso é criado.

## <a name="expressroute-locations"></a>Localizações do ExpressRoute
As localizações expressRoute (por vezes designadas como locais de observação ou locais de encontro)) são instalações de co-localização onde estão localizados os dispositivos Microsoft Enterprise Edge (MSEE). As localizações do ExpressRoute são o ponto de entrada da rede da Microsoft – e estão distribuídas globalmente, proporcionando aos clientes a oportunidade de se conectarem à rede da Microsoft em todo o mundo. Estas localizações são onde os parceiros ExpressRoute e ExpressRoute Direct emitem ligações cruzadas à rede da Microsoft. Em geral, a localização ExpressRoute não precisa de corresponder à região de Azure. Por exemplo, um cliente pode criar um circuito ExpressRoute com a localização de recursos *East US,* no local *de Observação* de Seattle.

Terá acesso aos serviços do Azure em todas as regiões numa região geopolítica se estiver ligado a, pelo menos, uma localização do ExpressRoute numa região geopolítica.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Regiões do Azure para localizações do ExpressRoute numa região geopolítica.
A tabela seguinte fornece um mapa de regiões do Azure para localizações do ExpressRoute numa região geopolítica.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Governo da Austrália** |Austrália Central, Austrália Central 2 |Camberra, Camberra2 |
| **Europa** | França Central, França Sul, Alemanha Norte, Alemanha West Central, Norte da Europa, Noruega Leste, Noruega Oeste, Suíça Norte, Suíça Oeste, Reino Unido Oeste, Reino Unido Sul, Europa Ocidental |Amesterdão, Amesterdão2, Berlim, Copenhague, Dublin, Frankfurt, Genebra, Londres, Londres2, Marselha, Milão, Munique, Newport(País de Gales), Oslo, Paris, Stavanger, Estocolmo, Zurique |
| **América do Norte** |E.U.A. Leste, E.U.A. Oeste, E.U.A. Leste 2, E.U.A. Oeste 2, E.U.A. Central, E.U.A. Centro-Sul, E.U.A. Centro-Norte, E.U.A. Centro-Oeste, Canadá Central, Leste do Canadá |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montreal, Nova Iorque, Phoenix, Quebec City, Queretaro (México), Quincy, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Toronto, Vancouver, Washington DC, Washington DC2 |
| **Ásia** | Ásia Leste, Ásia Sudeste | Banguecoque, Hong Kong, Hong Kong2, Jacarta, Kuala Lumpur, Singapura, Singapura2, Taipei |
| **Índia** | Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | Oeste do Japão, Leste do Japão |Osaka, Tóquio, Tóquio2 |
| **Oceânia** | Austrália Sudeste, Leste da Austrália |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Coreia do Sul** | Coreia do Sul Central, Sul da Coreia do Sul |Busan, Seul|
| **E.A.U.** | UAE Central, UAE North | Dubai |
| **África do Sul** | África do Sul Ocidental, África do Sul Norte |Cidade do Cabo, Joanesburgo |
| **América do Sul** | Sul do Brasil |Bogotá |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões e limites geopolíticos para nuvens nacionais
A tabela abaixo fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões do Azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Nuvem do US Gov** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD Central, US DoD East  |Atlanta,Chicago, Dallas, Nova Iorque, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Leste da China** |Leste da China, Leste da China 2 |Xangai |
| **Norte da China** |Norte da China, Norte da China 2 |Pequim, Pequim2 |
| **Alemanha** |Alemanha Central, Leste da Alemanha |Berlim, Frankfurt |

A conectividade em regiões geopolíticas não é suportada no SKU do ExpressRoute standard. Terá de ativar o suplemento ExpressRoute Premium para suportar a conetividade global. A conectividade com ambientes em nuvem nacionais não é suportada. Pode trabalhar com o seu fornecedor de conectividade, se tal for necessário.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Fornecedores de conectividade do ExpressRoute

A tabela seguinte mostra as localizações por fornecedor de serviços. Se quiser ver os fornecedores disponíveis por localização, veja [Fornecedores de serviços por localização](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Azure comercial global

| **Fornecedor de serviços** | **Microsoft Azure** | **Microsoft 365**  | **Localizações** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Suportado |Suportado |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Suportado | Suportado | Chennai2, Mumbai2 |
| **[RIO AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Suportado | Suportado | Rio Banguecoque |
| **[Redes Aryaka](https://www.aryaka.com/)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Hong Kong SAR, São Paulo, Seattle, Silicon Valley, Singapura, Tóquio, Washington DC |
| **[Datacenters da Ascenty](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Suportado |Suportado |São Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Frankfurt, Londres, Silicon Valley, Singapura, Sydney, Tóquio, Toronto, Washington DC |
| **[EM TÓQUIO](https://www.attokyo.com/connectivity/azure.html)** | Suportado | Suportado | Osaka |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Suportado | Suportado | Tóquio |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Suportado |Suportado |Cidade do Cabo, Joanesburgo|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Suportado |Suportado |Montreal, Toronto, Cidade do Quebeque |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Suportado |Suportado |Amesterdão, Amesterdão2, Chicago, Hong Kong SAR, Joanesburgo, Londres, Londres2, Newport (País de Gales), Paris, São Paulo, Silicon Valley, Singapura, Sydney, Tóquio, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Suportado |Suportado |Miami |
| **CDC** | Suportado | Suportado | Camberra, Camberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Suportado |Suportado |Amesterdão2, Chicago, Dublin, Frankfurt, Hong Kong, Las Vegas, Londres2, Nova Iorque, Paris, San Antonio, Silicon Valley, Tóquio, Toronto, Washington DC, Washington DC2 |
| **[Chefe de Telecomunicações](https://www.chief.com.tw/)** |Suportado |Suportado |Hong Kong |
| **China Mobile International** |Suportado |Suportado | Hong Kong, Hong Kong2, Singapura |
| **China Telecom Global** |Suportado |Suportado |Hong Kong |
| **China Unicom Global** |Suportado |Suportado | Hong Kong |
| **[Telecomunicações Chunghwa](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Suportado |Suportado |Taipé |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Suportado |Suportado |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Suportado |Suportado |Chicago, Dallas, Minneapolis, Montreal, Toronto, Vancouver, Washington DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Suportado |Suportado |Amesterdão, Amesterdão2, Chicago, Dublin, Frankfurt, Londres, Londres2, Milão, Newport, Nova Iorque, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapura2, Tóquio, Washington DC |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Suportado |Suportado |Chicago, Denver, Los Angeles, Nova Iorque, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Suportado |Suportado |Amesterdão2, Dubai2, Frankfurt, Marselha, Mumbai, Munique, Nova Iorque |
| **[Devoli](https://devoli.com/expressroute)** | Suportado |Suportado | Auckland |
| **du datamena** |Suportado |Suportado | Dubai2 |
| **eir** |Suportado |Suportado |Dublin|
| **[Comunicações Globais Epsilon](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Suportado |Suportado |Singapura, Singapura2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportado |Amesterdão, Amesterdão2, Atlanta, Berlim, Camberra2, Chicago, Dallas, Dubai2, Dublin, Frankfurt, Genebra, Hong Kong SAR, Londres, Londres2, Los Angeles, Los Angeles2, Melbourne, Miami, Milão, Nova Iorque, Osaka, Paris, São Paulo, Seattle, Seul, Silicon Valley, Singapura, Estocolmo, Sydney, Tóquio, Toronto, Washington DC, Zurique |
| **Emirados Árabes Unidos** |Suportado |Suportado |Rio Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Suportado |Suportado |Amsterdão, Amesterdão2, Dublin, Frankfurt, Londres |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Suportado |Suportado |Taipé|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Suportado |Suportado |Milão|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Suportado |Suportado |Montreal|
| **[GÉANT](https://www.geant.org/Networks)** |Suportado |Suportado |Amsterdão, Amesterdão2, Dublin, Frankfurt, Marselha |
| **[GlobalConnect]()** | Suportado |Suportado | Oslo | 
| **GTT** |Suportado |Suportado |Londres2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Suportado| Suportado | Chennai, Mumbai |
| **Intelsat** | Suportado | Suportado | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Suportado |Suportado |Amesterdão, Chicago, Frankfurt, Hong Kong, Londres, Nova Iorque, Paris, Silicon Valley, Singapura, Washington DC, Zurique |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Suportado |Suportado |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. – IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Suportado |Suportado |Osaka, Tóquio |
| **[Soluções de Internet - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Suportado |Suportado |Cidade do Cabo, Joanesburgo, Londres |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Suportado |Suportado |Amesterdão, Amesterdão2, Copenhague, Dublin, Frankfurt, Londres, Marselha, Paris, Zurique |
| **[IRIDEOS](https://irideos.it/)** |Suportado |Suportado |Milão |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Suportado |Suportado | Amsterdão, Londres2, Silicon Valley, Toronto, Washington DC |
| **Rede Jaguar** |Suportado |Suportado |Marselha |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Suportado |Suportado |Londres, Newport(País de Gales) |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Suportado |Suportado |Seoul |
| **[Rio Kordia](https://www.kordia.co.nz/cloudconnect)** | Suportado |Suportado |Auckland |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Suportado | Suportado | Amesterdão |
| **[KT](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | Suportado | Suportado | Seoul |
| **[Comunicações de nível 3](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Londres, Newport (País de Gales), São Paulo, Seattle, Silicon Valley, Singapura, Washington DC |
| **LG CNS** |Suportado |Suportado |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Suportado |Suportado |Cidade do Cabo, Joanesburgo |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado |Suportado |Amesterdão, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Frankfurt, Genebra, Hong Kong, Hong Kong, Las Vegas, Londres, Londres2, Los Angeles, Melbourne, Miami, Montreal, Nova Iorque, Osaka, Oslo, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapura2, Stavanger, Estocolmo, Sydney2, Tóquio, |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Suportado |Suportado |Londres |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Suportado |Suportado |Dallas, Los Angeles, Miami, São Paulo, Washington DC |
| **[Next Generation Data](https://vantage-dc-cardiff.co.uk/)** |Suportado |Suportado |Newport (País de Gales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Suportado |Suportado |Melbourne, Perth, Sydney, Sydney2 |
| **[NOS](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Suportado |Suportado |Amesterdão2 |
| **[Comunicações NTT](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Suportado |Suportado |Amesterdão, Hong Kong SAR, Londres, Los Angeles, Osaka, Singapura, Sydney, Tóquio, Washington DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Suportado |Suportado |Tóquio |
| **[NTT Global DataCenters EMEA](https://hello.global.ntt/)** |Suportado |Suportado |Amsterdam2 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Suportado |Suportado |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Suportado |Suportado |Marselha |
| **[Optus](https://www.optus.com.au/enterprise/)** |Suportado |Suportado |Melbourne, Sydney |
| **[Laranja](https://www.orange-business.com/en/products/business-vpn-galerie)** |Suportado |Suportado |Amesterdão, Amesterdão2, Dubai2, Frankfurt, Hong Kong SAR, Joanesburgo, Londres, Paris, São Paulo, Silicon Valley, Singapura, Sydney, Tóquio, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Suportado | Suportado | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Suportado |Suportado |Chicago, Las Vegas, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Suportado |Suportado |Chicago, Hong Kong, Hong Kong2, Londres, Singapura2 |
| **[Retelit](https://www.retelit.it/EN/Home.aspx)** | Suportado | Suportado | Milão | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Suportado |Suportado |Seoul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Suportado |Suportado | London2 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Suportado |Suportado |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Suportado |Suportado |Hong Kong2, Singapura, Singapura2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Suportado |Suportado |Osaka, Tóquio |
| **[Rio Sohonet](https://www.sohonet.com/fastlane/)** |Suportado |Suportado |Londres2 |
| **[Faísca NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Suportado |Suportado |Auckland |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Suportado | Suportado | Zurique |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Suportado |Suportado |Amesterdão, Chennai, Hong Kong SAR, Londres, Mumbai, São Paulo, Silicon Valley, Singapura, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Suportado |Suportado |Amesterdão, São Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Suportado |Suportado |Londres, Londres2, Singapura2 |
| **Telenor** |Suportado |Suportado |Amsterdão, Londres, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Suportado | Suportado |Amesterdão, Chicago, Dallas, Frankfurt, Hong Kong, Londres, Oslo, Paris, Silicon Valley, Estocolmo, Washington DC |
| **[Rio Telin](https://www.telin.net/)** | Suportado | Suportado |Jacarta |
| **TELMEX Uninet**| Suportado | Suportado | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Suportado |Suportado |Melbourne, Singapura, Sydney |
| **[Telus](https://www.telus.com)** |Suportado |Suportado |Montreal, Seattle, Toronto, Vancouver |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Suportado |Suportado |Cidade do Cabo, Joanesburgo |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Suportado | Suportado | Kuala Lumpur |
| **[Comunicações Tokai](https://www.tokai-com.co.jp/en/)** | Suportado | Suportado | Osaka |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Suportado |Suportado |Dallas, Queretaro (México)|
| **T-Systems** |Suportado |Suportado |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Suportado |Suportado |São Paulo |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Suportado | Suportado | Rio Banguecoque |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Hong Kong SAR, Londres, Mumbai, Silicon Valley, Singapura, Sydney, Tóquio, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Suportado | Suportado | Washington DC2 |
| **[Grupo Vocus NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Suportado | Suportado | Auckland |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Suportado |Suportado |Amsterdam2, Londres, Singapura |
| **[Ideia Vodafone](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Suportado | Suportado | Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Suportado |Suportado |Amesterdão, Chicago, Dallas, Denver, Londres, Los Angeles, Montreal, Nova Iorque, Paris, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** denota em breve

### <a name="national-cloud-environment"></a>Ambientes em nuvens nacionais

As nuvens nacionais azure são isoladas umas das outras e do Azure comercial global. ExpressRoute para uma nuvem Azure não pode ligar-se às regiões de Azure nas outras. 

### <a name="us-government-cloud"></a>Nuvem do US Gov

| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Suportado |Suportado |Chicago, Phoenix, Silicon Valley, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Suportado |Suportado |Nova Iorque, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Suportado |Suportado |Atlanta,Chicago, Dallas, Nova Iorque, Seattle, Silicon Valley, Washington DC |
| **[Comunicações de nível 3](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Suportado |Suportado |Chicago, Silicon Valley, Washington D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado | Suportado | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Suportado |Suportado |Chicago, Dallas, Nova Iorque, Silicon Valley, Washington DC |

### <a name="china"></a>China

| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **China Telecom** |Suportado |Não suportado |Pequim, Pequim2, Xangai, Xangai2 |
| **China Unicom** | Suportado | Não suportado | Beijing2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Suportado |Não suportado |Beijing2 |

Para saber mais, veja [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemanha

| **Fornecedor de serviços** | **Microsoft Azure** | **Office 365** | **Localizações** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Suportado |Não suportado |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Suportado |Não suportado |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Suportado |Não suportado |Berlim |
| **Interxion** |Suportado |Não suportado |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Suportado  | Não suportado | Berlim |
| **T-Systems** |Suportado |Não suportado |Berlim |

## <a name="connectivity-through-exchange-providers"></a>Conectividade através de fornecedores de intercâmbio

Se o seu fornecedor de conectividade não está listado nas secções anteriores, pode criar na mesma uma ligação.

* Consulte o seu fornecedor de conectividade para saber se este está ligado a alguma das trocas na tabela acima. Pode verificar as ligações seguintes para recolher mais informações sobre os serviços disponibilizados pelos fornecedores de troca. Vários fornecedores de conectividade já estão ligados às trocas de Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Solicite ao seu fornecedor de conectividade para expandir a sua rede para a localização de peering que pretende.
  * Certifique-se de que o fornecedor de conectividade expande a sua conectividade para um estado de elevada disponibilidade, de forma a não existirem pontos únicos de falha.
* Organize um circuito ExpressRoute com a troca como o seu fornecedor de conectividade para se ligar à Microsoft.
  * Siga os passos em [Criar um circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

## <a name="connectivity-through-satellite-operators"></a>Conectividade através de operadores de satélite
Se você é remoto e não tem conectividade de fibra ou você quer explorar outras opções de conectividade você pode verificar os seguintes operadores de satélite. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Conectividade através de prestadores de serviços adicionais

| **Fornecedor de conectividade** | **Exchange** | **Localizações** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapura |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Comunicações do Alasca](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nova Iorque, Washington D.C. |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tóquio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londres |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amesterdão, Frankfurt, Londres, Singapura, Washington DC |https://www.chief.com.tw/
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tóquio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief]()** | Equinix | R.A.E. de Hong Kong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburgo |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amesterdão | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapura | 
| **[Tecnologias Aptum](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://www.coreazure.com/)**| Equinix | Londres |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Castelo da Coroa](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, Nova Iorque, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | Londres, Singapura, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amesterdão |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amesterdão |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Cidade do Quebeque |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Ponte do Golfo Internacional](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amesterdão |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/azure/)**| Comunicações de nível 3 |Amesterdão |
| **LGA Telecom** |Equinix |Singapura|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |R.A.E. de Hong Kong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amesterdão |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Cidade do Cabo, Joanesburgo |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londres |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amesterdão, Frankfurt |
| **[Serviço Oncore Cloud Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[POST Telecom Luxemburgo](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amesterdão |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amesterdão, Dublin, Londres, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amesterdão |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Empresa spectrum](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, Nova Iorque, Silicon Valley | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londres | 
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

## <a name="connectivity-through-datacenter-providers"></a>Conectividade através de fornecedores de datacenter

| **Fornecedor** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport |
| **[Banco de dados](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[Centros de Dados QTS](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport |
| **[Centros de Dados de Fluxo]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach |
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Conectividade através de Redes Nacionais de Investigação e Educação (NREN)

| **Fornecedor**|
| --- |
| **AARNET**| 
| **DeIC, através da GÉANT**|
| **GARR, através da GÉANT**|
| **GÉANT**|
| **HEAnet, através da GÉANT**|
| **Internet2**|
| **RIO JISC**|
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
| **[Céu Brilhante GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Ásia
| **[Serviços Profissionais Equinix](https://www.equinix.com/services/consulting/)** | América do Norte |
| **[FlexManage](https://www.flexmanage.com/cloud)** | América do Norte |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | América do Norte |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Austrália |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Austrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemanha) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Ásia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | América do Norte |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | América do Norte |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | América do Sul |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Austrália |

## <a name="next-steps"></a>Próximos passos
* Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)
* Confirme que todos os pré-requisitos são cumpridos. Veja os [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
