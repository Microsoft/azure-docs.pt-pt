---
title: Sobre os gateways de rede virtual do ExpressRoute - Azure | Documentos da Microsoft
description: Saiba mais sobre os gateways de rede virtual para o ExpressRoute. Este artigo inclui informações sobre SKUs de gateway e de tipos.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281421"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Sobre gateways de rede virtual ExpressRoute

Para ligar a sua rede virtual Azure e a sua rede no local via ExpressRoute, tem de criar primeiro um portal de rede virtual. Um portal de rede virtual serve dois propósitos: trocar rotas IP entre as redes e o tráfego da rede de rotas. Este artigo explica os tipos de gateway, gateway SKUs e desempenho estimado pela SKU. Este artigo também explica o ExpressRoute [FastPath,](#fastpath)uma funcionalidade que permite ao tráfego de rede a partir da sua rede no local contornar a porta de entrada de rede virtual para melhorar o desempenho.

## <a name="gateway-types"></a>Tipos de gateway

Quando cria um gateway de rede virtual, tem de especificar várias definições. Uma das definições necessárias, '-GatewayType ", especifica se o gateway é utilizado para o ExpressRoute, ou tráfego VPN. Os tipos de dois gateway são:

* **Vpn** - Para enviar tráfego encriptado através da Internet pública, utiliza o tipo de gateway 'Vpn'. Isto é também referido como um gateway VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

* **ExpressRoute** - Para enviar tráfego de rede numa ligação privada, utiliza o tipo de gateway 'ExpressRoute'. Isso também é referido como um gateway do ExpressRoute e é o tipo de gateway utilizado quando configurar o ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute.

## <a name="gwsku"></a>SKUs de Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se quiser atualizar a sua porta de entrada para um Portal De entrada mais potente SKU, na maioria dos casos pode utilizar o cmdlet PowerShell 'Resize-AzVirtualNetworkGateway'. Isso funcionará para atualizações para Standard e o SKU HighPerformance. No entanto, para atualizar para o SKU de UltraPerformance, precisa de recriar o gateway. Recriação de um gateway, incorre em tempo de inatividade.

### <a name="aggthroughput"></a>Desempenhos estimados por Gateway SKU
A tabela seguinte mostra os tipos de gateway e os desempenhos estimados. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Desempenho da aplicação depende de vários fatores, como a latência de ponto-a-ponto e o número de fluxos de tráfego, que o aplicativo é aberto. Os números na tabela representam o limite superior que o aplicativo, teoricamente, pode alcançar num ambiente ideal.
>
>

## <a name="gwsub"></a>Sub-rede gateway

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

### <a name="zrgw"></a>SKUs de gateway redundante de zona

Também pode implementar gateways do ExpressRoute em zonas de disponibilidade do Azure. Isso física e logicamente separa-los em diferentes zonas de disponibilidade, a proteger a conectividade da rede no local para o Azure de falhas de nível de zona.

![Gateway do ExpressRoute com redundância de zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Os gateways com redundância de zona utilizam novos SKUs de gateway específico para o gateway do ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Os novos SKU de gateway também suportam outras opções de implementação para melhor satisfazerem as suas necessidades. Ao criar um gateway de rede virtual com o novo gateway SKUs, tem também a opção de implementar o gateway de uma zona específica. Isto é referido como um gateway zona. Quando implementa um gateway zonal, todas as instâncias do gateway são implementadas na mesma zona de disponibilidade.

## <a name="fastpath"></a>Caminho rápido

O gateway da rede virtual ExpressRoute foi concebido para trocar rotas de rede e tráfego de rede de rotas. O FastPath foi concebido para melhorar o desempenho do caminho de dados entre a sua rede no local e a sua rede virtual. Quando ativado, o FastPath envia o tráfego de rede diretamente para máquinas virtuais na rede virtual, contornando o portal.

Para mais informações sobre o FastPath, incluindo limitações e requisitos, consulte [sobre fastpath](about-fastpath.md).

## <a name="resources"></a>APIs rest e powerShell cmdlets
Para recursos técnicos adicionais e requisitos de sintaxe específica ao utilizar REST APIs e cmdlets do PowerShell para configurações de gateway de rede virtual, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as configurações de ligação disponíveis, consulte a visão geral da [ExpressRoute](expressroute-introduction.md).

Para mais informações sobre a criação de gateways ExpressRoute, consulte Criar uma porta de entrada de [rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Para obter mais informações sobre a configuração de gateways redundantes em zonas, consulte [Criar um gateway de rede virtual redundante](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)em zona .

Para mais informações sobre fastPath, consulte [Sobre fastpath](about-fastpath.md).