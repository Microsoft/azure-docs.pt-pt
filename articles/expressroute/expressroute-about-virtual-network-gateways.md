---
title: Sobre os gateways da rede virtual ExpressRoute - Azure. Microsoft Docs
description: Conheça os gateways de rede virtuais para o ExpressRoute. Este artigo inclui informações sobre as SKUs e tipos de gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79281421"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Sobre gateways de rede virtual ExpressRoute

Para ligar a sua rede virtual Azure e a sua rede no local via ExpressRoute, tem de criar primeiro um portal de rede virtual. Um portal de rede virtual serve dois propósitos: trocar rotas IP entre as redes e o tráfego da rede de rotas. Este artigo explica os tipos de gateway, gateway SKUs e desempenho estimado pela SKU. Este artigo também explica o ExpressRoute [FastPath,](#fastpath)uma funcionalidade que permite ao tráfego de rede a partir da sua rede no local contornar a porta de entrada de rede virtual para melhorar o desempenho.

## <a name="gateway-types"></a>Tipos de gateway

Quando cria um portal de rede virtual, precisa especificar várias definições. Uma das definições necessárias, 'GatewayType', especifica se o gateway é utilizado para o tráfego ExpressRoute ou VPN. Os dois tipos de gateway são:

* **Vpn** - Para enviar tráfego encriptado através da Internet pública, utiliza o tipo de gateway 'Vpn'. Isto também é referido como um gateway VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

* **ExpressRoute** - Para enviar tráfego de rede numa ligação privada, utiliza o tipo de gateway 'ExpressRoute'. Isto também é referido como um gateway ExpressRoute e é o tipo de gateway usado na configuração do ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs de gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se quiser atualizar a sua porta de entrada para um Portal De entrada mais potente SKU, na maioria dos casos pode utilizar o cmdlet PowerShell 'Resize-AzVirtualNetworkGateway'. Isto funcionará para upgrades para SKUs Standard e HighPerformance. No entanto, para fazer o upgrade para o UltraPerformance SKU, terá de recriar o portal. Recriar um portal incorre no tempo de inatividade.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Desempenhos estimados por Gateway SKU
A tabela seguinte mostra os tipos de gateway e as performances estimadas. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> O desempenho da aplicação depende de múltiplos fatores, como a latência de ponta a ponta, e o número de fluxos de tráfego que a aplicação abre. Os números na tabela representam o limite superior que a aplicação pode teoricamente alcançar num ambiente ideal.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Sub-rede de gateway

Antes de criar um gateway ExpressRoute, deve criar uma sub-rede de gateway. A sub-rede gateway contém os endereços IP que os VMs e serviços de gateway de rede virtual utilizam. Quando cria o portal de rede virtual, os VMs de gateway são implantados na subnet gateway e configurados com as definições de gateway expressRoute necessárias. Nunca coloque mais nada (por exemplo, VMs adicionais) na sub-rede gateway. A sub-rede gateway deve ser denominada 'GatewaySubnet' para funcionar corretamente. Nomear a subnet 'GatewaySubnet' permite ao Azure saber que esta é a subrede para implantar os VMs e serviços de gateway de rede virtual para.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na subnet gateway são atribuídos aos VMs gateway e serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. 

Quando estiver a planear o tamanho da sua subnet gateway, consulte a documentação para a configuração que está a planear criar. Por exemplo, a configuração coexistl ExpressRoute/VPN Gateway requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, pode querer certificar-se de que a sua subnet de gateway contém endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora possa criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede de entrada de /27 ou maior (/27, /26 etc.) se tiver o espaço de endereço disponível para o fazer. Isto acomodará a maioria das configurações.

O exemplo powerShell do Gestor de Recursos que se segue mostra uma subnet de gateway chamada GatewaySubnet. Pode ver que a notação CIDR especifica um /27, que permite endereços IP suficientes para a maioria das configurações que existem atualmente.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>SKUs de gateway redundante de zona

Também pode implementar gateways ExpressRoute em Zonas de Disponibilidade Azure. Isto, física e logicamente, separa-os em diferentes Zonas de Disponibilidade, protegendo a sua conectividade de rede no local para o Azure de falhas ao nível da zona.

![Gateway ExpressRoute redundante em zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Os gateways redundantes em zonas utilizam novas portas específicas para gateway SKUs para gateway ExpressRoute.

* Ergw1Az
* Ergw2Az
* Ergw3Az

O novo gateway SKUs também suporta outras opções de implementação para melhor corresponder às suas necessidades. Ao criar um portal de rede virtual utilizando o novo gateway SKUs, também tem a opção de implementar o gateway numa zona específica. Isto é referido como um portal zonal. Quando você implanta um gateway zonal, todas as instâncias do gateway são implantadas na mesma Zona de Disponibilidade.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

O gateway da rede virtual ExpressRoute foi concebido para trocar rotas de rede e tráfego de rede de rotas. O FastPath foi concebido para melhorar o desempenho do caminho de dados entre a sua rede no local e a sua rede virtual. Quando ativado, o FastPath envia o tráfego de rede diretamente para máquinas virtuais na rede virtual, contornando o portal.

Para mais informações sobre o FastPath, incluindo limitações e requisitos, consulte [sobre fastpath](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>APIs rest e powerShell cmdlets
Para recursos técnicos adicionais e requisitos específicos de sintaxe ao utilizar APIs REST e cmdlets PowerShell para configurações de gateway de rede virtual, consulte as páginas seguintes:

| **Clássica** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações de ligação disponíveis, consulte a visão geral da [ExpressRoute](expressroute-introduction.md).

Para mais informações sobre a criação de gateways ExpressRoute, consulte Criar uma porta de entrada de [rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Para obter mais informações sobre a configuração de gateways redundantes em zonas, consulte [Criar um gateway de rede virtual redundante](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)em zona .

Para mais informações sobre fastPath, consulte [Sobre fastpath](about-fastpath.md).