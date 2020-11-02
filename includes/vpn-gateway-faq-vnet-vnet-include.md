---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "67184080"
---
O VNet-to-VNet FAQ aplica-se às ligações de gateway VPN. Para obter informações sobre o espremiamento da VNet, consulte [a rede virtual a espreitar](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>O Azure cobra o tráfego entre VNets?

O tráfego VNet-vNet dentro da mesma região é gratuito para ambas as direções quando utiliza uma ligação de gateway VPN. O tráfego de saída da VNet-to-VNet é cobrado com as taxas de transferência de dados inter-VNet de saída com base nas regiões de origem. Para obter mais informações, consulte [a página de preços da VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Se estiver a ligar os seus VNets utilizando o espremiamento VNet em vez de uma porta de entrada VPN, consulte [os preços da rede virtual](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>O tráfego VNet-to-VNet viaja pela internet?

N.º O tráfego VNet-to-VNet atravessa a espinha dorsal do Microsoft Azure, não a internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Posso estabelecer uma ligação VNet-para-VNet através dos inquilinos do Azure Ative Directory (AAD)?

Sim, as ligações VNet-to-VNet que utilizam gateways Azure VPN funcionam em inquilinos da AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>O tráfego VNet a VNet é seguro?

Sim, está protegido pela encriptação IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Preciso de um dispositivo VPN para ligar VNets?

N.º A ligação de várias redes virtuais do Azure em conjunto não precisa de um dispositivo VPN, a menos que precise de conectividade em vários locais.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>As minhas VNets precisam de estar na mesma região?

N.º As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Se os VNets não estiverem na mesma subscrição, as subscrições precisam de ser associadas ao mesmo inquilino ative directory?

N.º

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Posso utilizar VNet a VNet para ligar redes virtuais em instâncias do Azure separadas? 

N.º A VNet a VNet suporta a ligação de redes virtuais na mesma instância do Azure. Por exemplo, não é possível criar uma ligação entre os casos globais do Azure e do governo chinês/alemão/americano Azure. Considere utilizar uma ligação VPN site-to-site para estes cenários.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Pode utilizar a VNet a VNet juntamente com ligações de vários locais?

Sim. A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>A quantos sites no local e redes virtuais pode uma rede virtual ligar?

Consulte a tabela [de requisitos gateway.](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Posso utilizar a VNet a VNet para ligar VMs ou serviços cloud fora de uma VNet?

N.º A VNet a VNet suporta a ligação de redes virtuais. Não suporta a ligação de máquinas virtuais ou serviços em nuvem que não estejam numa rede virtual.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Um serviço de nuvem ou um ponto final de equilíbrio de carga VNets?

N.º Um serviço de nuvem ou um ponto final de equilíbrio de carga não pode abranger redes virtuais, mesmo que estejam ligados entre si.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Posso utilizar um tipo VPN baseado em políticas para ligações VNet-to-VNet ou multi-site?

N.º As ligações VNet-to-VNet e Multi-Site requerem gateways VPN Azure com tipos de VPN RouteBased (anteriormente chamados de encaminhamento dinâmico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Posso ligar uma VNet com um Tipo de VPN RouteBased a outra VNet com um tipo de VPN PolicyBased?

Não, ambas as redes virtuais DEVEM utilizar VPNs baseados em rotas (anteriormente chamados de encaminhamento dinâmico).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Os túneis VPN partilham a largura de banda?

Sim. Todos os túneis VPN da rede virtual partilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA do tempo de atividade do gateway de VPN no Azure.

### <a name="are-redundant-tunnels-supported"></a>Os túneis redundantes são suportados?

Os túneis redundantes entre um par de redes virtuais são suportados quando um gateway de rede virtual está configurado como ativo-ativo.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Pode ter espaços de endereços sobrepostos para configurações de VNet a VNet?

N.º Não pode ter intervalos de endereços IP sobrepostos.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Pode existir uma sobreposição de espaços de endereços entre as redes virtuais ligadas e os sites locais no local?

N.º Não pode ter intervalos de endereços IP sobrepostos.



