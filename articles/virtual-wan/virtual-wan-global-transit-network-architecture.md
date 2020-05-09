---
title: 'Arquitetura: Arquitetura global da rede de trânsito'
titleSuffix: Azure Virtual WAN
description: Conheça a arquitetura da rede global de trânsito para o Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: c32d42de5290bff63a897e7b9d5c8a2b1bf04ce4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786976"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Arquitetura global da rede de trânsito e WAN Virtual

As empresas modernas requerem conectividade ubíqua entre aplicações hiperdistribuídas, dados e utilizadores através da nuvem e no local. A arquitetura global da rede de trânsito está a ser adotada pelas empresas para consolidar, conectar e controlar a pegada de TI moderna e global centrada na nuvem.

A arquitetura global da rede de trânsito baseia-se num modelo clássico de conectividade hub-and-spoke onde o 'hub' da rede hospedada em nuvem permite conectividade transitiva entre pontos finais que podem ser distribuídos por diferentes tipos de 'raios'.

Neste modelo, um discurso pode ser:
* Rede virtual (VNets)
* Site de ramo físico
* Utilizador remoto
* Internet

![hub e falou](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figura 1: Rede global de centros de trânsito e porta-voz**

A Figura 1 mostra a visão lógica da rede global de trânsito onde utilizadores geograficamente distribuídos, sites físicos e VNets estão interligados através de um centro de networking alojado na nuvem. Esta arquitetura permite a conectividade lógica de um lúpulo entre os pontos finais de networking.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Rede global de trânsito com o Wan Virtual

Azure Virtual WAN é um serviço de rede de cloud gerido pela Microsoft. Todos os componentes de rede de que este serviço é composto são hospedados e geridos pela Microsoft. Para mais informações sobre o WAN Virtual, consulte o artigo [Virtual WAN Overview.](virtual-wan-about.md)

O Azure Virtual WAN permite uma arquitetura global de rede de trânsito, permitindo uma conectividade ubíqua e qualquer conectividade entre conjuntos distribuídos globalmente de cargas de trabalho em nuvem em VNets, sites de filiais, aplicações SaaS e PaaS, e utilizadores.

![WAN Virtual do Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figura 2: Rede global de trânsito e WAN Virtual**

Na arquitetura Azure Virtual WAN, os centros virtuais WAN são aprovisionados nas regiões de Azure, às quais pode optar por ligar os seus balcões, VNets e utilizadores remotos. Os sites de ramificação física estão ligados ao hub por Premium ExpressRoute ou site-to-VPNs, VNets são ligados ao hub por ligações VNet, e os utilizadores remotos podem ligar-se diretamente ao hub usando VPN do utilizador (VPNs ponto-a-site). O WAN virtual também suporta a ligação VNet transversal onde um VNet numa região pode ser ligado a um centro virtual WAN numa região diferente.

Você pode estabelecer um WAN virtual criando um único centro de WAN virtual na região que tem o maior número de porta-vozes (ramos, VNets, utilizadores), e, em seguida, conectando os raios que estão em outras regiões ao centro. Esta é uma boa opção quando uma pegada empresarial está principalmente numa região com alguns raios remotos.  
  
## <a name="hub-to-hub-connectivity-preview"></a><a name="hubtohub"></a>Conectividade hub-to-hub (Pré-visualização)

Uma pegada de nuvem da Enterprise pode abranger várias regiões de nuvem e é ideal (em termos de latência) para aceder à nuvem de uma região mais próxima do seu site físico e utilizadores. Um dos princípios fundamentais da arquitetura global da rede de trânsito é permitir a conectividade entre todas as nuvens e os pontos finais da rede no local. Isto significa que o tráfego de um ramo ligado à nuvem numa região pode chegar a outro ramo ou um VNet numa região diferente, utilizando a conectividade hub-to-hub ativada pela [Azure Global Network.](https://azure.microsoft.com/global-infrastructure/global-network/)

![região transversal](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Figura 3: Conectividade virtual wan inter-região**

Quando vários centros são ativados num único WAN virtual, os centros são automaticamente interligados através de ligações hub-to-hub, permitindo assim a conectividade global entre ramos e Vnets que são distribuídos por várias regiões. 

Além disso, os centros que fazem parte do mesmo WAN virtual, podem ser associados a diferentes políticas de acesso e segurança regionais. Para mais informações, consulte a [Segurança e o Controlo de Políticas](#security) mais tarde neste artigo.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Qualquer conectividade para qualquer

A arquitetura global da rede de trânsito permite qualquer conectividade através de centros WAN virtuais. Esta arquitetura elimina ou reduz a necessidade de conectividade de malha completa ou de malha parcial entre os raios, que são mais complexos para construir e manter. Além disso, o controlo de encaminhamento em redes de rede hub-e-spoke vs. malha é mais fácil de configurar e manter.

Qualquer conectividade a qualquer (no contexto de uma arquitetura global) permite que uma empresa com utilizadores, sucursais, datacenters, VNets e aplicações se conectem entre si através do hub ou do "trânsito").. O Azure Virtual WAN atua como o sistema de trânsito global.

![qualquer a qualquer](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Figura 4: Vias de tráfego virtual WAN**

O Azure Virtual WAN suporta os seguintes caminhos globais de conectividade de trânsito. As letras em parênteses mapeiam para a Figura 4.

* Sucursal-para-VNet (a)
* Ramo-a-ramo b
  * ExpressRoute Global Reach e Virtual WAN
* Remoto User-to-VNet (c)
* Remoto Utilizador-a-ramo (d)
* VNet-to-VNet (e)
* Ramo-a-hub-hub-to-Branch (f)
* Branch-to-hub-hub-to-VNet (g)
* VNet-to-hub-hub-to-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Sucursal-para-VNet a e Região Transversal Branch-to-VNet (g)

Branch-to-VNet é o caminho principal suportado pelo Azure Virtual WAN. Este caminho permite-lhe ligar balcões às cargas de trabalho da empresa Azure IAAS que são implantadas em VNets Azure. As sucursais podem ser ligadas ao WAN virtual via ExpressRoute ou VPN site-to-site. Os trânsitos transitam para VNets que estão ligados aos centros virtuais WAN através de Ligações VNet. O trânsito explícito de [gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) não é necessário para o WAN virtual porque o WAN virtual automaticamente permite o trânsito de gateway para o local da sucursal. Consulte o artigo [virtual wan partners](virtual-wan-configure-automation-providers.md) sobre como ligar um CPE SD-WAN ao Wan Virtual.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach e Virtual WAN

O ExpressRoute é uma forma privada e resiliente de ligar as suas redes no local ao Microsoft Cloud. O VIRTUAL WAN suporta as ligações do circuito Express Route. Ligar um site de ramificação ao Virtual WAN com a Rota expressa requer 1) Circuito Premium 2) Circuito para estar num local global ativado.

ExpressRoute Global Reach é uma funcionalidade de complemento para o ExpressRoute. Com o Global Reach, pode ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes no local. Os balcões ligados ao Azure Virtual WAN utilizando o ExpressRoute exigem que o ExpressRoute Global Reach se comunique entre si.

Neste modelo, cada ramo ligado ao hub virtual WAN utilizando o ExpressRoute pode ligar-se a VNets utilizando o caminho ramificação-vNet. O tráfego de ramificação para sucursal não vai transitar o centro porque o ExpressRoute Global Reach permite um caminho mais ideal sobre o Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Ramo-a-ramo b e região transversal ramificação-a-ramo -a-filial f

Os balcões podem ser ligados a um hub wan virtual Azure utilizando circuitos ExpressRoute e/ou ligações VPN site-to-site. Pode ligar os ramos ao centro virtual WAN que se encontra na região mais próxima do ramo.

Esta opção permite que as empresas aproveitem a espinha dorsal azure para ligar balcões. No entanto, mesmo que esta capacidade esteja disponível, deve pesar os benefícios de ligar balcões através do Azure Virtual WAN vs. usando um WAN privado.  

> [!NOTE]
> Desativando a conectividade ramificante em WAN virtual - O WAN virtual pode ser configurado para desativar a conectividade Branch-to-Branch. Esta configuação bloqueará a propagação da rota entre os locais ligados vpn (S2S e P2S) e Express Route. Esta configuração não afetará a propogamoação e conectividade da rota Do ramo-a-Vnet e Vnet-to-Vnet. Para configurar esta definição utilizando o Portal Azure: Sob o menu de configuração virtual WAN, escolha a definição: Branch-to-Branch - Disabled. 

### <a name="remote-user-to-vnet-c"></a>Remoto User-to-VNet (c)

Pode ativar o acesso remoto direto e seguro ao Azure utilizando a ligação ponto-a-local de um cliente utilizador remoto para um WAN virtual. Os utilizadores remotos da Enterprise já não têm de fixar o cabelo na nuvem usando uma VPN corporativa.

### <a name="remote-user-to-branch-d"></a>Remoto Utilizador-a-ramo (d)

O caminho Remoto user-to-branch permite que os utilizadores remotos que estão a usar uma ligação ponto-a-local ao acesso ao Azure acedam às cargas de trabalho e aplicações no local, transitando através da nuvem. Este caminho confere aos utilizadores remotos a flexibilidade para aceder em cargas de trabalho que são implantadas tanto no Azure como no local. As empresas podem ativar o serviço central de acesso remoto seguro baseado em nuvem em Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Trânsito VNet-to-VNet (e) e VNet-to-VNet cross-region (h)

O trânsito VNet-to-VNet permite que os VNets se conectem entre si de forma a interligar aplicações de vários níveis que são implementadas em vários VNets. Opcionalmente, pode ligar VNets entre si através do VNet Peering e isso pode ser adequado para alguns cenários em que o trânsito através do hub VWAN não é necessário.

## <a name="security-and-policy-control"></a><a name="security"></a>Controlo de segurança e política

Os hubs Azure Virtual WAN interligam todos os pontos finais de rede através da rede híbrida e potencialmente vêem todo o tráfego da rede de trânsito. Os hubs virtuais WAN podem ser convertidos para Centros Virtuais Seguros, implantando o Firewall Azure dentro dos hubs VWAN para permitir a segurança, acesso e controlo de políticas baseados na nuvem. A orquestração de Firewalls Azure em centros wan virtuais pode ser executada pelo Azure Firewall Manager.

[O Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) fornece as capacidades para gerir e escalar a segurança das redes de trânsito globais. O Azure Firewall Manager oferece capacidade para gerir centralmente o encaminhamento, a gestão de políticas globais, os serviços avançados de segurança da Internet através de terceiros, juntamente com o Azure Firewall.

![centro virtual seguro com Firewall Azure](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Figura 5: Centro virtual seguro com Firewall Azure**

O Azure Firewall para o WAN virtual suporta os seguintes caminhos globais de conectividade de trânsito garantido. As letras em parênteses mapeiam para a Figura 5.

* Trânsito seguro VNet-to-VNet (e)
* VNet-to-Internet ou Serviço de Segurança de terceiros (i)
* Serviço de Segurança Sucursal para internet ou serviço de segurança de terceiros (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Trânsito seguro VNet-to-VNet (e)

O trânsito seguro VNet-to-VNet permite que os VNets se conectem entre si através da Firewall Azure no centro virtual WAN.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-to-Internet ou Serviço de Segurança de terceiros (i)

O vnet-to-Internet ou o trânsito seguro de terceiros permite que os VNets se conectem à internet ou a serviços de segurança de terceiros suportados através do Firewall Azure no centro virtual WAN.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Serviço de Segurança Sucursal para internet ou serviço de segurança de terceiros (j)
O trânsito seguro de sucursais para internet ou de terceiros permite que as agências se conectem à internet ou a serviços de segurança de terceiros apoiados através do Azure Firewall no centro virtual WAN.

## <a name="next-steps"></a>Passos seguintes

Criar uma ligação utilizando o Virtual WAN e implementar firewall Azure em hub(s VWAN).

* [Ligações site-to-site usando WAN virtual](virtual-wan-site-to-site-portal.md)
* [Ligações ExpressRoute usando WAN virtual](virtual-wan-expressroute-portal.md)
* [Gestor de firewall azure vai implantar Azure FW na VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
