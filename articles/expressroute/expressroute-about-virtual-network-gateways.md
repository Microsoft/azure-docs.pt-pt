---
title: Sobre os gateways de rede virtual ExpressRoute - Azure| Microsoft Docs
description: Saiba mais sobre as portas de rede virtuais para ExpressRoute. Este artigo inclui informações sobre skus de gateway e tipos.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: duau
ms.openlocfilehash: 45b059784cc0b442b615a2a1cb50386da6ee990f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740924"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Sobre os gateways de rede virtual ExpressRoute

Para ligar a sua rede virtual Azure e a sua rede no local via ExpressRoute, tem de criar primeiro um gateway de rede virtual. Um gateway de rede virtual serve dois propósitos: trocar rotas IP entre as redes e tráfego de rede de rotas. Este artigo explica tipos de gateway, GATEWAY SKUs, e desempenho estimado pela SKU. Este artigo também explica o ExpressRoute [FastPath](#fastpath), uma funcionalidade que permite que o tráfego de rede a partir da sua rede no local contorne o gateway de rede virtual para melhorar o desempenho.

## <a name="gateway-types"></a>Tipos de gateway

Quando criar um gateway de rede virtual, tem de especificar várias definições. Uma das definições necessárias, 'GatewayType', especifica se o gateway é utilizado para o tráfego ExpressRoute ou VPN. Os dois tipos de gateway são:

* **VPN** - Para enviar tráfego encriptado através da Internet pública, utilize o tipo de gateway 'Vpn'. Isto também é referido como um portal VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

* **ExpressRoute** - Para enviar tráfego de rede numa ligação privada, utilize o tipo de gateway 'ExpressRoute'. Isto também é referido como um gateway ExpressRoute e é o tipo de porta de entrada usado ao configurar ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs de Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se quiser atualizar o seu gateway para um gateway SKU mais potente, na maioria dos casos pode utilizar o cmdlet PowerShell 'Resize-AzVirtualNetworkGateway'. Isto funcionará para upgrades para SKUs Standard e HighPerformance. No entanto, para atualizar para o SKU UltraPerformance, terá de recriar o gateway. Recriar um portal incorre no tempo de inatividade.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Desempenhos estimados por gateway SKU
A tabela a seguir mostra os tipos de gateway e as performances estimadas. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> O desempenho da aplicação depende de vários fatores, tais como a latência de ponta a ponta, e o número de fluxos de tráfego que a aplicação abre. Os números na tabela representam o limite superior que a aplicação pode teoricamente atingir num ambiente ideal.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Sub-rede de gateway

Antes de criar um gateway ExpressRoute, tem de criar uma sub-rede de gateway. A sub-rede gateway contém os endereços IP que os VMs e serviços de gateway de rede virtual utilizam. Quando cria o seu gateway de rede virtual, os VMs de gateway são implantados na sub-rede gateway e configurados com as definições de gateway ExpressRoute necessárias. Nunca coloque mais nada (por exemplo, VMs adicionais) na sub-rede gateway. A sub-rede gateway deve ser denominada "GatewaySubnet" para funcionar corretamente. Nomear a sub-rede de gateway 'GatewaySubnet' permite ao Azure saber que esta é a sub-rede para implantar os VMs e serviços de gateway de rede virtual.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede gateway são atribuídos aos VMs de gateway e serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. 

Quando estiver a planear o tamanho da sub-rede gateway, consulte a documentação para a configuração que está a planear criar. Por exemplo, a configuração coexistição ExpressRoute/VPN Gateway requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, pode querer certificar-se de que a sub-rede gateway contém endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora possa criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede de gateway de /27 ou maior (/27, /26 etc.) se tiver o espaço de endereço disponível para o fazer. Se estiver a criar uma sub-rede de gateway de pilha dupla, recomendamos que utilize também uma gama IPv6 de /64 ou maior. Isto acomodará a maioria das configurações.

O exemplo seguinte do Gestor de Recursos PowerShell mostra uma sub-rede de gateway chamada GatewaySubnet. Pode ver que a notação CIDR especifica uma /27, que permite endereços IP suficientes para a maioria das configurações que existem atualmente.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>SKUs de gateway redundante de zona

Também pode implementar gateways ExpressRoute em Zonas de Disponibilidade Azure. Isto separa-os física e logicamente em diferentes Zonas de Disponibilidade, protegendo a conectividade da rede no local para o Azure de falhas ao nível da zona.

![Gateway ExpressRoute redundante zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Os gateways redundantes da zona utilizam novos SKUs específicos para gateway ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

O novo gateway SKUs também suporta outras opções de implementação para melhor corresponder às suas necessidades. Ao criar um gateway de rede virtual utilizando o novo gateway SKUs, você também tem a opção de implementar o gateway em uma zona específica. Isto é referido como um portal zonal. Quando você implanta um gateway zonal, todos os casos do gateway são implantados na mesma Zona de Disponibilidade.

> [!IMPORTANT]
> Se planeia utilizar o peering privado baseado no IPv6 sobre o ExpressRoute, certifique-se de selecionar um SKU AZ para o gateway que implementa numa sub-rede de gateway de pilha dupla.
> 
>

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

O Gateway de rede virtual ExpressRoute foi concebido para trocar rotas de rede e tráfego de rede de rotas. O FastPath foi concebido para melhorar o desempenho do caminho de dados entre a sua rede no local e a sua rede virtual. Quando ativado, o FastPath envia tráfego de rede diretamente para máquinas virtuais na rede virtual, contornando o gateway.

Para obter mais informações sobre o FastPath, incluindo limitações e requisitos, consulte [About FastPath](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST APIs e PowerShell cmdlets
Para obter recursos técnicos adicionais e requisitos específicos de sintaxe ao utilizar cmdlets REST APIs e PowerShell para configurações de gateway de rede virtual, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0#azure) |[PowerShell](/powershell/module/az.network#networking) |
| [API REST](/previous-versions/azure/reference/jj154113(v=azure.100)) |[API REST](/rest/api/virtual-network/) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações de ligação disponíveis, consulte [a Visão Geral do ExpressRoute](expressroute-introduction.md).

Para obter mais informações sobre a criação de gateways ExpressRoute, consulte [Criar uma porta de entrada de rede virtual para ExpressRoute.](expressroute-howto-add-gateway-resource-manager.md)

Para obter mais informações sobre a configuração de portas redundantes de zona, consulte [Criar um gateway de rede virtual redundante em zonas](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Para mais informações sobre o FastPath, consulte [About FastPath](about-fastpath.md).