---
title: Considerações de networking - Azure Dedicado HSM | Microsoft Docs
description: Visão geral das considerações de networking aplicáveis às implementações do HSM dedicado à Azure
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
ms.openlocfilehash: 3764b261b491c660da16d7989be20742fead1fbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91359159"
---
# <a name="azure-dedicated-hsm-networking"></a>Rede HSM dedicada Azure

O Azure Dedicado HSM requer um ambiente de networking altamente seguro. Isto é verdade quer seja da nuvem Azure de volta ao ambiente de TI do cliente (no local), usando aplicações distribuídas ou para cenários de alta disponibilidade. O Azure Networking fornece este facto e há quatro áreas distintas que devem ser abordadas.

- Criar dispositivos HSM dentro da sua Rede Virtual (VNet) em Azure
- Ligação no local a recursos baseados na nuvem para a configuração e gestão de dispositivos HSM
- Criação e ligação de redes virtuais para recursos de aplicações interligar e dispositivos HSM
- Ligação de redes virtuais entre regiões para inter-comunicações e também para permitir cenários de elevada disponibilidade

## <a name="virtual-network-for-your-dedicated-hsms"></a>Rede virtual para os seus HSMs dedicados

Os HSMs dedicados estão integrados numa Rede Virtual e colocados na própria rede privada de clientes em Azure. Isto permite o acesso aos dispositivos a partir de máquinas virtuais ou recursos de computação na rede virtual.  
Para obter mais informações sobre a integração dos serviços Azure na rede virtual e as capacidades que fornece, consulte a rede Virtual para documentação [dos serviços Azure.](../virtual-network/virtual-network-for-azure-services.md)

### <a name="virtual-networks"></a>Redes virtuais

Antes de a provisionar um dispositivo HSM dedicado, os clientes terão primeiro de criar uma Rede Virtual em Azure ou usar um que já existe na subscrição dos clientes. A rede virtual define o perímetro de segurança do dispositivo dedicado HSM. Para obter mais informações sobre a criação de redes virtuais, consulte [a documentação da rede virtual.](../virtual-network/virtual-networks-overview.md)

### <a name="subnets"></a>Sub-redes

Sub-redes segmentam a rede virtual em espaços de endereço separados utilizáveis pelos recursos Azure que coloca neles. Os HSMs dedicados são implantados numa sub-rede na rede virtual. Cada dispositivo HSM dedicado que seja implantado na sub-rede do cliente receberá um endereço IP privado a partir desta sub-rede. A sub-rede em que o dispositivo HSM é implantado precisa de ser explicitamente delegada no serviço: Microsoft.HardwareSecurityModules/DEDICADOHSMs. Isto concede determinadas permissões ao serviço HSM para implantação na sub-rede. A delegação aos HSMs dedicados impõe certas restrições políticas à sub-rede. Os Grupos de Segurança de Rede (NSGs) e User-Defined Rotas (UDRs) não são atualmente suportados em sub-redes delegadas. Como resultado, uma vez que uma sub-rede é delegada em HSMs dedicados, ele só pode ser usado para implantar recursos HSM. A implantação de quaisquer outros recursos do cliente na sub-rede falhará.


### <a name="expressroute-gateway"></a>Gateway ExpressRoute

Um requisito da arquitetura atual é a configuração de uma porta de entrada ER na sub-rede de clientes onde é necessário colocar um dispositivo HSM para permitir a integração do dispositivo HSM em Azure. Este gateway ER não pode ser utilizado para ligar locais no local aos dispositivos HSM dos clientes em Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ligar o seu TI no local ao Azure

Ao criar recursos baseados na nuvem, é um requisito típico para uma ligação privada de volta aos recursos de TI no local. No caso do HSM dedicado, este será predominantemente para o software cliente HSM configurar os dispositivos HSM e também para atividades como backups e puxar registos de HSMs para análise. Um ponto de decisão fundamental aqui é a natureza da ligação, uma vez que existem opções.  A opção mais flexível é a VPN site-to-site, uma vez que provavelmente haverá vários recursos no local que requerem uma comunicação segura com recursos (incluindo HSMs) na nuvem Azure. Isto exigirá que uma organização de clientes tenha um dispositivo VPN para facilitar a ligação. Uma ligação VPN ponto-a-local pode ser utilizada se houver apenas um único ponto final no local, como uma única estação de trabalho administrativa.
Para obter mais informações sobre opções de conectividade, consulte [as opções de planeamento do Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Neste momento, o ExpressRoute não é uma opção para a ligação aos recursos no local. Note-se também que o Portal ExpressRoute utilizado como acima descrito não se destina a ligações com infraestruturas no local.

### <a name="point-to-site-vpn"></a>VPN ponto-a-local

Uma rede privada virtual de ponto a local é a forma mais simples de ligação segura a um único ponto final no local. Isto pode ser relevante se pretender ter apenas uma estação de trabalho de administração única para HSMs dedicados baseados em Azure.

### <a name="site-to-site-vpn"></a>VPN site a site

Uma Rede Privada Virtual para site permite uma comunicação segura entre os HSMs dedicados baseados em Azure e as suas TI no local. Uma razão para isso é ter uma instalação de reserva para o HSM no local e precisar de uma ligação entre os dois para executar o backup.

## <a name="connecting-virtual-networks"></a>Ligação de redes virtuais

Uma arquitetura típica de implantação para O HSM dedicado começará com uma única rede virtual e sub-rede correspondente na qual os dispositivos HSM são criados e a provisionados. Dentro dessa mesma região, poderia muito bem existirem redes virtuais e sub-redes adicionais para componentes de aplicação que utilizariam o HSM dedicado. Para ativar a comunicação através destas redes, utilizamos o Virtual Network Peering.

### <a name="virtual-network-peering"></a>Peering de rede virtual

Quando existem várias redes virtuais dentro de uma região que precisam de aceder aos recursos uns dos outros, o Virtual Network Peering pode ser usado para criar canais de comunicação seguros entre eles.  O espreitamento de rede virtual fornece não só comunicações seguras, mas também garante uma baixa latência e ligações de alta largura de banda entre os recursos em Azure.

![espreitar a rede](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ligação através das Regiões de Azure

Os dispositivos HSM têm a capacidade, através de bibliotecas de software, de redirecionar o tráfego para um HSM alternativo. A reorientação do tráfego é útil se os dispositivos falharem ou o acesso a um dispositivo for perdido. Os cenários de insuficiência de nível regional podem ser atenuados através da implantação de HSMs noutras regiões e da comunicação entre redes virtuais entre regiões.

### <a name="cross-region-ha-using-vpn-gateway"></a>Região transversal HA usando gateway VPN

Para aplicações distribuídas globalmente ou para cenários de falha regional de alta disponibilidade, é necessário ligar redes virtuais entre regiões. Com o Azure Dedicado HSM, a alta disponibilidade pode ser alcançada usando um Gateway VPN que fornece um túnel seguro entre as duas redes virtuais. Para obter mais informações sobre as ligações Vnet-to-Vnet utilizando o Gateway VPN, consulte o artigo intitulado [O que é VPN Gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> O perspitamento global da Vnet não está disponível em cenários de conectividade entre regiões com HSMs dedicados neste momento e o gateway VPN deve ser usado em vez disso. 

![O diagrama mostra duas regiões ligadas por dois gateways V P N. Cada região contém redes virtuais espreitadas.](media/networking/global-vnet.png)

## <a name="next-steps"></a>Passos seguintes

- [Perguntas mais frequentes](faq.md)
- [Suportabilidade](supportability.md)
- [Elevada disponibilidade](high-availability.md)
- [Segurança Física](physical-security.md)
- [Monitorização](monitoring.md)
- [Arquitetura de implantação](deployment-architecture.md)