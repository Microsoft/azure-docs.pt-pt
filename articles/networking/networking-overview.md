---
title: Visão geral dos serviços de networking Azure
description: Conheça os serviços de networking em Azure e as suas capacidades - serviços de conectividade, serviços de proteção de aplicações, serviços de entrega de aplicações e monitorização da rede.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 90eddea839d2f6ae5235ac6a391b40dd667ab401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257840"
---
# <a name="azure-networking-services-overview"></a>Visão geral dos serviços de networking Azure

Os serviços de networking em Azure fornecem uma variedade de capacidades de networking que podem ser usadas em conjunto ou separadamente. Clique em qualquer uma das seguintes capacidades-chave para saber mais sobre elas:
- [**Serviços de conectividade**](#connect): Connect Azure recursos e recursos no local utilizando qualquer ou uma combinação destes serviços de networking em Azure - Rede Virtual (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Rede Virtual NAT Gateway, Azure DNS, serviço de peering e Bastião Azure.
- [**Serviços de proteção de aplicações**](#protect) Proteja as suas aplicações utilizando qualquer ou uma combinação destes serviços de rede em Azure - Private Link, proteção DDoS, Firewall, Network Security Groups, Firewall application firewall e pontos finais de rede virtuais.
- [**Serviços de entrega**](#deliver) de pedidos Entregar aplicações na rede Azure utilizando qualquer ou uma combinação destes serviços de networking em Azure - Rede de Entrega de Conteúdos (CDN), Serviço de Porta Frontal Azure, Gestor de Tráfego, Gateway de Aplicações, Analisador de Internet e Balancer de Carga.
- [**Monitorização da rede**](#monitor) – Monitorize os seus recursos de rede utilizando qualquer ou uma combinação destes serviços de rede em Azure - Network Watcher, ExpressRoute Monitor, Azure Monitor ou VNet Terminal Access Point (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Serviços de conectividade
 
Esta secção descreve serviços que proporcionam conectividade entre os recursos do Azure, conectividade de uma rede no local para recursos Azure, e sucursal para a conectividade de sucursais em Azure - Rede Virtual (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Rede virtual NAT Gateway, Azure DNS, serviço De Peering Azure e Bastião Azure.

|Serviço|Por que usar?|Cenários|
|---|---|---|
|[Rede virtual](#vnet)|Permite que os recursos do Azure se comuniquem de forma segura entre si, com a internet e com as redes no local.| <p>[Filtre o tráfego de rede](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Encaminhe o tráfego de rede](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Restrinja o acesso da rede aos recursos](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Ligar redes virtuais](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Estende as suas redes no local para a nuvem da Microsoft sobre uma ligação privada facilitada por um fornecedor de conectividade.|<p>[Criar e modificar um circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Criar e modificar o peering de um circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Ligar uma VNet a um circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configure e gerencie filtros de rotas para circuitos ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Envia tráfego encriptado entre uma rede virtual Azure e uma localização no local através da Internet pública.|<p>[Ligações site-a-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Ligações VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Ligações ponto-a-local](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[WAN Virtual](#virtualwan)|Otimiza e automatiza a conectividade do ramo para, e através, Azure. As regiões azure servem como centros aos quais pode optar por ligar os seus ramos.|<p>[Ligações site-to-site](../virtual-wan/virtual-wan-site-to-site-portal.md), [conexões ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Os anfitriões de domínios DNS que fornecem resolução de nomes utilizando a infraestrutura do Microsoft Azure.|<p>[Aloje o seu domínio no DNS do Azure](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Crie registos DNS para uma aplicação web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Criar um recorde de pseudónimos para O Gestor de Tráfego](../dns/tutorial-alias-tm.md)</p> <p>[Criar um registo de pseudónimos para endereço IP público](../dns/tutorial-alias-pip.md)</p> <p>[Criar um recorde de pseudónimos para o registo de recursos de zona](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Configure a conectividade RDP/SSH segura e totalmente integrada às suas máquinas virtuais diretamente no portal do Azure por SSL. Quando se conecta através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público|<p>[Criar um anfitrião do Bastião Azure](../bastion/bastion-create-host-portal.md)</p><p>[Ligue usando sSH a um Linux VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[Conecte-se usando RDP a um VM do Windows](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Rede virtual NAT Gateway](#nat)|Crie um portal NAT para fornecer conectividade de saída para uma máquina virtual.|<p>[Criar um Portal NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Serviço de Peering Azure (Pré-visualização)](#azurepeeringservice)|Colabore com fornecedores de serviços para um encaminhamento ideal e fiável para a nuvem da Microsoft sobre a rede pública.|<p>[Registre serviço de peering Azure](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Rede virtual

A Rede Virtual Azure (VNet) é o bloco de construção fundamental da sua rede privada em Azure. Pode usar um VNets para:
- **Comunicar entre os recursos do Azure**: Pode implantar VMs e vários outros tipos de recursos Azure para uma rede virtual, como o Azure App Service Environments, o Serviço Azure Kubernetes (AKS) e conjuntos de escala de máquinas virtuais Azure. Para ver uma lista completa dos recursos do Azure que pode implementar numa rede virtual, veja [Integração de serviço da rede virtual](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicar entre si:** Pode ligar redes virtuais entre si, permitindo que os recursos em qualquer uma das redes virtuais se comuniquem entre si, utilizando o epeering virtual da rede. As redes virtuais a que liga podem estar nas mesmas regiões ou em regiões diferentes do Azure. Para mais informações, consulte o peering da [rede Virtual.](../virtual-network/virtual-network-peering-overview.md)
- **Comunicar à internet:** Todos os recursos de um VNet podem comunicar com saída à internet, por padrão. Pode comunicar com um recurso à entrada, ao atribuir-lhe um endereço IP público ou um Balanceador de Carga público. Também pode utilizar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ou [balanceadores](../load-balancer/load-balancer-overview.md) públicos de carga para gerir as suas ligações de saída.
- **Comunicar com redes no local:** Pode ligar os computadores e redes no local a uma rede virtual utilizando [o VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [o ExpressRoute](../expressroute/expressroute-introduction.md).

Para mais informações, consulte o que é a [Rede Virtual Azure?](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
O ExpressRoute permite-lhe estender as suas redes no local para a nuvem da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Esta ligação é privada. O tráfego não é transmitido pela Internet. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365.  Para mais informações, consulte [o que é expressRoute?](../expressroute/expressroute-introduction.md)

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
O VPN Gateway ajuda-o a criar ligações transversais encriptadas à sua rede virtual a partir de locais no local ou a criar ligações encriptadas entre VNets. Existem diferentes configurações disponíveis para ligações VPN Gateway, tais como, site-to-site, ponto-a-site ou VNet para VNet.
O diagrama seguinte ilustra várias ligações VPN site-to-site para a mesma rede virtual.

![Ligações de Gateway VpN do Site-a-Site](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Para obter mais informações sobre diferentes tipos de ligações VPN, consulte [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>WAN Virtual
O Azure Virtual WAN é um serviço de networking que fornece conectividade de filial otimizada e automatizada para, e através, Azure. As regiões azure servem como centros aos quais pode optar por ligar os seus ramos. Você pode alavancar a espinha dorsal Azure para também ligar ramos e desfrutar da conectividade ramificação-VNet. O Azure Virtual WAN reúne muitos serviços de conectividade em nuvem Azure, tais como VPN, ExpressRoute, utilizador ponto-a-site VPN numa única interface operacional. A conectividade com o Azure VNets é estabelecida utilizando ligações de rede virtuais. Para mais informações, consulte [o que é O Wan virtual do Azure?](../virtual-wan/virtual-wan-about.md)

![Diagrama da WAN Virtual](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
O DNS do Azure é um serviço de alojamento dos domínios DNS que oferece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. Para mais informações, consulte [o que é Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
O serviço Azure Bastion é um novo serviço PaaS totalmente gerido pela plataforma que você disponibiliza dentro da sua rede virtual. Fornece conectividade RDP/SSH segura e sem emenda às suas máquinas virtuais diretamente no portal Azure sobre o SSL. Ao ligar-se através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público. Para mais informações, veja [o que é Azure Bastion?](../bastion/bastion-overview.md)

![Arquitetura De Bastião Azure](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Rede virtual NAT Gateway
A Rede Virtual NAT (tradução de endereços de rede) simplifica a conectividade de acesso à Internet apenas para redes virtuais. Quando configurado numa subnet, toda a conectividade de saída utiliza os seus endereços IP públicos estáticos especificados. A conectividade de saída é possível sem o equilíbrio de carga ou endereços IP públicos diretamente ligados a máquinas virtuais. Para mais informações, consulte o que é a [rede virtual NAT gateway?](../virtual-network/nat-overview.md) 

![Gateway na rede virtual NAT](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Serviço de Peering Azure
O serviço Azure Peering melhora a conectividade dos clientes com serviços na nuvem da Microsoft, como o Office 365, Dynamics 365, software como serviços (SaaS), Azure ou quaisquer serviços da Microsoft acessíveis através da internet pública. Para mais informações, consulte o que é o [Serviço de Peering Azure?](../peering-service/about.md)

## <a name="application-protection-services"></a><a name="protect"></a>Serviços de proteção de aplicações

Esta secção descreve serviços de networking em Azure que ajudam a proteger os seus recursos de rede - Proteja as suas aplicações utilizando qualquer ou uma combinação destes serviços de networking em Azure - Private Link, Proteção DDoS, Firewall, Network Security Groups, Web Firewall de aplicação e pontos finais de rede virtual.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Proteção DDoS](#ddosprotection) |Alta disponibilidade para as suas aplicações com proteção contra taxas de tráfego IP excedentárias|[Gerir a Proteção DDoS Azure](../virtual-network/manage-ddos-protection.md)|
|[Firewall de Aplicação Web](#waf)|<p>[Azure WAF com Application Gateway](../web-application-firewall/ag/ag-overview.md) fornece proteção regional a entidades em espaço de endereçopúblico e privado</p><p>[O Azure WAF com porta frontal](../web-application-firewall/afds/afds-overview.md) proporciona proteção na borda da rede para pontos finais públicos.</p>|<p>[Configure regras de proteção de bots](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurar código de resposta personalizado](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configure regras de restrição IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Regra limite de taxa de configuração](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall como um serviço com monitorização de estado com alta disponibilidade integrada e escalabilidade da cloud irrestrita.|<p>[Implementar uma Firewall Azure num Vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Implementar uma Firewall Azure numa rede híbrida](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrar tráfego de entrada com DNAT de Firewall Azure](../firewall/tutorial-firewall-dnat.md)</p>|
|[Grupos de segurança da rede](#nsg)|Controlo completo do nó final distribuído granular na VM/subnet para todos os fluxos de tráfego da rede|[Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)|
|[Pontos finais de serviço de rede virtual](#serviceendpoints)|Permite-lhe limitar o acesso à rede a alguns recursos de serviço sinuoso para uma subnet de rede virtual|[Restrict network access to PaaS resources](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md) (Restringir o acesso de rede a recursos de PaaS)|
[Ligação Privada](#privatelink)|Permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Armazenamento Azure e Base de Dados SQL) e o Azure acolheu serviços de clientes/parceiros sobre um ponto final privado na sua rede virtual.|<p>[Criar um ponto final privado](../private-link/create-private-endpoint-portal.md)</p><p>[Criar um serviço de Ligação Privada](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>Proteção ddos 
[A Proteção Azure DDoS](../virtual-network/manage-ddos-protection.md) fornece contramedidas contra as ameaças mais sofisticadas do DDoS. O serviço fornece capacidades de mitigação dDoS melhoradas para a sua aplicação e recursos implantados nas suas redes virtuais. Além disso, os clientes que usam a Proteção Azure DDoS têm acesso ao suporte dDoS Rapid Response para envolver especialistas em DDoS durante um ataque ativo.

![Proteção contra DDOS](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall de Aplicação Web

O Firewall de [Aplicações Web Azure](../web-application-firewall/overview.md) (WAF) fornece proteção às suas aplicações web a partir de explorações e vulnerabilidades comuns da web, como a injeção de SQL e scripts cross site. A Azure WAF fornece proteção contra caixas das 10 vulnerabilidades do top 10 da OWASP através de regras geridas. Além disso, os clientes também podem configurar regras personalizadas, que são regras geridas pelo cliente para fornecer proteção adicional com base na gama IP de origem, e solicitar atributos como cabeçalhos, cookies, campos de dados de formulário ou parâmetros de cadeia de consulta.

Os clientes podem optar por implantar [o Azure WAF com o Application Gateway](../application-gateway/waf-overview.md) que fornece proteção regional a entidades em espaço de endereços públicos e privados. Os clientes também podem optar por implantar [o Azure WAF com](../frontdoor/waf-overview.md) a Porta Da Frente, que fornece proteção na borda da rede para pontos finais públicos.

![Firewall de Aplicação Web](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. Utilizando o Firewall Azure, pode criar, impor e registar políticas de aplicação e conectividade de rede através de subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual. 

Para mais informações sobre o Azure Firewall, consulte a [documentação da Firewall Azure.](../firewall/overview.md)

![Descrição geral das firewalls](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança da rede
Pode filtrar o tráfego de rede de e para recursos do Azure numa rede virtual do Azure com um grupo de segurança de rede. Para mais informações, consulte a [Visão Geral da Segurança](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Pontos finais de serviço
Os pontos finais de serviço da Rede Virtual (VNet) expandem o seu espaço de endereços privados de rede virtual e da identidade da sua VNet para os serviços do Azure através de uma ligação direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O tráfego da sua VNet para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure. Para mais informações, consulte [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)do serviço de rede Virtual .

![Pontos finais de serviço de rede virtual](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[O Azure Private Link](../private-link/private-link-overview.md) permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Armazenamento Azure e Base de Dados SQL) e o Azure acolheu serviços de clientes/parceiros sobre um ponto final privado na sua rede virtual.
O tráfego entre a sua rede virtual e o serviço viaja pela rede de espinha dorsal da Microsoft. Expor o seu serviço à internet pública já não é necessário. Pode criar o seu próprio serviço de ligação privada na sua rede virtual e entregá-lo aos seus clientes.

![Visão geral do ponto final privado](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Serviços de entrega de pedidos

Esta secção descreve serviços de networking no Azure que ajudam a fornecer aplicações - Network Watcher, ExpressRoute Monitor, Azure Monitor ou VNet Terminal Access Point (TAP).

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Rede de Entrega de Conteúdos](#cdn)|Fornece conteúdo de largura de banda alta aos utilizadores. Os CDNs armazenam conteúdo em cache em servidores de borda em locais de ponto de presença (POP) que estão perto dos utilizadores finais, para minimizar a latência|<p>[Adicione CDN a uma aplicação web](../cdn/cdn-add-to-web-app.md)</p> <p>[- Aceder a bolhas de armazenamento utilizando um domínio personalizado Azure CDN sobre HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Adicione um domínio personalizado ao seu ponto final Do CDN Azure](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configure HTTPS on an Azure CDN custom domain](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate) (Configurar HTTPS num domínio personalizado da CDN do Azure)</p>|
|[Azure Front Door Service](#frontdoor)|Permite-lhe definir, gerir e monitorizar o encaminhamento global para o seu tráfego web, otimizando para o melhor desempenho e falha global instantânea para uma elevada disponibilidade.|<p>[Adicione um domínio personalizado ao seu serviço de porta frontal Azure](../frontdoor/front-door-custom-domain.md)</p> <p>[Configure HTTPS em um domínio personalizado porta da frente](../frontdoor/front-door-custom-domain-https.md)</p><p>[Configurar a política de firewall de aplicação web de geofiltração](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Gestor de Tráfego](#trafficmanager)|Distribui tráfego com base no DNS para serviços em todas as regiões globais do Azure, ao mesmo tempo que proporciona alta disponibilidade e capacidade de resposta|<p> [Encaminhar o tráfego de baixa latência](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Encaminhar o tráfego para um ponto final prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Controlar o tráfego com pontos de extremidade ponderados](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Tráfego de rota com base na localização geográfica do ponto final](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Encaminhar tráfego com base na sub-rede do utilizador](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Balanceador de Carga](#loadbalancer)|Fornece o equilíbrio regional de carga, encaminhando o tráfego através de zonas de disponibilidade e para os seus VNets. Fornece um equilíbrio interno de carga, encaminhando o tráfego através e entre os seus recursos para construir a sua aplicação regional.|<p> [Balancear carga de tráfego de internet para VMs](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Tráfego de equilíbrio de carga através de VMs dentro de uma rede virtual](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Tráfego avançado portuário para uma porta específica em VMs específicos](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configure regras de equilíbrio de carga e saída](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Gateway de Aplicação](#applicationgateway)|O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web.|<p>[Direcionar o tráfego Web com o Gateway de Aplicação do Azure](../application-gateway/quick-create-portal.md)</p><p>[Configurar um gateway de aplicação com terminação SSL](../application-gateway/create-ssl-portal.md)</p><p>[Criar um gateway de aplicação com o redirecionamento com base no caminho do URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Rede de Entrega de Conteúdos
A Rede de Entrega de Conteúdos (CDN) oferece aos programadores uma solução global para o fornecimento rápido de conteúdo de largura de banda alta aos utilizadores, ao colocar em cache o respetivo conteúdo em nós físicos estrategicamente colocados em todo o mundo. Para mais informações sobre o Azure CDN, consulte a Rede de Entrega de [Conteúdos Azure](../cdn/cdn-overview.md)

![CDN do Azure](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Serviço de porta da frente azure
O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do tráfego da Web ao otimizar para o melhor desempenho e ativação pós-falha instantânea para uma elevada disponibilidade. Com o Front Door, pode transformar as suas aplicações empresariais e para consumidores globais (múltiplas regiões) em aplicações, APIs e conteúdos robustos, modernos e personalizados, com um elevado desempenho e que alcancem uma audiência global com o Azure. Para mais informações, consulte [a Porta da Frente Azure.](../frontdoor/front-door-overview.md)


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gestor de Tráfego

O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta. O Traffic Manager fornece uma gama de métodos de encaminhamento de tráfego para distribuir tráfego como prioridade, ponderado, desempenho, geográfico, multi-valor ou sub-rede. Para obter mais informações sobre os métodos de encaminhamento de tráfego, consulte os métodos de [encaminhamento do Gestor de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

O diagrama seguinte mostra o encaminhamento baseado em prioridade com o Gestor de Tráfego:

![Método de encaminhamento de tráfego "prioritário" do Gestor de Tráfego do Azure](./media/networking-overview/priority.png)

Para mais informações sobre o Gestor de Tráfego, consulte o que é o [Gestor de Tráfego azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Balanceador de Carga
O Equilíbrio de Carga Azure fornece um equilíbrio de carga de alta performance e baixa latência camada 4 para todos os protocolos UDP e TCP. Gere as ligações de entrada e saída. Pode configurar pontos finais equilibrados em carga pública e interna. Pode definir regras para mapear ligações de entrada para destinos de piscina seleção de back-end, utilizando opções de sondagem de saúde TCP e HTTP para gerir a disponibilidade do serviço. Para saber mais sobre o Balancer load, leia o artigo de visão geral do Balancer de [Carga.](../load-balancer/load-balancer-overview.md)

A imagem seguinte mostra uma aplicação multi-nível virada para a Internet que utiliza equilibradores de carga externos e internos:

![Exemplo de Balancer de Carga Azure](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway de Aplicação
O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web. É um Controlador de Entrega de Aplicações (ADC) como um serviço, oferecendo várias capacidades de equilíbrio de carga de camada 7 para as suas aplicações. Para mais informações, consulte o que é o Portal de [Aplicação Azure?](../application-gateway/overview.md)

O diagrama seguinte mostra o encaminhamento baseado em url com o Gateway da Aplicação.

![Exemplo de Gateway de aplicação](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Serviços de monitorização da rede
Esta secção descreve serviços de networking no Azure que ajudam a monitorizar os seus recursos de rede - Network Watcher, ExpressRoute Monitor, Azure Monitor e Virtual Network TAP.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Observador de Rede](#networkwatcher)|Ajuda a monitorizar e resolver problemas de conectividade, ajuda a diagnosticar problemas de VPN, NSG e encaminhamento, capturar pacotes no seu VM, automatiza desencadeando ferramentas de diagnóstico usando funções azure e aplicações lógicas|<p>[Diagnosticar problema de filtragem de tráfego de VM](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnosticar problema de encaminhamento de VM](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorizar as comunicações entre VMs](../network-watcher/connection-monitor.md)</p><p>[Diagnosticar problemas de comunicação entre redes](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Registar tráfego de rede de e para uma VM](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute Monitor](#expressroutemonitor)|Fornece monitorização em tempo real do desempenho da rede, disponibilidade e utilização, ajuda na auto-descoberta da topologia da rede, fornece isolamento de falhas mais rápido, deteta problemas transitórios de rede, ajuda a analisar o desempenho histórico da rede características, suporta multi-subscrição|<p>[Configurar o Monitor de Desempenho de Rede para o ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitorização, métricas e alertas do ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Ajuda-o a compreender como as suas aplicações estão a funcionar e identifica proativamente as questões que as afetam e os recursos de que dependem.|<p>[Métricas e alertas do Gestor de Tráfego](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure monitoriza diagnósticos para O Equilíbrio de Carga Padrão](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorar registos e métricas do Azure Firewall](../firewall/tutorial-diagnostics.md)</p><p>[Monitorização e registos da firewall de aplicações Web do Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[Rede Virtual TAP](#vnettap)|Fornece fluxo contínuo de tráfego de rede de máquinas virtuais para colecionador de pacotes, permite soluções de gestão de desempenho de rede e aplicação e ferramentas de análise de segurança|[Criar um recurso VNet TAP](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Observador de Rede
O Observador de Rede do Azure oferece ferramentas para monitorizar, diagnosticar, ver métricas e ativar ou desativar registos de recursos numa rede virtual do Azure. Para mais informações, consulte o que é o [Observador da Rede?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
Para saber como ver as métricas do circuito ExpressRoute, registos de diagnóstico e alertas, consulte [a monitorização, métricas e alertas do ExpressRoute.](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
O Azure Monitor maximiza a disponibilidade e o desempenho das aplicações ao disponibilizar uma solução abrangente para recolher, analisar e agir na telemetria dos ambientes na cloud e no local. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem. Para mais informações, consulte a [visão geral do Monitor Azure](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Rede Virtual TAP
A rede virtual Azure TAP (Terminal Access Point) permite-lhe transmitir continuamente o tráfego da sua rede virtual de máquinas para uma ferramenta de coleção de pacotes de rede ou de análise. A ferramenta de coleção ou análise é fornecida por um parceiro de aparelho virtual de [rede.](https://azure.microsoft.com/solutions/network-appliances/) 

A imagem que se segue mostra como funciona a rede virtual TAP. 

![Como funciona a rede virtual TAP](./media/networking-overview/virtual-network-tap-architecture.png)

Para mais informações, consulte o que é a [Rede Virtual TAP.](../virtual-network/virtual-network-tap-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Crie o seu primeiro VNet e conecte alguns VMs ao mesmo, completando os passos no primeiro artigo de [rede virtual Create.](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
- Ligue o seu computador a um VNet completando os passos no artigo de [ligação ponto-a-local](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Carregar o tráfego de Internet para servidores públicos, completando os passos no artigo [Criar um balancer de carga virado para](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) a Internet.
 
 
   
