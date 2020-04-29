---
title: Interligação com a China usando O WAN Virtual Azure e o hub seguro
description: Saiba mais sobre a conectividade automática de ramo-a-filial virtual WAN, regiões disponíveis e parceiros.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985631"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Interligação com a China usando Azure Virtual WAN e Secure Hub

Quando se olha para o automóvel comum, indústrias de fabrico, logística ou outros institutos como embaixadas, há muitas vezes a questão de como melhorar a interligação com a China. Estas melhorias são relevantes para a utilização de Serviços cloud como o Office 365, Azure Global Services ou interligações dentro da China com uma espinha dorsal do cliente.

Na maioria dos casos, os clientes estão a ter dificuldades com altas latências, baixa largura de banda, ligação instável e custos elevados de ligação fora da China (por exemplo, Europa ou Estados Unidos).

Uma das razões para estas lutas é a "Grande Firewall da China", que protege a parte chinesa da Internet e filtra o tráfego para a China. Quase todo o tráfego que vai da China Continental para fora da China, exceto as zonas de administração especial como Hong Kong e Macau, passa a Grande Firewall. O tráfego que atravessa Hong Kong e Macau não atinge a Grande Firewall em força, é tratado por um subconjunto da Grande Firewall.

![Interligação do fornecedor](./media/interconnect-china/provider.png)

Utilizando o Virtual WAN, um cliente pode estabelecer uma ligação mais performativa e estável aos Serviços Microsoft Cloud e uma ligação à sua rede empresarial sem violar a lei chinesa de cibersegurança.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>Requisitos e fluxo de trabalho

Se quiser manter-se em conformidade com a lei chinesa de cibersegurança, precisa de cumprir um conjunto de certas condições.

Em primeiro lugar, é necessário trabalhar em conjunto com uma rede e um ISP que detém uma licença ICP (Internet Content Provider) para a China. Na maioria dos casos, acabará com um dos seguintes fornecedores:

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

Dependendo do fornecedor e das suas necessidades, agora precisa de adquirir um dos seguintes serviços de conectividade da rede para interligar as suas agências dentro da China.

* Rede MPLS/IPVPN 
* Um software definido WAN (SDWAN)
* Acesso dedicado à Internet

Em seguida, você precisa concordar com esse fornecedor para dar uma fuga para a Microsoft Global Network e sua Edge Network em Hong Kong, não em Pequim ou Xangai. Neste caso, Hong Kong é muito importante devido à sua ligação física e localização à China.

Embora a maioria dos clientes pense que usar Singapura para interligação é o melhor caso porque parece mais próximo da China quando olha para o mapa, isso não é verdade. Quando segue os mapas de fibra de rede, quase todas as ligações de rede passam por Pequim, Xangai e Hong Kong. Isto faz de Hong Kong uma melhor escolha de localização para interligar-se à China.

Dependendo do fornecedor, poderá obter diferentes ofertas de serviços. O quadro abaixo mostra um exemplo de prestadores e do serviço que oferecem, com base em informações no momento em que este artigo foi escrito.

| Serviço | Exemplos de fornecedores |
| --- | --- |
| Rede MPLS/IPVPN |PCCW, China Telecom Global |
|Rio SDWAN| PCCW, China Telecom Global|
| Acesso dedicado à Internet | PCCW, Hong Kong Telecom, China Mobil|

Com o seu fornecedor, pode chegar a acordo sobre qual das duas soluções a utilizar para alcançar a espinha dorsal global da Microsoft:

* Ter uma Microsoft Azure ExpressRoute terminada em Hong Kong. Seria o caso da utilização do MPLS/IPVPN. Atualmente, apenas o único fornecedor de licenças ICP com ExpressRoute para Hong Kong é a China Telecom Global. No entanto, também podem falar com os outros fornecedores se alavancarem fornecedores de cloud exchange como o Megaport ou o InterCloud. Para mais informações, consulte os fornecedores de [conectividade ExpressRoute.](../expressroute/expressroute-locations-providers.md#partners)

* Utilizando um Acesso de Internet dedicado diretamente num dos seguintes Pontos de Troca de Internet, ou utilizando uma interligação de rede privada.

A seguinte lista mostra as trocas de Internet possíveis em Hong Kong:

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

Ao utilizar esta ligação, o seu próximo bGP hop para os Serviços Microsoft deve ser o Microsoft Autonomous System Number (AS#) 8075. Se utilizar uma única localização ou solução SDWAN, essa seria a escolha da ligação.

De qualquer forma, recomendamos que tenha uma segunda e regular Fuga de Internet para o Continente Chinês. Isto é para dividir o tráfego entre o tráfego empresarial para serviços na nuvem como o Microsoft 365 e o Azure, e o tráfego regulado por lei na Internet.

Uma arquitetura de rede compatível dentro da China poderia parecer o seguinte exemplo:

![Vários ramos](./media/interconnect-china/multi-branch.png)

Neste exemplo, tendo uma interligação com a Microsoft Global Network em Hong Kong, pode agora começar a alavancar a [Azure Virtual WAN Global Transit Architecture](virtual-wan-global-transit-network-architecture.md) e serviços adicionais, como o Azure secure Virtual WAN hub, de forma a consumir serviços e interligar-se aos seus balcões e datacenter fora da China.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>Comunicação hub-to-hub

Nesta secção, usamos a comunicação virtual WAN hub-to-hub para interligar. Neste cenário, você cria um novo recurso virtual WAN hub para se conectar a um centro virtual WAN em Hong Kong, outras regiões que você prefere, uma região onde você já tem recursos Azure, ou onde quer se conectar.

Uma arquitetura de amostra pode parecer o seguinte exemplo:

![Amostra WAN](./media/interconnect-china/sample.png)

Neste exemplo, os ramos da China ligam-se à Azure Cloud China e entre si utilizando ligações VPN ou MPLS. As sucursais que precisam de ser ligadas aos Serviços Globais utilizam serviços MPLS ou baseados na Internet que estão ligados diretamente a Hong Kong. Se quiser utilizar a ExpressRoute em Hong Kong e noutra região, precisa de configurar o [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) para interligar ambos os Circuitos ExpressRoute.

O ExpressRoute Global Reach não está disponível em algumas regiões. Se precisa de interligação com o Brasil ou com a Índia, por exemplo, precisa de aproveitar os [Fornecedores](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) de Cloud Exchange para fornecer os serviços de encaminhamento.

A figura abaixo mostra ambos os exemplos para este cenário.

![Alcance Global](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Fuga de Internet segura para o Office 365

Outra consideração é a segurança da rede, bem como o registo do ponto de entrada entre a China e o componente virtual wan estabelecido da espinha dorsal, e a espinha dorsal do cliente. Na maioria dos casos, é necessário que a Internet em Hong Kong chegue diretamente à Microsoft Edge Network e, com isso, os Servidores de Porta Frontal Azure utilizados para os Serviços Microsoft 365.

Para ambos os cenários com Wan Virtual, você aproveitaria o [hub seguro Azure Virtual WAN](../firewall-manager/secured-virtual-hub.md). Utilizando o Azure Firewall Manager, pode alterar um hub virtual WAN regular para um hub seguro e, em seguida, implementar e gerir uma Firewall Azure dentro desse hub.

A figura que se segue mostra um exemplo deste cenário:

![Fuga de internet para tráfego de serviços Web e Microsoft](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>Arquitetura e fluxos de tráfego

Dependendo da sua escolha em relação à ligação a Hong Kong, a arquitetura geral pode mudar ligeiramente. Esta secção mostra três arquiteturas disponíveis em combinação diferente com VPN ou SDWAN e/ou ExpressRoute.

Todas estas opções utilizam o hub seguro Azure Virtual WAN para a conectividade direta M365 em Hong Kong. Estas arquiteturas também suportam os requisitos de conformidade para o [Office 365 Multi-Geo](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) e mantêm esse tráfego perto da próxima localização da Porta frontal do Office 365. Como resultado, é também uma melhoria para o uso do Microsoft 365 fora da China.

Ao utilizar o Azure Virtual WAN juntamente com as ligações à Internet, todas as ligações podem beneficiar de serviços adicionais como o [Microsoft Azure Peering Services (MAPS)](https://docs.microsoft.com/azure/peering-service/about). O MAPS foi construído para otimizar o tráfego que chega à Microsoft Global Network a partir de Fornecedores de Serviços de Internet da 3ª Parte.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>Opção 1: SDWAN ou VPN

Esta secção discute um design que utiliza SDWAN ou VPN para Hong Kong e para outros ramos. Esta opção mostra o uso e o fluxo de tráfego ao utilizar uma conexão de Internet pura em ambos os sites da espinha dorsal Virtual WAN. Neste caso, a ligação é trazida para Hong Kong utilizando acesso dedicado à Internet, ou uma solução SDWAN fornecedor do ICP. Outros ramos também estão a usar soluções puras de Internet ou SDWAN.

![China para o tráfego de Hong Kong](./media/interconnect-china/china-traffic.png)

Nesta arquitetura, todos os sites estão ligados à Microsoft Global Network utilizando VPN e Azure Virtual WAN. O tráfego entre os sites e Hong Kong é transmitido na Rede Microsoft e apenas utiliza conexão regular de Internet na última milha.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>Opção 2: ExpressRoute e SDWAN ou VPN

Esta secção discute um design que utiliza a ExpressRoute em Hong Kong e outros Balcões com Filiais VPN/SDWAN. Esta opção mostra o uso e expressRoute terminada em Hong Kong e outros balcões conectados via SDWAN ou VPN. A ExpressRoute em Hong Kong está atualmente limitada a uma pequena lista de Fornecedores, que pode encontrar na lista de Parceiros de [Rota Expresso.](../expressroute/expressroute-locations-providers.md#global-commercial-azure)

![China para Hong Kong tráfego ExpressRoute](./media/interconnect-china/expressroute.png)

Existem também opções para encerrar a ExpressRoute da China, por exemplo, na Coreia do Sul ou no Japão. Mas, dado o cumprimento, regulação e latência, Hong Kong é atualmente a melhor escolha.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>Opção 3: Apenas ExpressRoute

Esta secção discute um design que onde o ExpressRoute é usado para Hong Kong e outros Ramos. Esta opção mostra a interligação utilizando o ExpressRoute em ambas as extremidades. Aqui tem um fluxo de tráfego diferente do outro. O tráfego microsoft 365 fluirá para o hub seguro wan virtual Azure e daí para a Microsoft Edge Network e a Internet.

O tráfego que vai para os ramos interligados ou deles para os locais na China seguirá uma abordagem diferente dentro dessa arquitetura. Atualmente o WAN virtual não suporta o expressroute para o trânsito ExpressRoute. O tráfego irá alavancar o ExpressRoute Global Reach ou a 3ª Parte interconecta-se sem passar o wan hub virtual. Fluirá diretamente de um Microsoft Enterprise Edge (MSEE) para outro.

![Alcance Global do ExpressRoute](./media/interconnect-china/expressroute-virtual.png)

Atualmente o ExpressRoute Global Reach não está disponível em todos os países, mas pode configurar uma solução usando o Azure Virtual WAN.

Pode, por exemplo, configurar uma Via Expressa com o Microsoft Peering e ligar um túnel VPN através desse olhar para o Azure Virtual WAN. Agora permitiu, mais uma vez, o trânsito entre a VPN e a ExpressRoute sem o Global Reach e o terceiro fornecedor e serviço de terceiros, como o Megaport Cloud.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais informações:

* [Arquitetura da rede de trânsito global com O WAN Virtual Azure](virtual-wan-global-transit-network-architecture.md)

* [Criar um centro virtual wan](virtual-wan-site-to-site-portal.md)

* [Configure um centro seguro virtual WAN](../firewall-manager/secure-cloud-network.md)

* [Visão geral do serviço de peering Azure](https://docs.microsoft.com/azure/peering-service/about)
