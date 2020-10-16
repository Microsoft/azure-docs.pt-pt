---
title: Interligar-se com a China usando Azure Virtual WAN e secure Hub
description: Aprenda a interligar-se com a China usando a Azure Virtual WAN e um hub seguro.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: sukishen
ms.openlocfilehash: 83cc7757f31a631af755155b49c7c26753618426
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399113"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interligar-se com a China usando Azure Virtual WAN e Secure Hub

Quando se olha para as indústrias de automóveis, de fabrico, logística ou outros institutos, como embaixadas, há muitas vezes a questão de como melhorar a interligação com a China. Estas melhorias são principalmente relevantes para a utilização de Serviços Cloud como o Microsoft 365, Azure Global Services ou entreense em sucursais dentro da China com uma espinha dorsal do cliente.

Na maioria dos casos, os clientes estão a ter dificuldades com altas latências, baixa largura de banda, conexão instável e custos elevados de ligação a fora da China (por exemplo, Europa ou Estados Unidos).

Uma das razões para estas lutas é a "Grande Firewall da China", que protege a parte chinesa da Internet e filtra o tráfego para a China. Quase todo o tráfego que vai da China Continental para fora da China, exceto as zonas especiais de administração como Hong Kong e Macau, passa pela Grande Firewall. O tráfego que atravessa Hong Kong e Macau não atinge a Grande Firewall em força, é tratado por um subconjunto da Grande Firewall.

![Interligação do fornecedor](./media/interconnect-china/provider.png)

Utilizando o Virtual WAN, um cliente pode estabelecer uma ligação mais performante e estável aos Serviços Microsoft Cloud e uma ligação à sua rede empresarial sem violar a lei chinesa de cibersegurança.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisitos e fluxo de trabalho

Se quiser manter-se em conformidade com a lei chinesa de cibersegurança, precisa de cumprir um conjunto de certas condições.

Em primeiro lugar, você precisa trabalhar em conjunto com uma rede e ISP que possui uma licença ICP (Fornecedor de Conteúdos de Internet) para a China. Na maioria dos casos, acabará com um dos seguintes fornecedores:

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

Dependendo do fornecedor e das suas necessidades, precisa agora de adquirir um dos seguintes serviços de conectividade de rede para interligar as suas agências dentro da China.

* Uma rede MPLS/IPVPN 
* Um WAN definido por software (SDWAN)
* Acesso à Internet dedicado

Em seguida, você precisa concordar com esse fornecedor para dar uma fuga para a Microsoft Global Network e sua Rede Edge em Hong Kong, não em Pequim ou Xangai. Neste caso, Hong Kong é muito importante devido à sua ligação física e localização à China.

Embora a maioria dos clientes pense que usar Singapura para interligação é o melhor caso porque parece mais próximo da China quando olha para o mapa, isso não é verdade. Quando segue mapas de fibra de rede, quase todas as ligações de rede passam por Pequim, Xangai e Hong Kong. Isto faz de Hong Kong uma melhor escolha de localização para se interligar com a China.

Dependendo do fornecedor, poderá obter diferentes ofertas de serviços. O quadro abaixo mostra um exemplo de prestadores e do serviço que oferecem, com base em informações no momento em que este artigo foi escrito.

| Serviço | Exemplos de fornecedor |
| --- | --- |
| Rede MPLS/IPVPN |PCCW, China Telecom Global |
|SDWAN| PCCW, China Telecom Global|
| Acesso à Internet dedicado | PCCW, Hong Kong Telecom, China Mobil|

Com o seu fornecedor, pode concordar em qual das duas soluções a utilizar para chegar à espinha dorsal global da Microsoft:

* A destituição do Microsoft Azure ExpressRoute em Hong Kong. É o caso da utilização de MPLS/IPVPN. Atualmente, apenas o único fornecedor de licenças ICP com ExpressRoute para Hong Kong é a China Telecom Global. No entanto, também podem falar com os outros fornecedores se alavancarem fornecedores de Bolsa de Nuvem como o Megaport ou o InterCloud. Para obter mais informações, consulte [os fornecedores de conectividade ExpressRoute](../expressroute/expressroute-locations-providers.md#partners).

* Utilizando um Acesso à Internet dedicado diretamente num dos seguintes Pontos de Troca de Internet, ou utilizando uma rede privada interligada.

A lista que se segue mostra as trocas de Internet possíveis em Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Ao utilizar esta ligação, o seu próximo salto BGP para serviços microsoft deve ser o Número de Sistema Autónomo da Microsoft (AS#) 8075. Se utilizar uma única localização ou solução SDWAN, essa seria a escolha da ligação.

De qualquer forma, recomendamos que tenha uma segunda e regular fuga de internet para o continente chinês. Isto é para dividir o tráfego entre o tráfego empresarial para serviços na nuvem como o Microsoft 365 e Azure, e o tráfego de Internet regulamentado por lei.

Uma arquitetura de rede compatível dentro da China poderia parecer o seguinte exemplo:

![Vários ramos](./media/interconnect-china/multi-branch.png)

Neste exemplo, tendo uma interligação com a Microsoft Global Network em Hong Kong, pode agora começar a alavancar a [Azure Virtual WAN Global Transit Architecture](virtual-wan-global-transit-network-architecture.md) e serviços adicionais, como o Azure secure Virtual WAN hub, de forma a consumir serviços e interligar-se aos seus balcões e datacenter fora da China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicação hub-to-hub

Nesta secção, usamos a comunicação virtual WAN hub-to-hub para interligar. Neste cenário, você cria um novo recurso de hub Virtual WAN para conectar a um hub Virtual WAN em Hong Kong, outras regiões que você prefere, uma região onde você já tem recursos Azure, ou onde quer se conectar.

Uma arquitetura de amostra pode parecer o seguinte exemplo:

![Amostra WAN](./media/interconnect-china/sample.png)

Neste exemplo, as sucursais da China conectam-se à Azure Cloud China e entre si utilizando ligações VPN ou MPLS. Os balcões que precisam de ser ligados aos Serviços Globais utilizam MPLS ou serviços baseados na Internet que estão ligados diretamente a Hong Kong. Se quiser utilizar o ExpressRoute em Hong Kong e noutra região, precisa de configurar o [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para interligar ambos os Circuitos ExpressRoute.

O ExpressRoute Global Reach não está disponível em algumas regiões. Se você precisa de interligar com o Brasil ou a Índia, por exemplo, você precisa aproveitar [a Cloud Exchange Providers](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) para fornecer os serviços de encaminhamento.

A figura abaixo mostra ambos os exemplos para este cenário.

![Alcance Global](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Fuga de Internet segura para o Microsoft 365

Outra consideração é a segurança da rede, bem como o registo para o ponto de entrada entre a China e o componente de espinha dorsal estabelecido pela WAN virtual, e a espinha dorsal do cliente. Na maioria dos casos, há necessidade de entrar na Internet em Hong Kong para chegar diretamente à Microsoft Edge Network e, com isso, os Servidores de Porta Frontal Azure utilizados para os Serviços Microsoft 365.

Para ambos os cenários com O WAN Virtual, você aproveitaria o [hub seguro Azure Virtual WAN](../firewall-manager/secured-virtual-hub.md). Utilizando o Azure Firewall Manager, pode alterar um hub virtual WAN normal para um hub seguro e, em seguida, implementar e gerir um Azure Firewall dentro desse hub.

A seguinte figura mostra um exemplo deste cenário:

![Fuga de internet para tráfego de serviços web e Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Arquitetura e fluxos de tráfego

Dependendo da sua escolha em relação à ligação a Hong Kong, a arquitetura geral pode mudar ligeiramente. Esta secção mostra três arquiteturas disponíveis em diferentes combinações com VPN ou SDWAN e/ou ExpressRoute.

Todas estas opções utilizam o hub seguro Azure Virtual WAN para a conectividade direta do Microsoft 365 em Hong Kong. Estas arquiteturas também suportam os requisitos de conformidade para [o Microsoft 365 Multi-Geo](/microsoft-365/enterprise/microsoft-365-multi-geo) e mantêm esse tráfego perto da próxima localização da Porta Frontal Azure. Como resultado, é também uma melhoria para o uso do Microsoft 365 fora da China.

Ao utilizar o Azure Virtual WAN juntamente com as ligações à Internet, todas as ligações podem beneficiar de serviços adicionais como [o Microsoft Azure Peering Services (MAPS)](https://docs.microsoft.com/azure/peering-service/about). O MAPS foi construído para otimizar o tráfego que chega à Microsoft Global Network a partir de Fornecedores de Serviços de Internet de 3ª Parte.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opção 1: SDWAN ou VPN

Esta secção discute um design que usa SDWAN ou VPN para Hong Kong e para outros ramos. Esta opção mostra a utilização e o fluxo de tráfego ao utilizar a ligação pura à Internet em ambos os sites da espinha dorsal Virtual WAN. Neste caso, a ligação é trazida para Hong Kong usando acesso dedicado à Internet, ou uma solução SDWAN fornecedora de ICP. Outros ramos também estão a usar soluções puras de Internet ou SDWAN.

![China para hong kong tráfego](./media/interconnect-china/china-traffic.png)

Nesta arquitetura, todos os sites estão ligados à Microsoft Global Network utilizando VPN e Azure Virtual WAN. O tráfego entre os sites e Hong Kong é transmitido através da Rede Microsoft e apenas utiliza ligação regular à Internet na última milha.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opção 2: ExpressRoute e SDWAN ou VPN

Esta secção discute um design que usa o ExpressRoute em Hong Kong e outros balcões com sucursais VPN/SDWAN. Esta opção mostra o uso e ExpressRoute encerrado em Hong Kong e outros ramos ligados via SDWAN ou VPN. O ExpressRoute em Hong Kong está atualmente limitado a uma pequena lista de Fornecedores, que pode encontrar na lista de Parceiros de [Rota Expresso.](../expressroute/expressroute-locations-providers.md#global-commercial-azure)

![China para Hong Kong tráfego ExpressRoute](./media/interconnect-china/expressroute.png)

Existem também opções para encerrar o ExpressRoute da China, por exemplo, na Coreia do Sul ou no Japão. Mas, dada a conformidade, regulação e latência, Hong Kong é atualmente a melhor escolha.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opção 3: Apenas ExpressRoute

Esta secção discute um projeto onde o ExpressRoute é usado para Hong Kong e outros ramos. Esta opção mostra a interligação utilizando o ExpressRoute em ambas as extremidades. Aqui tens um fluxo de tráfego diferente do outro. O tráfego Microsoft 365 fluirá para o hub virtual WAN virtual Azure e daí para a Microsoft Edge Network e para a Internet.

O tráfego que vai para os ramos interligados ou deles para os locais na China seguirá uma abordagem diferente dentro dessa arquitetura. Atualmente, a WAN virtual não suporta o trânsito ExpressRoute para o ExpressRoute. O tráfego irá alavancar o ExpressRoute Global Reach ou a 3ª Parte interligar-se sem passar no hub virtual wan. Passará diretamente de um Microsoft Enterprise Edge (MSEE) para outro.

![Alcance Global do ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

Atualmente o ExpressRoute Global Reach não está disponível em todos os países/regiões, mas pode configurar uma solução usando a Azure Virtual WAN.

Pode, por exemplo, configurar um ExpressRoute com o Microsoft Peering e ligar um túnel VPN através desse que espreita o Azure Virtual WAN. Agora, permitiu, mais uma vez, o trânsito entre a VPN e a ExpressRoute sem Global Reach e o fornecedor e serviço de terceiros, como o Megaport Cloud.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais informações:

* [Arquitetura de rede de trânsito global com Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Criar um hub VIRTUAL WAN](virtual-wan-site-to-site-portal.md)

* [Configure um hub virtual WAN seguro](../firewall-manager/secure-cloud-network.md)

* [Visão geral do serviço de peering Azure](https://docs.microsoft.com/azure/peering-service/about)
