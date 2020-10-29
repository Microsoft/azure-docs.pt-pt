---
title: Descrição geral dos serviços de rede do Azure
description: Conheça os serviços de networking em Azure, incluindo conectividade, proteção de aplicações, entrega de aplicações e serviços de monitorização da rede.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: f49a340a004a4aef37bcae9e3ae1c2b02ae030b9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913096"
---
# <a name="azure-networking-services-overview"></a>Descrição geral dos serviços de rede do Azure

Os serviços de networking em Azure fornecem uma variedade de capacidades de networking que podem ser usadas em conjunto ou separadamente. Clique em qualquer uma das seguintes capacidades-chave para saber mais sobre eles:
- [**Serviços de conectividade**](#connect): Conecte recursos Azure e recursos no local utilizando qualquer ou uma combinação destes serviços de rede em Azure - Rede Virtual (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Virtual network NAT Gateway, Azure DNS, Serviço de Peering e Azure Bastion.
- [**Serviços de proteção de aplicações**](#protect): Proteja as suas aplicações utilizando qualquer ou uma combinação destes serviços de rede em Azure - Private Link, proteção DDoS, Firewall, Grupos de Segurança de Rede, Firewall de Aplicações Web e Pontos Finais de Rede Virtual.
- [**Serviços de entrega de aplicações**](#deliver): Entregar aplicações na rede Azure utilizando qualquer ou uma combinação destes serviços de networking em Azure - Rede de Entrega de Conteúdos (CDN), Serviço de Porta Frontal Azure, Gestor de Tráfego, Gateway de Aplicação, Analisador de Internet e Balanceador de Carga.
- [**Monitorização da rede**](#monitor): Monitorize os seus recursos de rede utilizando qualquer ou uma combinação destes serviços de rede em Azure - Monitor de Rede, Monitor ExpressRoute, Monitor Azure ou Ponto de Acesso ao Terminal VNet (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Serviços de conectividade
 
Esta secção descreve serviços que fornecem conectividade entre recursos Azure, conectividade de uma rede no local para recursos Azure, e sucursal para sucursal conectividade em Azure - Rede Virtual (VNet), ExpressRoute, VPN Gateway, Virtual WAN, Virtual network NAT Gateway, Azure DNS, Azure Peering e Azure Bastion.


### <a name="virtual-network"></a><a name="vnet"></a>Rede virtual

A Azure Virtual Network (VNet) é o bloco de construção fundamental para a sua rede privada em Azure. Pode utilizar um VNets para:
- **Comunicar entre recursos Azure** : Pode implementar VMs e vários outros tipos de recursos Azure para uma rede virtual, como os Ambientes de Serviço de Aplicações Azure, o Serviço Azure Kubernetes (AKS) e conjuntos de escala de máquina virtual Azure. Para ver uma lista completa dos recursos do Azure que pode implementar numa rede virtual, veja [Integração de serviço da rede virtual](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicar entre si** : Pode ligar redes virtuais entre si, permitindo que os recursos em qualquer rede virtual se comuniquem entre si, utilizando o olhar de rede virtual. As redes virtuais a que liga podem estar nas mesmas regiões ou em regiões diferentes do Azure. Para obter mais informações, consulte [a rede Virtual a espreitar.](../virtual-network/virtual-network-peering-overview.md)
- **Comunicar à internet:** Todos os recursos num VNet podem comunicar saída para a internet, por padrão. Pode comunicar com um recurso à entrada, ao atribuir-lhe um endereço IP público ou um Balanceador de Carga público. Também pode utilizar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ou [Balancer](../load-balancer/load-balancer-overview.md) de Carga pública para gerir as suas ligações de saída.
- **Comunicar com redes no local** : Pode ligar os computadores e redes no local a uma rede virtual utilizando o [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o [ExpressRoute](../expressroute/expressroute-introduction.md).

Para mais informações, veja [o que é a Rede Virtual Azure?](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
O ExpressRoute permite-lhe estender as suas redes no local para a nuvem da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Esta ligação é privada. O tráfego não é transmitido pela Internet. Com o ExpressRoute, pode estabelecer ligação aos serviços cloud da Microsoft, como o Microsoft Azure, o Microsoft 365 e o Dynamics 365.  Para mais informações, veja [o que é ExpressRoute?](../expressroute/expressroute-introduction.md)

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>Gateway de VPN
O VPN Gateway ajuda-o a criar ligações cruzadas encriptadas à sua rede virtual a partir de locais no local ou a criar ligações encriptadas entre VNets. Existem configurações diferentes disponíveis para ligações VPN Gateway, tais como, site-to-site, ponto a site ou VNet-to-VNet.
O diagrama seguinte ilustra várias ligações VPN site-to-site à mesma rede virtual.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Azure ExpressRoute":::

Para obter mais informações sobre diferentes tipos de ligações VPN, consulte [o Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>WAN Virtual
Azure Virtual WAN é um serviço de networking que fornece conectividade de ramo otimizado e automatizado para, e através, Azure. As regiões de Azure servem como centros aos quais pode escolher ligar os seus ramos. Você pode aproveitar a espinha dorsal Azure para também conectar ramos e desfrutar da conectividade branch-to-VNet. A Azure Virtual WAN reúne muitos serviços de conectividade em nuvem Azure, tais como VPN site-to-site, ExpressRoute, VPN de utilizador ponto-a-local numa única interface operacional. A conectividade com os VNets Azure é estabelecida utilizando ligações de rede virtuais. Para mais informações, veja [o que é Azure virtual WAN?](../virtual-wan/virtual-wan-about.md)

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Azure ExpressRoute":::

### <a name="azure-dns"></a><a name="dns"></a>DNS do Azure
O DNS do Azure é um serviço de alojamento para domínios DNS que fornece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. Para mais informações, veja [o que é Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
O serviço Azure Bastion é um novo serviço PaaS totalmente gerido pela plataforma que fornece dentro da sua rede virtual. Fornece conectividade RDP/SSH segura e sem emenda às suas máquinas virtuais diretamente no portal Azure sobre TLS. Ao ligar-se através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público. Para mais informações, veja [o que é Azure Bastion?](../bastion/bastion-overview.md)

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure ExpressRoute":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Rede virtual NAT Gateway
O VIRTUAL Network NAT (tradução de endereços de rede) simplifica a conectividade de saída da Internet para redes virtuais. Quando configurados numa sub-rede, toda a conectividade de saída utiliza os seus endereços IP públicos estáticos especificados. A conectividade de saída é possível sem o balanceador de carga ou endereços IP públicos diretamente ligados a máquinas virtuais. Para mais informações, veja [o que é a rede virtual NAT gateway?](../virtual-network/nat-overview.md)

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Azure ExpressRoute":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Serviço de Peering Azure
O serviço Azure Peering melhora a conectividade do cliente com os serviços na nuvem da Microsoft, tais como o Microsoft 365, Dynamics 365, software como serviço (SaaS), Azure, ou quaisquer serviços da Microsoft acessíveis através da internet pública. Para mais informações, veja [o que é o Serviço de Espreitamento Azure?](../peering-service/about.md)

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

Azure Edge Zone é uma família de ofertas da Microsoft Azure que permite o processamento de dados perto do utilizador. Pode implantar VMs, contentores e outros serviços Azure selecionados em Edge Zones para responder aos baixos requisitos de latência e de produção elevada das aplicações. Para mais informações, veja [o que é Azure Edge Zones?](edge-zones-overview.md)

### <a name="azure-orbital"></a><a name="orbital"></a>Azure Orbital

O Azure Orbital é uma estação terrestre totalmente gerida com base em nuvem como um serviço que permite comunicar com as suas constelações de naves espaciais ou satélites, dados de downlink e uplink, processar os seus dados na nuvem, serviços em cadeia com serviços Azure em cenários únicos e gerar produtos para os seus clientes. Este sistema é construído em cima da infraestrutura global Azure e da rede global de fibra de baixa latência. Para mais informações, veja [o que é Azure Orbital?](azure-orbital-overview.md)

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Azure ExpressRoute":::

## <a name="application-protection-services"></a><a name="protect"></a>Serviços de proteção de aplicações

Esta secção descreve serviços de networking em Azure que ajudam a proteger os seus recursos de rede - Proteja as suas aplicações utilizando qualquer ou uma combinação destes serviços de rede em Azure - proteção DDoS, Link Privado, Firewall, Firewall, Web Application Firewall, Grupos de Segurança de Rede e Endpoints de Serviço de Rede Virtual.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDos Proteção 
[A Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) fornece contramedidas contra as ameaças mais sofisticadas do DDoS. O serviço fornece capacidades de mitigação de DDoS melhoradas para a sua aplicação e recursos implantados nas suas redes virtuais. Além disso, os clientes que usam a Azure DDoS Protection têm acesso ao suporte DDoS Rapid Response para envolver especialistas em DDoS durante um ataque ativo.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="Azure ExpressRoute":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[O Azure Private Link](../private-link/private-link-overview.md) permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Azure Storage e SQL Database) e à Azure aloja serviços de propriedade do cliente/parceiro sobre um ponto final privado na sua rede virtual.
O tráfego entre a sua rede virtual e o serviço percorre a rede de espinha dorsal da Microsoft. Expor o seu serviço à internet pública já não é necessário. Pode criar o seu próprio serviço de ligação privada na sua rede virtual e entregá-lo aos seus clientes.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Azure ExpressRoute":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. Utilizando o Azure Firewall, pode criar, impor e registar políticas de aplicação e conectividade de rede em subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual. 

Para obter mais informações sobre o Azure Firewall, consulte a documentação do [Azure Firewall](../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Azure ExpressRoute":::

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall de Aplicação Web
[O Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) fornece proteção às suas aplicações web a partir de explorações web comuns e vulnerabilidades, tais como injeção de SQL e scripts de sites cruzados. A Azure WAF fornece proteção fora da caixa das 10 principais vulnerabilidades da OWASP através de regras geridas. Além disso, os clientes também podem configurar regras personalizadas, que são regras geridas pelo cliente para fornecer proteção adicional com base na gama IP de origem, e solicitar atributos como cabeçalhos, cookies, formar campos de dados ou parâmetros de cadeia de consulta.

Os clientes podem optar por implantar [a Azure WAF com o Application Gateway,](../application-gateway/waf-overview.md) que fornece proteção regional às entidades no espaço de endereços públicos e privados. Os clientes também podem optar por implementar [a Azure WAF com Porta Frontal,](../frontdoor/waf-overview.md) que fornece proteção na borda da rede para os pontos finais públicos.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Azure ExpressRoute":::

### <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede
Pode filtrar o tráfego de rede de e para recursos do Azure numa rede virtual do Azure com um grupo de segurança de rede. Para obter mais informações, consulte [os grupos de segurança da Rede.](../virtual-network/network-security-groups-overview.md)

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Pontos finais de serviço
Os pontos finais de serviço da Rede Virtual (VNet) expandem o seu espaço de endereços privados de rede virtual e da identidade da sua VNet para os serviços do Azure através de uma ligação direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O tráfego da sua VNet para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure. Para obter mais informações, consulte [os pontos finais do serviço de rede Virtual.](../virtual-network/virtual-network-service-endpoints-overview.md)

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Azure ExpressRoute":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Serviços de entrega de aplicativos

Esta secção descreve os serviços de networking em Azure que ajudam a entregar aplicações - Rede de Entrega de Conteúdos, Serviço de Porta Frontal Azure, Gestor de Tráfego, Balanceador de Carga e Gateway de Aplicações.

### <a name="content-delivery-network"></a><a name="cdn"></a>Rede de Entrega de Conteúdos
A Rede de Entrega de Conteúdos (CDN) oferece aos programadores uma solução global para o fornecimento rápido de conteúdo de largura de banda alta aos utilizadores, ao colocar em cache o respetivo conteúdo em nós físicos estrategicamente colocados em todo o mundo. Para obter mais informações sobre a Azure CDN, consulte [a Rede de Entrega de Conteúdos Azure](../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure ExpressRoute":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do tráfego da Web ao otimizar para o melhor desempenho e ativação pós-falha instantânea para uma elevada disponibilidade. Com o Front Door, pode transformar as suas aplicações empresariais e para consumidores globais (múltiplas regiões) em aplicações, APIs e conteúdos robustos, modernos e personalizados, com um elevado desempenho e que alcancem uma audiência global com o Azure. Para mais informações, consulte [a Porta frontal Azure.](../frontdoor/front-door-overview.md)

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Azure ExpressRoute":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gestor de Tráfego

O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta. O Gestor de Tráfego fornece uma gama de métodos de encaminhamento de tráfego para distribuir tráfego, tais como prioridade, ponderação, desempenho, geográfico, multi-valor ou sub-rede. Para obter mais informações sobre os métodos de encaminhamento de tráfego, consulte [os métodos de encaminhamento do Gestor de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

O diagrama que se segue mostra o encaminhamento baseado na prioridade do ponto final com o Gestor de Tráfego:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Azure ExpressRoute":::

Para mais informações sobre o Gestor de Tráfego, veja [o que é O Gestor de Tráfego da Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load balancer
O Azure Load Balancer proporciona um equilíbrio de carga de alto desempenho e baixa latência camada 4 para todos os protocolos UDP e TCP. Gere ligações de entrada e saída. Pode configurar pontos finais públicos e internos equilibrados em carga. Pode definir regras para mapear ligações de entrada para destinos de piscina back-end, utilizando opções de sondagem de saúde TCP e HTTP para gerir a disponibilidade do serviço. Para saber mais sobre o Balanceador de Carga, leia o artigo de visão geral do [Balancer de Carga.](../load-balancer/load-balancer-overview.md)

A seguinte imagem mostra uma aplicação multi-camadas virada para a Internet que utiliza os equilibradores de carga externos e internos:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Azure ExpressRoute":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway de Aplicação
O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web. É um Controlador de Entrega de Aplicações (ADC) como um serviço, oferecendo várias capacidades de equilíbrio de carga de camada 7 para as suas aplicações. Para mais informações, veja [o que é O Gateway de Aplicação Azure?](../application-gateway/overview.md)

O diagrama seguinte mostra o encaminhamento baseado em url path com o Application Gateway.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Azure ExpressRoute":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Serviços de monitorização da rede
Esta secção descreve os serviços de networking em Azure que ajudam a monitorizar os recursos da sua rede - Observador de Rede, Monitor Azure para redes, Monitor ExpressRoute, Monitor Azure e Rede Virtual TAP.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Observador de Rede
O Observador de Rede do Azure oferece ferramentas para monitorizar, diagnosticar, ver métricas e ativar ou desativar registos de recursos numa rede virtual do Azure. Para mais informações, consulte [o que é o Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)

### <a name="azure-monitor-for-networks-preview"></a>Monitor Azure para visualização de redes
O Azure Monitor for Networks proporciona uma visão abrangente da saúde e métricas para todos os recursos de rede implantados, sem necessidade de qualquer configuração. Também fornece acesso a capacidades de monitorização de rede como [Connection Monitor,](../network-watcher/connection-monitor-preview.md) [registo de fluxos para grupos de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-overview.md), e Traffic [Analytics](../network-watcher/traffic-analytics.md). Para obter mais informações, consulte [o Azure Monitor for Networks Preview](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
Para saber como ver as métricas do circuito ExpressRoute, registos de recursos e alertas, consulte [a monitorização, métricas e alertas ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
O Azure Monitor maximiza a disponibilidade e o desempenho das aplicações ao disponibilizar uma solução abrangente para recolher, analisar e agir na telemetria dos ambientes na cloud e no local. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem. Para mais informações, consulte [a Visão Geral do Monitor Azure](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Rede Virtual TAP
A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da rede de máquinas virtuais para um coletor de pacotes de rede ou uma ferramenta de análise. A ferramenta de coletor ou de análise é fornecida por um parceiro [de aparelho virtual de rede.](https://azure.microsoft.com/solutions/network-appliances/)

A imagem a seguir mostra como funciona a rede virtual TAP:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Azure ExpressRoute":::

Para mais informações, consulte [O que é a Rede Virtual TAP.](../virtual-network/virtual-network-tap-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Crie a sua primeira rede virtual e conecte alguns VMs a ela, completando os passos no Criar o seu primeiro artigo [de rede virtual.](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
- Ligue o computador a uma rede virtual completando os passos no [Configure um artigo de ligação ponto-a-local](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Carregue o tráfego de Internet para servidores públicos completando os passos no artigo Do equilíbrio de carga virado para a [Internet.](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
