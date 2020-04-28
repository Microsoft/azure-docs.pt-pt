---
title: Considerações de networking - Azure Dedicado HSM [ Microsoft Docs
description: Visão geral das considerações de networking aplicáveis às implementações de HSM dedicadas ao Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "70881279"
---
# <a name="azure-dedicated-hsm-networking"></a>Rede HSM dedicada azure

O Azure Dedicated HSM requer um ambiente de networking altamente seguro. Isto é verdade, quer seja da nuvem Azure de volta ao ambiente de TI do cliente (no local), usando aplicações distribuídas ou para cenários de alta disponibilidade. A Azure Networking fornece-o e há quatro áreas distintas que devem ser abordadas.

- Criação de dispositivos HSM dentro da sua Rede Virtual (VNet) em Azure
- Ligação no local aos recursos baseados na nuvem para a configuração e gestão de dispositivos HSM
- Criação e ligação de redes virtuais para recursos de aplicação inter-conectantes e dispositivos HSM
- Ligar redes virtuais através de regiões para intercomunicação e também para permitir cenários de alta disponibilidade

## <a name="virtual-network-for-your-dedicated-hsms"></a>Rede virtual para os seus HSMs dedicados

Os HSMs dedicados são integrados numa Rede Virtual e colocados na própria rede privada de clientes em Azure. Isto permite o acesso aos dispositivos a partir de máquinas virtuais ou recursos de computação na rede virtual.  
Para obter mais informações sobre a integração dos serviços Azure na rede virtual e as capacidades que fornece, consulte a rede Virtual para a documentação dos [serviços Azure.](../virtual-network/virtual-network-for-azure-services.md)

### <a name="virtual-networks"></a>Redes virtuais

Antes de fornecer um dispositivo HSM dedicado, os clientes precisarão primeiro de criar uma Rede Virtual em Azure ou usar uma que já existe na subscrição dos clientes. A rede virtual define o perímetro de segurança para o dispositivo HSM dedicado. Para obter mais informações sobre a criação de redes virtuais, consulte documentação de [rede virtual.](../virtual-network/virtual-networks-overview.md)

### <a name="subnets"></a>Sub-redes

As subredes segmentam a rede virtual em espaços de endereços separados utilizáveis pelos recursos Azure que você colocar neles. HSMs dedicados são implantados numa sub-rede na rede virtual. Cada dispositivo HSM dedicado que é implantado na subnet do cliente receberá um endereço IP privado desta subnet. A sub-rede em que o dispositivo HSM é implantado precisa de ser explicitamente delegada no serviço: Microsoft.HardwareSecurityModules/dedicados HSMs. Isto concede certas permissões ao serviço HSM para implantação na sub-rede. A delegação aos HSMs dedicados impõe certas restrições políticas à sub-rede. Atualmente, os Grupos de Segurança da Rede (NSGs) e as Rotas Definidas pelo Utilizador (UDRs) não são suportados nas subredes delegadas. Como resultado, uma vez que uma sub-rede é delegada em HSMs dedicados, só pode ser usada para implantar recursos HSM. A implantação de quaisquer outros recursos de clientes na sub-rede falhará.


### <a name="expressroute-gateway"></a>Gateway ExpressRoute

Um requisito da arquitetura atual é a configuração de um gateway ER na subnet dos clientes onde é necessário colocar um dispositivo HSM para permitir a integração do dispositivo HSM no Azure. Este gateway ER não pode ser utilizado para ligar as localizações no local aos dispositivos HSM dos clientes em Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ligando o seu IT no local ao Azure

Ao criar recursos baseados na nuvem, é um requisito típico para uma ligação privada de volta aos recursos de TI no local. No caso do HSM dedicado, este será predominantemente para o software cliente HSM configurar os dispositivos HSM e também para atividades como backups e puxar registos de HSMs para análise. Um ponto de decisão fundamental aqui é a natureza da ligação, uma vez que existem opções.  A opção mais flexível é a VPN site-to-site, uma vez que provavelmente haverá vários recursos no local que requerem uma comunicação segura com recursos (incluindo HSMs) na nuvem Azure. Isto exigirá que uma organização de clientes tenha um dispositivo VPN para facilitar a ligação. Uma ligação VPN ponto-a-local pode ser utilizada se houver apenas um ponto final no local, como uma única estação de trabalho de administração.
Para obter mais informações sobre as opções de conectividade, consulte opções de [planeamento vpN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Neste momento, o ExpressRoute não é uma opção para a ligação aos recursos no local. Note-se também que o Gateway ExpressRoute utilizado acima, tal como acima descrito, não se destina a ligações com infraestruturas no local.

### <a name="point-to-site-vpn"></a>VPN Ponto a Site

Uma rede privada virtual ponto-a-site é a forma mais simples de ligação segura a um único ponto final no local. Isto pode ser relevante se pretender ter apenas uma estação de trabalho de administração única para HSMs dedicados baseados em Azure.

### <a name="site-to-site-vpn"></a>VPN site a site

Uma rede virtual privada de site-a-site permite uma comunicação segura entre hSMs dedicados baseados em Azure e o seu IT no local. Uma razão para fazer isso é ter uma instalação de reserva para o HSM no local e precisar de uma ligação entre os dois para executar o backup.

## <a name="connecting-virtual-networks"></a>Ligação de redes virtuais

Uma arquitetura típica de implantação para HSM dedicado começará com uma única rede virtual e subnet correspondente em que os dispositivos HSM são criados e aprovisionados. Nessa mesma região, poderia muito bem haver redes virtuais e subredes adicionais para componentes de aplicação que utilizariam o HSM dedicado. Para permitir a comunicação através destas redes, utilizamos o Virtual Network Peering.

### <a name="virtual-network-peering"></a>Peering de rede virtual

Quando existem várias redes virtuais dentro de uma região que precisam de aceder aos recursos uns dos outros, o Virtual Network Peering pode ser usado para criar canais de comunicação seguros entre eles.  O epeering de rede virtual fornece não só comunicações seguras, mas também garante uma baixa latência e ligações de largura de banda entre os recursos em Azure.

![peering rede](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ligação através das regiões de Azure

Os dispositivos HSM têm a capacidade, através de bibliotecas de software, de redirecionar o tráfego para um HSM alternativo. A reorientação do tráfego é útil se os dispositivos falharem ou se perder o acesso a um dispositivo. Os cenários de falha a nível regional podem ser atenuados através da implantação de HSMs noutras regiões e permitindo a comunicação entre redes virtuais entre regiões.

### <a name="cross-region-ha-using-vpn-gateway"></a>Região transversal HA usando gateway VPN

Para aplicações distribuídas globalmente ou para cenários de falhas regionais de alta disponibilidade, é necessário ligar redes virtuais através de regiões. Com o Azure Dedicado HSM, a alta disponibilidade pode ser alcançada através de um Gateway VPN que fornece um túnel seguro entre as duas redes virtuais. Para obter mais informações sobre as ligações Vnet-to-Vnet utilizando o VPN Gateway, consulte o artigo intitulado [O que é VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> O peering global Vnet não está disponível em cenários de conectividade transversal com HSMs dedicados neste momento e o gateway VPN deve ser usado em vez disso. 

![global-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Passos seguintes

- [Perguntas mais frequentes](faq.md)
- [Suportabilidade](supportability.md)
- [Elevada disponibilidade](high-availability.md)
- [Segurança Física](physical-security.md)
- [Monitorização](monitoring.md)
- [Arquitetura de implantação](deployment-architecture.md)