---
title: Arquitetura de rede global de trânsito Virtual WAN do Azure | Documentos da Microsoft
description: Saiba mais sobre a arquitetura de rede de trânsito global para Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 114d11f98c6181a03f5ce52527b5e2efea468c42
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65965979"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Arquitetura de rede de trânsito global e Virtual WAN

Arquitetura de rede de trânsito global está a ser adotada por empresas para consolidar, ligar e controlar a empresa centralizado em cloud moderna pegada digital. Numa empresa voltada para a cloud moderna, o tráfego de rede não precisa de ser backhauled a sede. Arquitetura de rede de trânsito global se baseia em conceitos de rede familiares e novos conceitos que são exclusivos para a cloud e arquiteturas baseadas na cloud.

![arquitetura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Figura 1: Rede de trânsito global com o Virtual WAN**

As empresas modernas exigem a conectividade ubíqua entre aplicativos hyper distribuído, dados e os utilizadores na cloud e no local. WAN Virtual do Azure permite uma arquitetura de rede de trânsito global ao possibilitar onipresente, qualquer para qualquer conectividade entre conjuntos de distribuição global de VNets, sites, aplicações e utilizadores. WAN Virtual do Azure é um serviço gerido pela Microsoft. Todos os componentes de rede que este serviço é composto por alojados e geridos pela Microsoft. Para obter mais informações sobre o Virtual WAN, consulte a [descrição geral de WAN Virtual](virtual-wan-about.md) artigo.

Na arquitetura de WAN Virtual do Azure, regiões do Azure servem de hubs ao qual pode optar por ligar seus ramos. Assim que os ramos estão ligados, pode tirar partido do backbone do Azure para estabelecer o ramo-para-VNet e, opcionalmente, conectividade de ramificação para ramificação.

Pode estabelecer uma WAN virtual ao criar um hub de Virtual WAN único na região que tem o maior número de spokes (ramos, VNets, os utilizadores) e, em seguida, ligar os spokes que estão em outras regiões para o hub. Em alternativa, se spokes são distribuídos geograficamente, também pode criar uma instância de hubs regionais e os hubs de interligações. Os hubs fazem parte do mesmo WAN virtual, mas eles podem ser associados a políticas regionais diferentes.

## <a name="hub"></a>Trânsito de hub-and-spoke

A arquitetura de rede de trânsito global baseia-se um modelo de conectividade do clássico hub-and-spoke onde a rede de nuvem alojada "hub" permite conectividade transitiva entre pontos de extremidade que podem ser distribuídos por diferentes tipos de 'spokes'.
  
Nesse modelo, pode ser um spoke:

* Rede virtual (VNets)
* Local de ramificação físico
* Utilizador remoto
* Internet

![Diagrama de trânsito global de hub- and -spoke](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Figura 2: Hub-and-spoke**

Figura 2 mostra a exibição lógica da rede global em que os utilizadores distribuídos geograficamente, sites físicos e VNets estão interligadas através de um hub de rede alojado na cloud. Esta arquitetura permite a conectividade de lógica de um salto de trânsito entre os pontos de extremidade de rede. Os spokes são ligados ao concentrador de por vários serviços do Azure funcionamento em rede, tais como VPN de site para site ou ExpressRoute para ramos físicos, o VNet peering para VNets e ponto-para-site VPN para usuários remotos.

## <a name="crossregion"></a>Conectividade entre regiões

Para uma empresa, uma pegada de nuvem normalmente segue os requisitos de espaço físico. A maioria das empresas acessar a nuvem a partir de uma região mais próxima dos seus sites físicos e utilizadores. Um dos expoentes da arquitetura de rede global de chave é habilitar a conectividade entre regiões entre entidades de rede e os pontos finais. Requisitos de espaço de nuvem podem abranger várias regiões. Isso significa que o tráfego de uma ramificação que está ligado à cloud numa única região contactar noutro ramo ou de uma VNet na região diferente com a conectividade do hub de hub que está atualmente em pré-visualização.

## <a name="any"></a>Conectividade qualquer a qualquer

Arquitetura de rede de trânsito global permite *uma conectividade qualquer a qualquer* através de um hub de rede central. Esta arquitetura elimina ou reduz a necessidade de malha completa ou modelos de conectividade de malha parcial mais complexas para criar e manter. Além disso, o controle de encaminhamento no hub-and-spoke versus redes de malha é mais fácil de configurar e manter.

Uma conectividade qualquer a qualquer, no contexto de uma arquitetura global, permite que uma empresa com utilizadores e distribuídos globalmente, ramificações, datacenters, VNets e aplicações se liguem entre si através do hub de trânsito. O hub de trânsito funciona como o sistema de trânsito global.

![caminhos de tráfego](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Figura 3: Caminhos de tráfego da WAN virtual**

WAN Virtual do Azure suporta os seguintes caminhos de conectividade de trânsito global. As letras de mapa de parênteses para a figura 3.

* Ramo-para-VNet (a)  
* Ramificação para ramificação (b)
* Utilizador-para-VNet remota (c)
* Utilizador-para-filiais remotas (d)
* VNet para VNet com peering de VNet (e)
* Alcance Global do ExpressRoute 

### <a name="branchvnet"></a>Ramo-para-VNet

Ramo-para-VNet é o caminho principal suportado pela WAN Virtual do Azure. Este caminho permite-lhe ligar ramificações para cargas de trabalho de IAAS do Azure enterprise que são implementadas em VNets do Azure. Ramificações podem ser ligadas a WAN virtual através do ExpressRoute ou VPN de site a site. Transits o tráfego para VNets que estão ligados para os hubs de WAN virtual através de ligações de VNet.

### <a name="branchbranch"></a>Ramo para o ramo

Ramificações podem ser ligadas a um hub de WAN Virtual do Azure utilizando a circuitos do ExpressRoute e/ou de ligações de VPN de site a site. Pode ligar-se as ramificações para o hub de Virtual WAN que está na região mais próxima para o ramo.

Esta opção permite que as empresas a aproveitar o backbone do Azure para ligar os ramos. No entanto, mesmo que esta capacidade está disponível, deve pesar as vantagens de estabelecer ligação ramos através da WAN Virtual do Azure vs. por uma WAN particular.

### <a name="usertovnet"></a>Remoto utilizador-para-VNet

Pode ativar o acesso remoto seguro e direto para o Azure através de ligações de ponto a site de um cliente de utilizador remoto a uma WAN virtual. Os usuários remotos de Enterprise já não é precisam hairpin à cloud através de uma VPN empresarial.

### <a name="usertobranch"></a>Utilizador para sucursais

O caminho de utilizador para sucursais permite que os utilizadores remotos que estão a utilizar uma ligação ponto a site para cargas de trabalho do acesso do Azure no local e aplicações ao trânsito através da cloud. Este caminho proporciona aos utilizadores remotos a flexibilidade para cargas de trabalho de acesso que são implementado no Azure e no local. As empresas podem ativar o serviço central com base na cloud acesso remoto seguro na WAN Virtual do Azure.

### <a name="vnetvnet"></a>Trânsito de VNet a VNet com peering de VNet

Para ligar VNets entre si para suportar aplicações de várias camadas que estão implementadas em várias VNets, utilize o VNet peering. Um cenário de trânsito do VNet a VNet através de WAN Virtual do Azure não é atualmente suportado, mas está planeado do Azure. Ligar VNets através de VNet Peering é a solução recomendada para VNets que precisam de estar ligados entre si. [O trânsito de gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (no contexto do VNet peering) não é necessária para Virtual WAN porque a Virtual WAN ativa automaticamente o trânsito de gateway.

### <a name="globalreach"></a>Alcance Global do ExpressRoute

O ExpressRoute é uma forma privada e resiliente para ligar as suas redes no local para a Cloud da Microsoft. Alcance Global do ExpressRoute é uma funcionalidade de suplemento do ExpressRoute. Com o alcance Global, pode ligar os circuitos do ExpressRoute em conjunto para garantir uma rede privada entre as redes no local. Ramos que estão ligados ao Azure WAN Virtual com o ExpressRoute exigem o ExpressRoute alcance Global comunicar entre si.

Nesse modelo, cada ramo que está ligado ao hub de Virtual WAN com o ExpressRoute pode ligar-se para VNets com o caminho de ramificação-para-VNet. Tráfego de ramificação para ramificação não transitar o hub como o alcance Global do ExpressRoute permite que um caminho mais ideal através de WAN do Azure.

## <a name="security"></a>Segurança e controle de diretivas

O hub de rede virtual interconexões e potencialmente vê todo o tráfego de trânsito. Pode ser o local para as funções de sistema de rede central host e serviços, como tal um encaminhamento de cloud, política de rede e a segurança e controlo de acesso de Internet.

## <a name="next-steps"></a>Passos Seguintes

Crie uma ligação com o Virtual WAN.

* [Ligações de site a site com o Virtual WAN](virtual-wan-site-to-site-portal.md)
* [Ligações de ponto a site com o Virtual WAN](virtual-wan-point-to-site-portal.md)
* [Ligações do ExpressRoute com Virtual WAN](virtual-wan-expressroute-portal.md)
