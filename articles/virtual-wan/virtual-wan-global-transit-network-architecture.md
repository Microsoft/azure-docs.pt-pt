---
title: Arquitetura de rede de trânsito global de WAN virtual do Azure | Microsoft Docs
description: Saiba mais sobre a arquitetura de rede de trânsito global para WAN virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 63ab30c83db692d00e292828b8a8203fa33e7e74
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499734"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Arquitetura de rede de trânsito global e WAN virtual

As empresas modernas exigem conectividade onipresente entre aplicativos, dados e usuários do Hyper-Distributed na nuvem e no local. A arquitetura de rede de trânsito global está sendo adotada pelas empresas para consolidar, conectar e controlar a superfície de ti global moderna, centrada em nuvem.

A arquitetura de rede de trânsito global é baseada em um modelo de conectividade de Hub e spoke clássico em que o ' hub ' de rede hospedada na nuvem permite a conectividade transitiva entre pontos de extremidade que podem ser distribuídos entre diferentes tipos de ' spokes '.

Nesse modelo, um spoke pode ser:
* Rede virtual (VNets)
* Site do Branch físico
* Usuário remoto
* Internet

![Hub e spoke](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figura 1: rede hub e spoke de trânsito global**

A Figura 1 mostra a exibição lógica da rede de trânsito global em que usuários distribuídos geograficamente, sites físicos e VNets são interconectados por meio de um hub de rede hospedado na nuvem. Essa arquitetura permite a conectividade de trânsito de um salto lógico entre os pontos de extremidade de rede.

## <a name="globalnetworktransit"></a>Rede de trânsito global com WAN virtual

A WAN virtual do Azure é um serviço de rede de nuvem gerenciado pela Microsoft. Todos os componentes de rede dos quais esse serviço é composto são hospedados e gerenciados pela Microsoft. Para obter mais informações sobre WAN virtual, consulte o artigo [visão geral da WAN virtual](virtual-wan-about.md) .

A WAN virtual do Azure permite uma arquitetura de rede de trânsito global permitindo uma conectividade onipresente, de qualquer a qualquer entre conjuntos distribuídos globalmente de cargas de trabalho de nuvem em VNets, sites de filiais, aplicativos SaaS e PaaS e usuários.

![WAN Virtual do Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figura 2: rede de trânsito global e WAN virtual**

Na arquitetura WAN virtual do Azure, os hubs de WAN virtuais são provisionados nas regiões do Azure, às quais você pode optar por conectar seus branches, VNets e usuários remotos. Os sites de ramificação física estão conectados ao Hub por VPNs do Premium ExpressRoute ou site a site-para-redes, VNets estão conectados ao Hub por conexões de VNet, e os usuários remotos podem se conectar diretamente ao Hub usando VPN de usuário (VPNs ponto a site). A WAN virtual também dá suporte à conexão VNet entre regiões, em que uma VNet em uma região pode ser conectada a um hub de WAN virtual em uma região diferente.

Você pode estabelecer uma WAN virtual criando um único Hub de WAN virtual na região que tem o maior número de spokes (branches, VNets, Users) e, em seguida, conectando os spokes que estão em outras regiões ao Hub. Essa é uma boa opção quando uma superfície corporativa está em grande parte de uma região com alguns spokes remotos.  
  
## <a name="hubtohub"></a>Conectividade Hub para Hub

Uma superfície de nuvem corporativa pode abranger várias regiões de nuvem e é ideal (de latência) acessar a nuvem de uma região mais próxima de seu site físico e usuários. Um dos principais princípios da arquitetura de rede de trânsito global é habilitar a conectividade entre regiões entre todos os pontos de extremidade de rede local e na nuvem. Isso significa que o tráfego de um Branch que está conectado à nuvem em uma região pode alcançar outro Branch ou uma VNet em uma região diferente usando conectividade de Hub para Hub habilitada pela [rede global do Azure](https://azure.microsoft.com/global-infrastructure/global-network/).

![entre regiões](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Figura 3: conectividade entre regiões virtuais da WAN**

Quando vários hubs estão habilitados em uma única WAN virtual, os hubs são automaticamente interconectados por meio de links de Hub para Hub, permitindo assim a conectividade global entre branches e Vnets que são distribuídos em várias regiões. 

Além disso, os hubs que fazem parte da mesma WAN virtual podem ser associados a diferentes políticas de acesso regional e de segurança. Para obter mais informações, consulte [segurança e controle de diretivas](#security) mais adiante neste artigo.

## <a name="anytoany"></a>Conectividade qualquer-para-qualquer

A arquitetura de rede de trânsito global habilita qualquer conectividade por meio de hubs de WAN virtual. Essa arquitetura elimina ou reduz a necessidade de malha completa ou conectividade de malha parcial entre spokes, que são mais complexas de criar e manter. Além disso, o controle de roteamento em vs. redes de malhas é mais fácil de configurar e manter.

Qualquer conectividade (no contexto de uma arquitetura global) permite que uma empresa com usuários distribuídos globalmente, branches, data centers, VNets e aplicativos se conectem entre si por meio dos hubs de "trânsito". A WAN virtual do Azure atua como o sistema de trânsito global.

![qualquer para qualquer](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Figura 4: caminhos de tráfego de WAN virtual**

A WAN virtual do Azure dá suporte aos seguintes caminhos de conectividade de trânsito globais. As letras entre parênteses são mapeadas para a Figura 4.

* Ramificação para VNet (a)
* Ramificação para ramificação (b)
  * ExpressRoute Alcance Global e WAN virtual
* Usuário remoto para VNet (c)
* Usuário remoto para ramificação (d)
* VNet a VNet (e)
* Branch-to-Hub-Hub para ramificação (f)
* Branch-to-Hub-Hub para VNet (g)
* VNet a Hub-Hub-para-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Entre regiões (a) de ramificação para rede virtual (a) e ramificação para VNet (g)

O Branch-to-VNet é o caminho principal com suporte da WAN virtual do Azure. Esse caminho permite que você conecte branches a cargas de trabalho corporativas do Azure IAAS implantadas no Azure VNets. As ramificações podem ser conectadas à WAN virtual via ExpressRoute ou VPN site a site. O tráfego está em trânsito para VNets que estão conectados aos hubs de WAN virtuais por meio de conexões VNet. O [tráfego de gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) explícito não é necessário para a WAN virtual porque a WAN virtual habilita automaticamente o tráfego de gateway para o site de ramificação. Confira o artigo [parceiros de WAN virtual](virtual-wan-configure-automation-providers.md) sobre como conectar um SD-Wan CPE à WAN virtual.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Alcance Global e WAN virtual

O ExpressRoute é uma maneira privada e resiliente de conectar suas redes locais ao Microsoft Cloud. A WAN virtual dá suporte a conexões de circuito de rota expressa. Conectar um site de ramificação à WAN virtual com rota expressa requer 1) circuito do circuito Premium 2) para estar em um local Alcance Global habilitado.

O ExpressRoute Alcance Global é um recurso complementar para o ExpressRoute. Com o Alcance Global, você pode vincular circuitos do ExpressRoute juntos para fazer uma rede privada entre suas redes locais. As ramificações que estão conectadas à WAN virtual do Azure usando o ExpressRoute exigem que os Alcance Global do ExpressRoute se comuniquem entre si.

Nesse modelo, cada ramificação conectada ao Hub de WAN virtual usando o ExpressRoute pode se conectar ao VNets usando o caminho de ramificação para VNet. O tráfego de ramificação para ramificação não transita o Hub porque o ExpressRoute Alcance Global permite um caminho mais ideal na WAN do Azure.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Ramificação para ramificação (b) e Branch para ramificação entre regiões (f)

As ramificações podem ser conectadas a um hub de WAN virtual do Azure usando circuitos de ExpressRoute e/ou conexões VPN site a site. Você pode conectar as ramificações ao Hub de WAN virtual que está na região mais próxima da ramificação.

Essa opção permite que as empresas aproveitem o backbone do Azure para conectar branches. No entanto, embora esse recurso esteja disponível, você deve avaliar os benefícios de conectar branches pela WAN virtual do Azure versus usar uma WAN privada.  

### <a name="remote-user-to-vnet-c"></a>Usuário remoto para VNet (c)

Você pode habilitar o acesso remoto direto e seguro ao Azure usando a conexão ponto a site de um cliente de usuário remoto para uma WAN virtual. Os usuários remotos da empresa não precisam mais hairpin-los para a nuvem usando uma VPN corporativa.

### <a name="remote-user-to-branch-d"></a>Usuário remoto para ramificação (d)

O caminho do usuário para a ramificação remota permite que usuários remotos que usam uma conexão ponto a site acessem cargas de trabalho e aplicativos locais de acesso do Azure por meio da nuvem. Esse caminho dá aos usuários remotos a flexibilidade para acessar as cargas de trabalho que são implantadas no Azure e no local. As empresas podem habilitar o serviço de acesso remoto seguro baseado em nuvem central na WAN virtual do Azure.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Trânsito de VNet a VNet (e) e VNet para VNet entre regiões (h)

O trânsito de VNet para VNet permite que o VNets se conecte entre si para interconectar aplicativos multicamadas que são implementados em vários VNets. Opcionalmente, você pode conectar o VNets entre si por meio do emparelhamento de VNet e isso pode ser adequado para alguns cenários em que o trânsito por meio do Hub VWAN não é necessário.

## <a name="security"></a>Segurança e controle de política

Os hubs de WAN virtual do Azure interconectam todos os pontos de extremidade de rede na rede híbrida e potencialmente veem todo o tráfego de rede de trânsito. Os hubs de WAN virtuais podem ser convertidos em hubs virtuais protegidos implantando o Firewall do Azure dentro de hubs de VWAN para habilitar a segurança, o acesso e o controle de políticas baseados em nuvem. A orquestração de firewalls do Azure em hubs de WAN virtual pode ser executada pelo Gerenciador de firewall do Azure.

O [Gerenciador de firewall do Azure](https://go.microsoft.com/fwlink/?linkid=2107683) fornece os recursos para gerenciar e dimensionar a segurança para redes de trânsito globais. O Gerenciador de firewall do Azure fornece a capacidade de gerenciar centralmente o roteamento, o gerenciamento de políticas globais e os serviços avançados de segurança da Internet por meio de terceiros, juntamente com o Firewall do Azure.

![Hub virtual protegido com o Firewall do Azure](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Figura 5: Hub virtual protegido com o Firewall do Azure**

O Firewall do Azure para a WAN virtual dá suporte aos seguintes caminhos de conectividade de trânsito seguro global. As letras entre parênteses são mapeadas para a Figura 5.

* Trânsito seguro de VNet a VNet (e)
* VNet a Internet ou serviço de segurança de terceiros (i)
* Branch-to-Internet ou serviço de segurança de terceiros (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Trânsito seguro de VNet a VNet (e)

O trânsito seguro de VNet para VNet permite que o VNets se conecte entre si por meio do firewall do Azure no Hub de WAN virtual.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet a Internet ou serviço de segurança de terceiros (i)

A VNet para a Internet ou o trânsito protegido de terceiros permite que o VNets se conecte à Internet ou a serviços de segurança de terceiros com suporte por meio do firewall do Azure no Hub de WAN virtual.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Branch-to-Internet ou serviço de segurança de terceiros (j)
A filial para a Internet ou o trânsito seguro de terceiros permite que as ramificações se conectem à Internet ou a serviços de segurança de terceiros com suporte por meio do firewall do Azure no Hub de WAN virtual.

## <a name="next-steps"></a>Passos seguintes

Crie uma conexão usando a WAN virtual e implante o Firewall do Azure em VWAN Hub (s).

* [Conexões site a site usando a WAN virtual](virtual-wan-site-to-site-portal.md)
* [Conexões do ExpressRoute usando a WAN virtual](virtual-wan-expressroute-portal.md)
* [Gerenciador de firewall do Azure para implantar o FW do Azure no VWAN] (https://go.microsoft.com/fwlink/?linkid=2107683)
