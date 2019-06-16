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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121182"
---
A FAQ de VNet a VNet aplicam-se às ligações do gateway VPN. Para obter informações sobre o VNet peering, veja [peering de rede Virtual](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>O Azure cobra o tráfego entre VNets?

O tráfego VNet a VNet na mesma região é gratuito em ambas as direções, quando utiliza uma ligação de gateway VPN. O tráfego de saída de VNet a VNet entre regiões é cobrado com as taxas de transferência de dados inter-vnet de saída baseadas nas regiões de origem. Para obter mais informações, consulte [página de preços de Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Se estiver a ligar as suas VNets com o VNet peering, em vez de um gateway de VPN, consulte [preços da rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>O tráfego VNet a VNet viaja em toda a internet?

Não. O tráfego de VNet a VNet circula entre o backbone do Microsoft Azure, não na internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Pode estabelecer uma ligação de VNet a VNet em inquilinos do Azure Active Directory (AAD)?

Sim, as ligações de VNet a VNet que utilizam gateways de VPN do Azure funcionam em inquilinos do AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>O tráfego VNet a VNet é seguro?

Sim, está protegido pela encriptação de IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Preciso de um dispositivo VPN para ligar VNets?

Não. A ligação de várias redes virtuais do Azure em conjunto não precisa de um dispositivo VPN, a menos que precise de conectividade em vários locais.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>As minhas VNets precisam de estar na mesma região?

Não. As redes virtuais podem estar nas mesmas regiões ou em regiões (localizações) diferentes do Azure.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Se as VNets não estiverem na mesma subscrição, as subscrições têm de estar associado ao mesmo inquilino do Active Directory?

Não.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Posso utilizar VNet a VNet para ligar redes virtuais em instâncias do Azure separadas? 

Não. A VNet a VNet suporta a ligação de redes virtuais na mesma instância do Azure. Por exemplo, não é possível criar uma ligação entre o global Azure e o chinês/alemão/US administração pública do Azure instâncias. Considere utilizar uma ligação VPN de Site a Site para estes cenários.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Pode utilizar a VNet a VNet juntamente com ligações de vários locais?

Sim. A conectividade de rede virtual pode ser utilizada em simultâneo com VPNs de vários locais.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>A quantos sites no local e redes virtuais pode uma rede virtual ligar?

Consulte a [requisitos do Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) tabela.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Posso utilizar a VNet a VNet para ligar VMs ou serviços cloud fora de uma VNet?

Não. A VNet a VNet suporta a ligação de redes virtuais. Ela não suporta a ligação de máquinas virtuais ou serviços cloud que não estão numa rede virtual.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Pode um serviço cloud ou um ponto final de balanceamento de carga abranger VNets?

Não. Um serviço cloud ou um ponto final de balanceamento de carga não pode abranger várias redes virtuais, mesmo se eles estão conectados juntos.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Pode utilizar um tipo de PolicyBased VPN para ligações de VNet a VNet ou de vários locais?

Não. Ligações de VNet a VNet e de vários sites precisam de gateways de VPN do Azure com RouteBased (anteriormente denominado encaminhamento dinâmico) tipos de VPN.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Posso ligar uma VNet com um Tipo de VPN RouteBased a outra VNet com um tipo de VPN PolicyBased?

Não, ambas as redes virtuais têm de utilizar VPNs baseadas na rota de (anteriormente chamado encaminhamento dinâmico).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Os túneis VPN partilham a largura de banda?

Sim. Todos os túneis VPN da rede virtual partilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA do tempo de atividade do gateway de VPN no Azure.

### <a name="are-redundant-tunnels-supported"></a>Os túneis redundantes são suportados?

Os túneis redundantes entre um par de redes virtuais são suportados quando um gateway de rede virtual está configurado como ativo-ativo.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Pode ter espaços de endereços sobrepostos para configurações de VNet a VNet?

Não. Não pode ter intervalos de endereços IP sobrepostos.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Pode existir uma sobreposição de espaços de endereços entre as redes virtuais ligadas e os sites locais no local?

Não. Não pode ter intervalos de endereços IP sobrepostos.



