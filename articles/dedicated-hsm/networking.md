---
title: Considerações sobre o funcionamento em rede - HSM dedicada do Azure | Documentos da Microsoft
description: Descrição geral do funcionamento em rede considerações aplicáveis a implementações de HSM dedicados do Azure
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: barclayn
ms.openlocfilehash: aed0eb6ba4cdaa57d282ac4484e0c27c0697afb5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58083135"
---
# <a name="azure-dedicated-hsm-networking"></a>Redes de HSM dedicados do Azure

HSM dedicada do Azure requer um ambiente de rede altamente seguro. Isso é verdadeiro quer se trate do Azure na cloud para o cliente com aplicações distribuídas ambiente de TI (no local), ou para cenários de elevada disponibilidade. Redes do Azure fornecem isso e há quatro áreas distintas que devem ser resolvidas.

- Criar dispositivos HSM no interior da rede Virtual (VNet) no Azure
- Ligar no local para recursos baseados na nuvem para a configuração e gestão de dispositivos HSM
- Criar e ligar redes virtuais para a ligação entre recursos de aplicativos e dispositivos HSM
- Ligar redes virtuais em várias regiões para comunicação entre e também para ativar cenários de elevada disponibilidade

## <a name="virtual-network-for-your-dedicated-hsms"></a>Rede virtual para seus HSMs dedicados

HSMs dedicados são integrados numa rede Virtual e colocados na própria rede privada de clientes no Azure. Isso permite o acesso aos dispositivos de máquinas virtuais ou recursos de computação na rede virtual.  
Para obter mais informações sobre como integrar os serviços do Azure para a rede virtual e os recursos que fornece, veja [rede Virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md) documentação.

### <a name="virtual-networks"></a>Redes virtuais

Antes de aprovisionar um dispositivo de HSM dedicados, os clientes terão primeiro criar uma rede Virtual no Azure ou utilizar um que já exista na subscrição aos clientes. A rede virtual define o perímetro de segurança para o dispositivo de HSM dedicados. Para obter mais informações sobre como criar redes virtuais, consulte [documentação da rede virtual](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Sub-redes

Sub-redes segmentar a rede virtual em espaços de endereço separados utilizáveis pelos recursos do Azure que coloca nos mesmos. HSMs dedicados são implementados numa sub-rede na rede virtual. Cada dispositivo HSM dedicado que é implementado na sub-rede do cliente irão receber um endereço IP privado desta sub-rede. A sub-rede na qual o dispositivo HSM está implementado tem de ser explicitamente delegada para o serviço: Microsoft.HardwareSecurityModules/dedicatedHSMs. Esta ação garante determinadas permissões para o serviço HSM para a implementação para a sub-rede. Delegação de HSMs dedicados impõe algumas restrições de política na sub-rede. Grupos de segurança de rede (NSGs) e rotas definidas pelo utilizador (UDRs) não são atualmente suportadas em sub-redes do delegado. Como resultado, depois de uma sub-rede é delegada para HSMs dedicados, ele só pode ser utilizado para implementar recursos HSM. Implementação de quaisquer outros recursos de cliente para a sub-rede irá falhar.


### <a name="expressroute-gateway"></a>Gateway do ExpressRoute

Um requisito da arquitetura atual é a configuração de um gateway de ER na sub-rede de clientes em que um dispositivo HSM tem de ser colocados para ativar a integração do dispositivo HSM para o Azure. Este gateway de ER não pode ser utilizado para estabelecer ligação a localizações no local para os dispositivos HSM de clientes no Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ligar no local que a TI do Azure

Ao criar recursos baseados na nuvem, é um requisito típico para uma ligação privada para o local recursos de TI. No caso de HSM dedicados, este será predominantemente para o software de cliente do HSM configurar os dispositivos HSM e também para atividades, como cópias de segurança e extrair os registos de HSMs para análise. Um ponto de decisão principais aqui é a natureza da conexão, pois existem opções.  A opção mais flexível é VPN Site a Site, como provavelmente haverá vários recursos no local que exigem uma comunicação segura com os recursos (incluindo HSMs) na cloud do Azure. Isso exigirá uma organização do cliente para que um dispositivo VPN para facilitar a ligação. Pode ser utilizada uma ligação VPN ponto a Site, se houver apenas um ponto de extremidade único no local, como uma estação de trabalho de administração único.
Para obter mais informações sobre as opções de conectividade, consulte [opções de planeamento de Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Neste momento, o ExpressRoute não é uma opção para a ligação a recursos no local. Também deve observar que o Gateway do ExpressRoute utilizado conforme descrito acima, não é para ligações a infraestrutura no local.

### <a name="point-to-site-vpn"></a>VPN ponto a Site

Uma rede privada Virtual do ponto a site é a forma mais simples de uma ligação segura para um único ponto final no local. Isto pode ser relevante se pretende ter apenas uma estação de trabalho de administração único para HSMs dedicados baseada no Azure.

### <a name="site-to-site-vpn"></a>VPN site a Site

Uma rede privada Virtual do site a site permite a comunicação segura entre baseada no Azure HSMs dedicados e no local IT. Um motivo para fazer isso é ter um recurso de backup para o HSM no local e que precisam de uma ligação entre os dois para executar a cópia de segurança.

## <a name="connecting-virtual-networks"></a>Ligar redes virtuais

Uma arquitetura de implementação típica para o HSM dedicados serão iniciados com uma única rede virtual e sub-rede correspondentes na qual os dispositivos HSM são criados e aprovisionados. Dentro dessa mesma região, pode também haver as redes virtuais adicionais e sub-redes de componentes da aplicação que faria a utilizar o HSM dedicados. Para ativar a comunicação entre estas redes, podemos usar o Peering de rede Virtual.

### <a name="virtual-network-peering"></a>Peering de rede virtual

Quando existem várias redes virtuais dentro de uma região que precisam de aceder a recursos uns dos outros, o Peering de rede Virtual pode ser utilizado para criar canais de comunicação segura entre eles.  Peering de rede virtual fornece não apenas proteger as comunicações, mas também garante um ligações de baixa latência e largura de banda alta entre recursos no Azure.

![Peering de rede](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ligar-se em regiões do Azure

Os dispositivos HSM têm a capacidade, por meio de bibliotecas de software, para redirecionar o tráfego para um HSM alternativo. Redirecionamento de tráfego é útil se os dispositivos não conseguem ou acesso a um dispositivo é perdido. Cenários de falha de nível regional podem ser atenuados pela implementação de HSMs noutras regiões e ativar a comunicação entre redes virtuais em várias regiões.

### <a name="cross-region-ha-using-vpn-gateway"></a>Entre regiões HA com o gateway de VPN

Para aplicações distribuídas globalmente ou para cenários de ativação pós-falha de elevada disponibilidade, é necessário para ligar redes virtuais em várias regiões. Com o HSM do Azure dedicado, elevada disponibilidade pode ser conseguida com um Gateway de VPN que fornece um túnel seguro entre as duas redes virtuais. Para obter mais informações sobre ligações de Vnet a Vnet com o Gateway de VPN, consulte o artigo intitulado [o que é o Gateway de VPN?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Global Vnet peering não está disponível na conectividade entre regiões, cenários com HSMs dedicados neste momento e o gateway de VPN devem ser utilizados em vez disso. 

![global vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Passos Seguintes

- [Perguntas mais frequentes](faq.md)
- [Capacidade de suporte](supportability.md)
- [Elevada disponibilidade](high-availability.md)
- [Segurança física](physical-security.md)
- [Monitorização](monitoring.md)
- [Arquitetura de implantação](deployment-architecture.md)