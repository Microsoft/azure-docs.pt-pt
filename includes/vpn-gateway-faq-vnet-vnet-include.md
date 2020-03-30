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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184080"
---
O VNet-to-VNet FAQ aplica-se às ligações de gateway VPN. Para obter informações sobre o peering vNet, consulte [o peering](../articles/virtual-network/virtual-network-peering-overview.md)da rede virtual .

### <a name="does-azure-charge-for-traffic-between-vnets"></a>O Azure cobra o tráfego entre VNets?

O tráfego VNet-to-VNet dentro da mesma região é gratuito para ambas as direções quando utilizar uma ligação de gateway VPN. O tráfego de egress VNet-to-VNet é cobrado com as taxas de transferência de dados inter-VNet de saída com base nas regiões de origem. Para mais informações, consulte [a página de preços vpN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Se estiver a ligar os seus VNets utilizando o vNet peering em vez de um gateway VPN, consulte os preços da [rede virtual](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>O tráfego VNet-to-VNet viaja pela internet?

Não. O tráfego VNet-to-VNet viaja através da espinha dorsal do Microsoft Azure, não da internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Posso estabelecer uma ligação VNet-to-VNet através de inquilinos do Azure Ative Directory (AAD) ?

Sim, as ligações VNet-to-VNet que usam gateways Azure VPN funcionam em inquilinos DaAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>O tráfego VNet a VNet é seguro?

Sim, está protegido pela encriptação IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Preciso de um dispositivo VPN para ligar VNets?

Não. A ligação de várias redes virtuais do Azure em conjunto não precisa de um dispositivo VPN, a menos que precise de conectividade em vários locais.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>As minhas VNets precisam de estar na mesma região?

Não. As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Se os VNets não estiverem na mesma subscrição, as subscrições precisam de ser associadas ao mesmo inquilino do Diretório Ativo?

Não.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Posso utilizar VNet a VNet para ligar redes virtuais em instâncias do Azure separadas? 

Não. A VNet a VNet suporta a ligação de redes virtuais na mesma instância do Azure. Por exemplo, não se pode criar uma ligação entre os casos globais do Azure e do governo chinês/alemão/norte-americano Azure. Considere utilizar uma ligação VPN site-to-site para estes cenários.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Pode utilizar a VNet a VNet juntamente com ligações de vários locais?

Sim. A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>A quantos sites no local e redes virtuais pode uma rede virtual ligar?

Consulte a tabela [de requisitos gateway.](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Posso utilizar a VNet a VNet para ligar VMs ou serviços cloud fora de uma VNet?

Não. A VNet a VNet suporta a ligação de redes virtuais. Não suporta ligar máquinas virtuais ou serviços de nuvem que não estejam numa rede virtual.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Pode um serviço de nuvem ou um ponto final de equilíbrio de carga abrangem VNets?

Não. Um serviço de nuvem ou um ponto final de equilíbrio de carga não pode abranger redes virtuais, mesmo que estejam ligados entre si.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Posso usar um tipo VPN baseado em políticas para ligações VNet-to-VNet ou multi-site?

Não. As ligações VNet-to-VNet e Multi-Site requerem gateways Azure VPN com tipos VPN baseados em rotas (anteriormente chamados de routing dinâmico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Posso ligar uma VNet com um Tipo de VPN RouteBased a outra VNet com um tipo de VPN PolicyBased?

Não, ambas as redes virtuais DEVEM utilizar VPNs baseadas em rotas (anteriormente chamadas de encaminhamento dinâmico).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Os túneis VPN partilham a largura de banda?

Sim. Todos os túneis VPN da rede virtual partilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA do tempo de atividade do gateway de VPN no Azure.

### <a name="are-redundant-tunnels-supported"></a>Os túneis redundantes são suportados?

Os túneis redundantes entre um par de redes virtuais são suportados quando um gateway de rede virtual está configurado como ativo-ativo.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Pode ter espaços de endereços sobrepostos para configurações de VNet a VNet?

Não. Não pode ter intervalos de endereços IP sobrepostos.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Pode existir uma sobreposição de espaços de endereços entre as redes virtuais ligadas e os sites locais no local?

Não. Não pode ter intervalos de endereços IP sobrepostos.



