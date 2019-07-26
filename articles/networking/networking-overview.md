---
title: Rede do Azure | Microsoft Docs
description: Saiba mais sobre os serviços de rede no Azure e seus recursos.
services: networking
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2019
ms.author: kumud
ms.openlocfilehash: 9fb7fc9b4f0e5af0847876ff41b6a307f8a09749
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348031"
---
# <a name="azure-networking"></a>Rede do Azure

Os serviços de rede no Azure fornecem uma variedade de recursos de rede que podem ser usados juntos ou separados. Clique em qualquer um dos seguintes recursos principais para saber mais sobre eles:
- [**Serviços de conectividade**](#connect): Conecte recursos do Azure e recursos locais usando qualquer uma delas ou uma combinação desses serviços de rede no Azure – rede virtual (VNet), WAN virtual, ExpressRoute, gateway de VPN, DNS do Azure ou bastiões do Azure.
- [**Serviços de proteção de aplicativo**](#protect) Proteja seus aplicativos usando qualquer um ou uma combinação desses serviços de rede no Azure – proteção contra DDoS, firewall, grupos de segurança de rede, firewall de aplicativo Web ou pontos de extremidade de rede virtual.
- [**Serviços de entrega de aplicativos**](#deliver) Entregue aplicativos na rede do Azure usando qualquer um ou uma combinação desses serviços de rede no Azure – CDN (rede de distribuição de conteúdo), serviço de porta frontal do Azure, Gerenciador de tráfego, gateway de aplicativo ou Load Balancer.
- [**Monitoramento de rede**](#monitor) – monitore seus recursos de rede usando qualquer um ou uma combinação desses serviços de rede no Azure-observador de rede, monitor de ExpressRoute, Azure monitor ou ponto de acesso de terminal de VNET (toque).

## <a name="connect"></a>Serviços de conectividade
 
Esta seção descreve os serviços que fornecem conectividade entre os recursos do Azure, a conectividade de uma rede local para recursos do Azure e a conectividade de Branch para Branch no Azure-rede virtual, ExpressRoute, gateway de VPN, WAN virtual, DNS e Azure Bastiões.

|Serviço|Por que usar?|Cenários|
|---|---|---|
|[Rede virtual](#vnet)|Permite que os recursos do Azure se comuniquem com segurança entre si, Internet e redes locais.| <p>[Filtrar tráfego de rede](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Encaminhar tráfego de rede](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Restringir acesso da rede aos recursos](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Ligar redes virtuais](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Estende suas redes locais para a nuvem da Microsoft por uma conexão privada, facilitada por um provedor de conectividade.|<p>[Criar e modificar um circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Criar e modificar o peering de um circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Ligar uma VNet a um circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configurar e gerenciar filtros de rota para circuitos do ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[Gateway de VPN](#vpngateway)|Envia o tráfego criptografado entre uma rede virtual do Azure e uma localização local pela Internet pública.|<p>[Conexões site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Conexões VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Conexões ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[WAN virtual](#virtualwan)|Otimiza e automatiza a conectividade de ramificação para, e por meio do Azure. As regiões do Azure servem como hubs para os quais você pode escolher conectar suas ramificações.|<p>[Conexões site a site](../virtual-wan/virtual-wan-site-to-site-portal.md), conexões do [ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[DNS do Azure](#dns)|Hospeda domínios DNS que fornecem resolução de nomes usando Microsoft Azure infraestrutura.|<p>[Alojar o seu domínio no DNS do Azure](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Criar registros DNS para um aplicativo Web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Criar um registro de alias para o Gerenciador de tráfego](../dns/tutorial-alias-tm.md)</p> <p>[Criar um registro de alias para o endereço IP público](../dns/tutorial-alias-pip.md)</p> <p>[Criar um registro de alias para o registro de recurso de zona](../dns/tutorial-alias-rr.md)</p>|
|[Bastiões do Azure (versão prévia)](#bastion)|Configure a conectividade RDP/SSH segura e totalmente integrada às suas máquinas virtuais diretamente no portal do Azure por SSL. Quando você se conecta por meio de bastiões do Azure, suas máquinas virtuais não precisam de um endereço IP público|<p>[Criar um host de bastiões do Azure](../bastion/bastion-create-host-portal.md)</p><p>[Conectar-se usando SSH em uma VM Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Conectar-se usando o RDP em uma VM do Windows](/bastion/bastion-connect-vm-rdp.md)</p>|
||||


### <a name="vnet"></a>Rede virtual

A VNet (rede virtual) do Azure é o bloco de construção fundamental para sua rede privada no Azure. Você pode usar um VNets para:
- **Comunicar-se entre os recursos do Azure**: Você pode implantar VMs e vários outros tipos de recursos do Azure em uma rede virtual, como ambientes de serviço Azure App, o AKS (serviço kubernetes do Azure) e conjuntos de dimensionamento de máquinas virtuais do Azure. Para ver uma lista completa dos recursos do Azure que pode implementar numa rede virtual, veja [Integração de serviço da rede virtual](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicar entre**si: Pode ligar redes virtuais entre si, ativando recursos em qualquer rede virtual para comunicarem entre si, com o peering de rede virtual. As redes virtuais a que liga podem estar nas mesmas regiões ou em regiões diferentes do Azure. Para obter mais informações, consulte [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md).
- **Comunicar-se com a Internet**: Por padrão, todos os recursos em uma VNet podem comunicar a saída para a Internet. Pode comunicar com um recurso à entrada, ao atribuir-lhe um endereço IP público ou um Balanceador de Carga público. Você também pode usar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ou [Load Balancer](../load-balancer/load-balancer-overview.md) públicos para gerenciar suas conexões de saída.
- **Comunicar-se com redes locais**: Você pode conectar seus computadores e redes locais a uma rede virtual usando o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o [ExpressRoute](../expressroute/expressroute-introduction.md).

Para obter mais informações, consulte [o que é a rede virtual do Azure?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a>ExpressRoute
O ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft por uma conexão privada, facilitada por um provedor de conectividade. Esta ligação é privada. O tráfego não é transmitido pela Internet. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365.  Para obter mais informações, consulte [o que é o ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpngateway"></a>Gateway de VPN
O gateway de VPN ajuda a criar conexões criptografadas entre locais com sua rede virtual a partir de locais ou criar conexões criptografadas entre VNets. Há diferentes configurações disponíveis para conexões de gateway de VPN, como site a site, ponto a site ou VNet para VNet.
O diagrama a seguir ilustra várias conexões VPN site a site para a mesma rede virtual.

![Conexões de gateway de VPN do Azure site a site](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Para obter mais informações sobre diferentes tipos de conexões VPN, consulte [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtualwan"></a>WAN virtual
A WAN virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para o, e por meio do Azure. As regiões do Azure servem como hubs para os quais você pode escolher conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar ramificações e aproveitar a conectividade de ramificação para VNet. A WAN virtual do Azure reúne muitos serviços de conectividade de nuvem do Azure, como VPN site a site, ExpressRoute, VPN de usuário ponto a site em uma única interface operacional. A conectividade com o Azure VNets é estabelecida usando conexões de rede virtual. Para obter mais informações, consulte [o que é a WAN virtual do Azure?](../virtual-wan/virtual-wan-about.md).

![Diagrama da WAN Virtual](./media/networking-overview/virtualwan1.png)

### <a name="dns"></a>DNS do Azure
O DNS do Azure é um serviço de alojamento dos domínios DNS que oferece resolução de nomes através da infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. Para obter mais informações, consulte [o que é o DNS do Azure?](../dns/dns-overview.md).

### <a name="bastion"></a>Bastiões do Azure (versão prévia)
O serviço de bastiões do Azure é um novo serviço de PaaS totalmente gerenciado por plataforma que você provisiona dentro de sua rede virtual. Ele fornece conectividade RDP/SSH segura e direta para suas máquinas virtuais diretamente no portal do Azure sobre SSL. Ao ligar-se através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, consulte [o que é a bastiões do Azure?](/bastion/bastion-overview.md).

![Arquitetura de bastiões do Azure](./media/networking-overview/architecture.png)


## <a name="protect"></a>Serviços de proteção de aplicativo

Esta seção descreve os serviços de rede no Azure que ajudam a proteger os recursos de rede – proteção contra DDoS, firewall do aplicativo Web, firewall do Azure, grupos de segurança de rede e pontos de extremidade de serviço.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Proteção contra DDoS](#ddosprotection) |Alta disponibilidade para seus aplicativos com proteção contra encargos de tráfego IP em excesso|[Gerenciar a proteção contra DDoS do Azure](../virtual-network/manage-ddos-protection.md)|
|[Firewall do aplicativo Web](#waf)|<p>O [Azure WAF com o gateway de aplicativo](../application-gateway/waf-overview.md) fornece proteção regional para entidades no espaço de endereço público e privado</p><p>O [Azure WAF com a porta frontal](../frontdoor/waf-overview.md) fornece proteção na borda da rede para pontos de extremidade públicos.</p>|<p>[Configurar regras de proteção de bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurar código de resposta personalizado](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configurar regras de restrição de IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Configurar regra de limite de taxa](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Firewall do Azure](#firewall)|O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. Trata-se de um firewall totalmente com estado como um serviço com alta disponibilidade interna e escalabilidade de nuvem irrestrita.|<p>[Implantar um firewall do Azure em uma vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-Implantar um firewall do Azure em uma rede híbrida](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrar o tráfego de entrada com o Firewall do Azure DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Grupos de segurança de rede](#nsg)|Controle completo de nó final distribuído granular na VM/sub-rede para todos os fluxos de tráfego de rede|[Filtrar o tráfego de rede usando grupos de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)|
|[Pontos finais de serviço de rede virtual](#serviceendpoints)|Permite limitar o acesso à rede para alguns recursos de serviço do Azure a uma sub-rede de rede virtual|[Restrict network access to PaaS resources](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md) (Restringir o acesso de rede a recursos de PaaS)|
|||
### <a name="ddosprotection"></a>Proteção contra DDoS 
A [proteção contra DDoS do Azure](../virtual-network/manage-ddos-protection.md) fornece medidas defensivas contra as ameaças de DDoS mais sofisticadas. O serviço fornece recursos aprimorados de mitigação de DDoS para seu aplicativo e recursos implantados em suas redes virtuais. Além disso, os clientes que usam a proteção contra DDoS do Azure têm acesso ao suporte de resposta rápida a DDoS para envolver especialistas em DDoS durante um ataque ativo.

![Proteção contra DDOS](./media/networking-overview/ddos-protection.png)

### <a name="waf"></a>Firewall do aplicativo Web

O WAF (firewall do aplicativo Web) do Azure fornece proteção aos seus aplicativos Web contra explorações e vulnerabilidades comuns da Web, como injeção de SQL e script entre sites. O Azure WAF fornece proteção pronta para uso das 10 principais vulnerabilidades do OWASP por meio de regras gerenciadas. Além disso, os clientes também podem configurar regras personalizadas, que são regras gerenciadas pelo cliente para fornecer proteção adicional com base no intervalo de IP de origem e atributos de solicitação, como cabeçalhos, cookies, campos de dados de formulário ou parâmetros de cadeia de caracteres de consulta.

Os clientes podem optar por implantar o [Azure WAF com o gateway de aplicativo](../application-gateway/waf-overview.md) , que fornece proteção regional para entidades no espaço de endereço público e privado. Os clientes também podem optar por implantar o [Azure WAF com a porta frontal](../frontdoor/waf-overview.md) , que fornece proteção na borda da rede para pontos de extremidade públicos.

![Firewall de Aplicações Web](./media/networking-overview/waf-overview.png)


### <a name="firewall"></a>Firewall do Azure
O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. Usando o Firewall do Azure, você pode criar, impor e registrar políticas de conectividade de aplicativo e de rede centralmente em assinaturas e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual. 

Para obter mais informações sobre o Firewall do Azure, consulte a [documentação do firewall do Azure](../firewall/overview.md).

![Descrição geral das firewalls](./media/networking-overview/firewall-threat.png)

### <a name="nsg"></a>Grupos de segurança de rede
Você pode filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure com um grupo de segurança de rede. Para obter mais informações, consulte [visão geral de segurança](../virtual-network/security-overview.md).

### <a name="serviceendpoints"></a>Pontos de extremidade de serviço
Os pontos finais de serviço da Rede Virtual (VNet) expandem o seu espaço de endereços privados de rede virtual e da identidade da sua VNet para os serviços do Azure através de uma ligação direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O tráfego da sua VNet para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure. Para obter mais informações, consulte [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

![Pontos finais de serviço de rede virtual](./media/networking-overview/vnet-service-endpoints-overview.png)

## <a name="deliver"></a>Serviços de entrega de aplicativos

Esta seção descreve os serviços de rede no Azure que ajudam a fornecer aplicativos – CDN (rede de distribuição de conteúdo), serviço de porta frontal do Azure, Gerenciador de tráfego, gateway de aplicativo e Load Balancer.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Rede de distribuição de conteúdo](#cdn)|Fornece conteúdo de alta largura de banda aos usuários. O CDNs armazena o conteúdo em cache em servidores de borda em locais POP (ponto de presença) que estão próximos aos usuários finais, para minimizar a latência|<p>[Adicionar CDN a um aplicativo Web](../cdn/cdn-add-to-web-app.md)</p> <p>[-Acessar blobs de armazenamento usando um domínio personalizado da CDN do Azure por HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Adicionar um domínio personalizado ao ponto de extremidade da CDN do Azure](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configure HTTPS on an Azure CDN custom domain](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate) (Configurar HTTPS num domínio personalizado da CDN do Azure)</p>|
|[Serviço de porta frontal do Azure](#frontdoor)|Permite que você defina, gerencie e monitore o roteamento global para o tráfego da Web otimizando para melhor desempenho e failover global instantâneo para alta disponibilidade.|<p>[Adicionar um domínio personalizado ao serviço de porta frontal do Azure](../frontdoor/front-door-custom-domain.md)</p> <p>[Configurar HTTPS em um domínio personalizado de porta frontal](../frontdoor/front-door-custom-domain-https.md)</p><p>[Configurar a política de firewall do aplicativo Web de filtragem geográfica](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Gestor de Tráfego](#trafficmanager)|Distribui o tráfego com base no DNS para serviços em regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta|<p> [Encaminhar o tráfego de baixa latência](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Encaminhar o tráfego para um ponto final prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Controlar o tráfego com pontos de extremidade ponderados](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Rotear o tráfego com base na localização geográfica do ponto de extremidade](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Encaminhar tráfego com base na sub-rede do utilizador](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Balanceador de Carga](#loadbalancer)|Fornece balanceamento de carga regional roteando o tráfego entre zonas de disponibilidade e em seu VNets. Fornece balanceamento de carga interno roteando o tráfego entre e entre seus recursos para criar seu aplicativo regional.|<p> [Balancear carga de tráfego de internet para VMs](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Balancear a carga de tráfego entre VMs dentro de uma rede virtual](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Tráfego de encaminhamento de porta para uma porta específica em VMs específicas](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configurar o balanceamento de carga e as regras de saída](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Gateway de Aplicação](#applicationgateway)|O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web.|<p>[Tráfego direto da Web com o gateway de Aplicativo Azure](../application-gateway/quick-create-portal.md)</p><p>[Configurar um gateway de aplicação com terminação SSL](../application-gateway/create-ssl-portal.md)</p><p>[Criar um gateway de aplicação com o redirecionamento com base no caminho do URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="cdn"></a>Rede de distribuição de conteúdo
A Rede de Entrega de Conteúdos (CDN) oferece aos programadores uma solução global para o fornecimento rápido de conteúdo de largura de banda alta aos utilizadores, ao colocar em cache o respetivo conteúdo em nós físicos estrategicamente colocados em todo o mundo. Para obter mais informações sobre a CDN do Azure, consulte [rede de distribuição de conteúdo do Azure](../cdn/cdn-overview.md)

![CDN do Azure](./media/networking-overview/cdn-overview.png)

### <a name="frontdoor"></a>Serviço de porta frontal do Azure
O Azure Front Door Service permite-lhe definir, gerir e monitorizar o encaminhamento global do tráfego da Web ao otimizar para o melhor desempenho e ativação pós-falha instantânea para uma elevada disponibilidade. Com o Front Door, pode transformar as suas aplicações empresariais e para consumidores globais (múltiplas regiões) em aplicações, APIs e conteúdos robustos, modernos e personalizados, com um elevado desempenho e que alcancem uma audiência global com o Azure. Para obter mais informações, consulte [Azure front door](../frontdoor/front-door-overview.md).


### <a name="trafficmanager"></a>Gerenciador de tráfego

O Gestor de Tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que lhe permite distribuir o tráfego de forma otimizada para serviços nas regiões globais do Azure, proporcionando, ao mesmo tempo, elevada disponibilidade e capacidade de resposta. O Gerenciador de tráfego fornece uma variedade de métodos de roteamento de tráfego para distribuir tráfego, como prioridade, peso, desempenho, geográfico, vários valores ou sub-rede. Para obter mais informações sobre métodos de roteamento de tráfego, consulte [métodos de roteamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

O diagrama a seguir mostra o roteamento baseado em prioridade de ponto de extremidade com o Gerenciador de tráfego:

![Método de roteamento de tráfego de "prioridade" do Gerenciador de tráfego do Azure](./media/networking-overview/priority.png)

Para obter mais informações sobre o Gerenciador de tráfego, consulte [o que é o Gerenciador de tráfego do Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="loadbalancer"></a>Load Balancer
O Azure Load Balancer fornece balanceamento de carga de camada 4 de alto desempenho e baixa latência para todos os protocolos UDP e TCP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade públicos e internos com balanceamento de carga. Você pode definir regras para mapear conexões de entrada para destinos de pool de back-end usando as opções de investigação de integridade TCP e HTTP para gerenciar a disponibilidade do serviço. Para saber mais sobre Load Balancer, leia o artigo [Load Balancer visão geral](../load-balancer/load-balancer-overview.md) .

A imagem a seguir mostra um aplicativo de várias camadas voltado para a Internet que utiliza balanceadores de carga externos e internos:

![Exemplo de Azure Load Balancer](./media/networking-overview/IC744147.png)


### <a name="applicationgateway"></a>Gateway de aplicativo
O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web. É um ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Para obter mais informações, consulte [o que é aplicativo Azure gateway?](../application-gateway/overview.md).

O diagrama a seguir mostra o roteamento baseado em caminho de URL com o gateway de aplicativo.

![Exemplo de gateway de aplicativo](./media/networking-overview/figure1-720.png)

## <a name="monitor"></a>Serviços de monitoramento de rede
Esta seção descreve os serviços de rede no Azure que ajudam a monitorar seus recursos de rede-observador de rede, monitor de ExpressRoute, Azure Monitor e toque de rede virtual.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Observador de rede](#networkwatcher)|Ajuda a monitorar e solucionar problemas de conectividade, ajuda a diagnosticar problemas de VPN, NSG e roteamento, capturar pacotes em sua VM, automatiza o disparo de ferramentas de diagnóstico usando Azure Functions e aplicativos lógicos|<p>[Diagnosticar problema de filtro de tráfego de VM](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnosticar problema de roteamento de VM](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorar comunicações entre VMs](../network-watcher/connection-monitor.md)</p><p>[Diagnosticar problemas de comunicação entre redes](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Registar tráfego de rede de e para uma VM](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Monitor do ExpressRoute](#expressroutemonitor)|Fornece monitoramento em tempo real de desempenho, disponibilidade e utilização da rede, ajuda com a descoberta automática da topologia de rede, fornece um isolamento de falhas mais rápido, detecta problemas de rede transitórios, ajuda a analisar o desempenho histórico da rede características, suporte a várias assinaturas|<p>[Configurar o Monitor de Desempenho de Rede para o ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitoramento, métricas e alertas do ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Ajuda a entender como os aplicativos estão sendo executados e identifica de forma proativa os problemas que os afetam e os recursos dos quais eles dependem.|<p>[Métricas e alertas do Gerenciador de tráfego](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnóstico do Azure monitor para Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorar os logs e as métricas do firewall do Azure](../firewall/tutorial-diagnostics.md)</p><p>[Monitorização e registos da firewall de aplicações Web do Azure](../frontdoor/waf-front-door-monitor.md)</p>|
|[TOQUE da rede virtual](#vnettap)|Fornece streaming contínuo de tráfego de rede de máquina virtual para coletor de pacotes, permite soluções de gerenciamento de desempenho de aplicativos e de rede e ferramentas de análise de segurança|[Criar um recurso de toque de VNet](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="networkwatcher"></a>Observador de rede
O Observador de Rede do Azure oferece ferramentas para monitorizar, diagnosticar, ver métricas e ativar ou desativar registos de recursos numa rede virtual do Azure. Para obter mais informações, consulte [o que é o observador de rede?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroutemonitor"></a>Monitor do ExpressRoute
Para saber mais sobre como exibir as métricas de circuito do ExpressRoute, logs e alertas de diagnóstico, consulte [monitoramento, métricas e alertas do expressroute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azuremonitor"></a>Azure Monitor
O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos, fornecendo uma solução abrangente para coletar, analisar e agir na telemetria de seus ambientes de nuvem e locais. Ajuda a compreender o desempenho das suas aplicações e identifica proativamente os problemas que as afetam e os recursos de que dependem. Para obter mais informações, consulte [Azure monitor visão geral](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="vnettap"></a>TOQUE da rede virtual
O TAP (ponto de acesso ao terminal) da rede virtual do Azure permite transmitir continuamente o tráfego de rede da máquina virtual para uma ferramenta de análise ou coletor de pacotes de rede. O coletor ou a ferramenta de análise é fornecida por um parceiro de solução de virtualização de [rede](https://azure.microsoft.com/solutions/network-appliances/) . 

A imagem a seguir mostra como o toque da rede virtual funciona. 

![Como o toque da rede virtual funciona](./media/networking-overview/virtual-network-tap-architecture.png)

Para obter mais informações, consulte [o que é toque de rede virtual](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Passos Seguintes

- Crie sua primeira VNet e conecte algumas VMs a ela, concluindo as etapas no artigo [criar sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Conecte seu computador a uma VNet concluindo as etapas no [artigo configurar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Balancear a carga do tráfego de Internet para servidores públicos ao concluir as etapas no artigo [criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
 
 
   
