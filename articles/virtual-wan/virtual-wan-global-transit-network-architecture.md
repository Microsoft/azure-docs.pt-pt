---
title: 'Arquitetura: Arquitetura de rede de trânsito global'
titleSuffix: Azure Virtual WAN
description: Saiba como o Azure Virtual WAN permite uma arquitetura global de rede de trânsito, permitindo uma conectividade ubíqua e qualquer entre cargas de trabalho em nuvem.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: e3a0eaeebbc0659b217051c6e98d67803896f2e1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102328"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Arquitetura global de rede de trânsito e WAN Virtual

As empresas modernas requerem conectividade ubíqua entre aplicações hiper-distribuídas, dados e utilizadores em toda a nuvem e no local. A arquitetura global da rede de trânsito está a ser adotada pelas empresas para consolidar, conectar e controlar a pegada de TI moderna e global centrada na nuvem.

A arquitetura global da rede de trânsito baseia-se num modelo clássico de conectividade hub-and-spoke onde o 'hub' da rede de cloud alojado permite a conectividade transitiva entre pontos finais que podem ser distribuídos por diferentes tipos de 'porta-vozes'.

Neste modelo, um porta-voz pode ser:
* Rede virtual (VNets)
* Local de ramo físico
* Utilizador remoto
* Internet

![hub e falou](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figura 1: Rede global de hub-and-spoke de trânsito**

A figura 1 mostra a visão lógica da rede global de trânsito onde utilizadores geograficamente distribuídos, sites físicos e VNets estão interligados através de um hub de rede hospedado na nuvem. Esta arquitetura permite a conectividade lógica de trânsito de um lúpulo entre os pontos finais de rede.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Rede global de trânsito com VIRTUAL WAN

Azure Virtual WAN é um serviço de rede em nuvem gerido pela Microsoft. Todos os componentes de rede de que este serviço é composto são hospedados e geridos pela Microsoft. Para obter mais informações sobre o WAN Virtual, consulte o artigo [Geral da WAN Virtual.](virtual-wan-about.md)

O Azure Virtual WAN permite uma arquitetura global de rede de trânsito, permitindo uma conectividade ubíqua, qualquer para qualquer, entre conjuntos de cargas de trabalho em nuvem distribuídas globalmente em VNets, sites de filiais, aplicações SaaS e PaaS, e utilizadores.

![WAN Virtual do Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figura 2: Rede global de trânsito e WAN Virtual**

Na arquitetura Azure Virtual WAN, os hubs WAN virtuais são a provisionados nas regiões de Azure, às quais pode optar por ligar os seus ramos, VNets e utilizadores remotos. Os sites de ramificações físicas estão ligados ao hub por Premium ou Standard ExpressRoute ou site-to site-VPNs, os VNets estão ligados ao hub por ligações VNet, e os utilizadores remotos podem ligar-se diretamente ao hub usando VPN do utilizador (VPNs ponto a local). O WAN virtual também suporta a ligação VNet entre regiões, onde um VNet numa região pode ser conectado a um hub WAN virtual numa região diferente.

Você pode estabelecer um WAN virtual criando um único hub WAN virtual na região que tem o maior número de porta-vozes (ramos, VNets, utilizadores), e, em seguida, conectando os raios que estão em outras regiões ao centro. Esta é uma boa opção quando uma pegada empresarial está principalmente numa região com alguns raios remotos.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Conectividade hub-to-hub

Uma pegada de nuvem empresarial pode abranger várias regiões em nuvem e é ideal (em termos de latência) aceder à nuvem a partir de uma região mais próxima do seu site físico e dos seus utilizadores. Um dos princípios fundamentais da arquitetura global da rede de trânsito é permitir a conectividade entre todos os pontos finais da rede de nuvem e no local. Isto significa que o tráfego de um ramo que está ligado à nuvem numa região pode chegar a outro ramo ou um VNet numa região diferente usando conectividade hub-to-hub ativada pela [Azure Global Network](https://azure.microsoft.com/global-infrastructure/global-network/).

![região transversal](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Figura 3: Conectividade transversal virtual WAN**

Quando vários hubs são ativados num único WAN virtual, os hubs são automaticamente interligados através de ligações hub-to-hub, permitindo assim a conectividade global entre ramos e Vnets que são distribuídos por várias regiões. 

Além disso, os centros que fazem parte do mesmo WAN virtual, podem ser associados a diferentes políticas regionais de acesso e segurança. Para mais informações, consulte [Segurança e controlo de políticas](#security) mais tarde neste artigo.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Qualquer conectividade

A arquitetura global da rede de trânsito permite qualquer conectividade através de centros WAN virtuais. Esta arquitetura elimina ou reduz a necessidade de malha completa ou conectividade parcial de malha entre raios, que são mais complexos para construir e manter. Além disso, o controlo de encaminhamento nas redes hub-e-spoke vs. malha é mais fácil de configurar e manter.

Qualquer conectividade (no contexto de uma arquitetura global) permite que uma empresa com utilizadores, agências, centros de dados, VNets e aplicações distribuídas globalmente se conecte através do hub(s) de "trânsito"). AZure Virtual WAN funciona como o sistema de trânsito global.

![qualquer a qualquer](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Figura 4: Vias de tráfego virtual WAN**

AZure Virtual WAN suporta os seguintes caminhos de conectividade de trânsito global. As letras em parênteses mapeam para a Figura 4.

* Ramo-a-VNet (a)
* Ramo-a-ramo (b)
  * ExpressRoute Global Reach e Virtual WAN
* Utilizador remoto-para-VNet (c)
* Utilizador-a-ramo remoto (d)
* VNet-to-VNet (e)
* Ramo-a-hub-hub-to-Branch (f)
* Ramo-a-hub-hub-to-VNet (g)
* VNet-to-hub-hub-to-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Sucursal-para-VNet (a) e região transversal branch-to-VNet (g)

Branch-to-VNet é o caminho principal suportado por Azure Virtual WAN. Este caminho permite-lhe ligar filiais às cargas de trabalho da empresa Azure IAAS que são implantadas em VNets Azure. Os ramos podem ser ligados ao WAN virtual via ExpressRoute ou VPN site-to-site. O trânsito passa para VNets que estão ligados aos centros WAN virtuais através de Conexões VNet. O trânsito de [gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) explícito não é necessário para o WAN Virtual porque o VIRTUAL WAN automaticamente permite o trânsito de gateway para o local da filial. Consulte o artigo [da Virtual WAN Partners](virtual-wan-configure-automation-providers.md) sobre como ligar um CPE SD-WAN ao WAN Virtual.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach e Virtual WAN

O ExpressRoute é uma forma privada e resiliente de ligar as suas redes no local à Microsoft Cloud. O WAN virtual suporta as ligações do circuito De Rota Expresso. Ligar um site de filial a VIRTUAL WAN com a Rota Expressa requer 1) Circuito Premium ou Standard 2) para estar numa localização ativada ao Alcance Global.

ExpressRoute Global Reach é uma funcionalidade de complemento para o ExpressRoute. Com o Global Reach, pode ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes no local. Os ramos que estão ligados ao Azure Virtual WAN usando o ExpressRoute exigem que o ExpressRoute Global Reach comunique entre si.

Neste modelo, cada ramo que está ligado ao hub WAN virtual utilizando o ExpressRoute pode ligar-se aos VNets utilizando o caminho branch-to-VNet. O tráfego de ramo-a-ramo não vai transitar o hub porque o ExpressRoute Global Reach permite um caminho mais ideal sobre Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Ramo-a-ramo b e região transversal de ramo-a-ramo f

Os ramos podem ser ligados a um hub WAN virtual Azure utilizando circuitos ExpressRoute e/ou ligações VPN site-to-site. Pode ligar os ramos ao centro wan virtual que está na região mais próxima do ramo.

Esta opção permite que as empresas aproveitem a espinha dorsal do Azure para ligar filiais. No entanto, mesmo esta capacidade disponível, você deve pesar os benefícios de ligar ramos sobre Azure Virtual WAN vs. usando um WAN privado.  

> [!NOTE]
> Desativando a conectividade branch-to-branch em VIRTUAL WAN - WAN virtual pode ser configurado para desativar a conectividade Branch-to-Branch. Esta configuação bloqueará a propagação de rotas entre os sites ligados VPN (S2S e P2S) e Express Route. Esta configuração não afetará a propogação e conectividade da rota branch-to-Vnet e Vnet-to-Vnet. Para configurar esta definição utilizando o Portal Azure: No menu de configuração VIRTUAL WAN, escolha definição: Branch-to-Branch - Desativado. 

### <a name="remote-user-to-vnet-c"></a>Utilizador remoto-para-VNet (c)

Pode ativar o acesso remoto direto e seguro ao Azure utilizando a ligação ponto-a-local de um cliente utilizador remoto para um WAN virtual. Os utilizadores remotos da empresa já não têm de se pentear na nuvem usando uma VPN corporativa.

### <a name="remote-user-to-branch-d"></a>Utilizador-a-ramo remoto (d)

O caminho remote user-to-branch permite aos utilizadores remotos que estão a utilizar uma ligação ponto-a-local ao Azure aceder a cargas de trabalho e aplicações no local, atravessando a nuvem. Este caminho confere aos utilizadores remotos a flexibilidade para acederem a cargas de trabalho que são implementadas tanto no Azure como no local. As empresas podem permitir o serviço de acesso remoto seguro baseado na nuvem central em Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Trânsito VNet-to-VNet (e) e região transversal VNet-to-VNet (h)

O trânsito VNet-to-VNet permite que os VNets se conectem entre si de modo a interligar aplicações multi-camadas que são implementadas através de vários VNets. Opcionalmente, pode ligar VNets entre si através do VNet Peering e isso pode ser adequado para alguns cenários em que o trânsito através do hub VWAN não é necessário.


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Forçar túnel e rota padrão em Azure Virtual WAN

O túnel de força pode ser ativado configurando a rota por defeito de ativação numa ligação VPN, ExpressRoute ou Virtual Network em VIRTUAL WAN.

Um hub virtual propaga uma rota padrão aprendida para uma ligação VPN/ExpressRoute de rede virtual/local se ativar a bandeira padrão é 'Activada' na ligação. 

Esta bandeira é visível quando o utilizador edita uma ligação de rede virtual, uma ligação VPN ou uma ligação ExpressRoute. Por predefinição, esta bandeira é desativada quando um site ou um circuito ExpressRoute estão ligados a um hub. É ativado por padrão quando uma ligação de rede virtual é adicionada para ligar um VNet a um hub virtual. A rota predefinida não tem origem no hub Virtual WAN; a rota padrão é propagada se já for aprendida pelo hub Virtual WAN como resultado da implantação de uma firewall no centro, ou se outro site conectado tiver um túnel forçado habilitado.

## <a name="security-and-policy-control"></a><a name="security"></a>Segurança e controlo de políticas

Os hubs Azure Virtual WAN interligam todos os pontos finais de rede em toda a rede híbrida e potencialmente vêem todo o tráfego da rede de trânsito. Os hubs virtuais wan podem ser convertidos para Secured Virtual Hubs implantando o Azure Firewall dentro dos hubs VWAN para permitir a segurança, acesso e controlo de políticas baseados na nuvem. A orquestração de Firewalls Azure em centros WAN virtuais pode ser executada por Azure Firewall Manager.

[O Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) fornece as capacidades para gerir e escalar a segurança das redes globais de trânsito. O Azure Firewall Manager fornece a capacidade de gerir centralmente o encaminhamento, a gestão global de políticas, serviços avançados de segurança da Internet através de terceiros, juntamente com o Azure Firewall.

![hub virtual seguro com Azure Firewall](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Figura 5: Centro virtual seguro com firewall Azure**

> [!NOTE]
> O inter-hub com firewall não é suportado atualmente. O tráfego entre os hubs passará diretamente pela Firewall Azure em cada hub.

O Azure Firewall para o WAN virtual suporta os seguintes caminhos globais de conectividade de trânsito garantidos. As letras em parênteses mapeam para a Figura 5.

* VNet-para-VNet trânsito seguro (e)
* VNet-to-Internet ou serviço de segurança de terceiros (i)
* Serviço de Segurança sucursal ou de terceiros (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet-para-VNet trânsito seguro (e)

O trânsito seguro VNet-to-VNet permite que os VNets se conectem entre si através do Azure Firewall no hub WAN virtual.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-to-Internet ou serviço de segurança de terceiros (i)

O VNet-to-Internet permite que os VNets se conectem à internet através do Azure Firewall no hub WAN virtual. O tráfego para a internet através de serviços de segurança de terceiros suportados não flui através do Azure Firewall. Pode configurar o caminho Vnet-to-Internet através de um serviço de segurança de terceiros suportado utilizando o Azure Firewall Manager.  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Serviço de Segurança sucursal ou de terceiros (j)
O Branch-to-Internet permite que os balcões se conectem à internet através do Azure Firewall no centro WAN virtual. O tráfego para a internet através de serviços de segurança de terceiros suportados não flui através do Azure Firewall. Pode configurar o caminho Branch-to-Internet através de um serviço de segurança de terceiros suportado utilizando o Azure Firewall Manager. 

### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>Como posso ativar a rota predefinido (0.0.0.0/0) num Hub Virtual Seguro

O Azure Firewall implantado num hub VIRTUAL WAN (Secure Virtual Hub) pode ser configurado como router padrão para a Internet ou Fornecedor de Segurança Fidedigna para todos os ramos (ligado por VPN ou Rota Expressa), Vnets e Utilizadores falados (ligados via P2S VPN). Esta configuração deve ser feita utilizando o Azure Firewall Manager.  Consulte o Tráfego de Rotas até ao seu hub para configurar todo o tráfego de agências (incluindo utilizadores) bem como Vnets para a Internet através do Azure Firewall. 

Esta é uma configuração de dois passos:

1. Configure o encaminhamento de tráfego de Internet utilizando o menu Secure Virtual Hub Route Setting. Configure Vnets e Ramos que podem enviar tráfego para a internet através da Firewall.

2. Configure quais as Ligações (Vnet e Branch) que podem encaminhar o tráfego para a internet (0.0.0.0/0) através do Azure FW no centro ou Fornecedor de Segurança Fidedigna. Este passo garante que a rota predefinida é propagada a ramos selecionados e Vnets que são ligados ao hub WAN virtual através das Conexões. 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>Forçar o tráfego de túneis para firewall nas instalações em um centro virtual seguro

Se já existir uma rota predefinida aprendida (via BGP) pelo Centro Virtual a partir de um dos sites VPN ou ER, esta rota padrão é ultrapassada pela rota padrão aprendida com a definição do Azure Firewall Manager. Neste caso, todo o tráfego que estiver a entrar no centro a partir de Vnets e ramos destinados à internet, será encaminhado para o Azure Firewall ou O Fornecedor de Segurança Fidedigna.

> [!NOTE]
> Atualmente não existe opção de selecionar firewall ou Azure Firewall (e Fornecedor de Segurança Fidedigna) para tráfego ligado à Internet originário de Vnets, Branches ou Utilizadores. A rota padrão aprendida com a definição do Azure Firewall Manager é sempre preferida sobre a rota padrão aprendida a partir de um dos ramos.


## <a name="next-steps"></a>Passos seguintes

Crie uma ligação utilizando o Virtual WAN e implementar a firewall Azure no(s) hub(s) VWAN.

* [Ligações site-to-site usando WAN Virtual](virtual-wan-site-to-site-portal.md)
* [Conexões ExpressRoute usando WAN Virtual](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager para implementar Azure FW em VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
