---
title: Sobre os gateways de rede virtual do ExpressRoute - Azure | Documentos da Microsoft
description: Saiba mais sobre os gateways de rede virtual para o ExpressRoute. Este artigo inclui informações sobre SKUs de gateway e de tipos.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: d9c607114d6c6c56c25303a88dcc11f4ab804eb4
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404353"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Acerca dos gateways de rede virtual para o ExpressRoute
Um gateway de rede virtual é utilizado para enviar tráfego de rede entre redes virtuais do Azure e localizações no local. Pode utilizar um gateway de rede virtual para o tráfego de ExpressRoute ou o tráfego VPN. Este artigo se concentra em gateways de rede virtual do ExpressRoute e contém informações sobre SKUs, desempenho estimado por SKU e tipos de gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>Tipos de gateway

Quando cria um gateway de rede virtual, tem de especificar várias definições. Uma das definições necessárias, '-GatewayType ", especifica se o gateway é utilizado para o ExpressRoute, ou tráfego VPN. Os tipos de dois gateway são:

* **VPN** - para enviar tráfego encriptado através da Internet pública, utiliza o tipo de gateway "Vpn". Isto é também referido como um gateway VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

* **ExpressRoute** - para enviar tráfego de rede numa ligação privada, utiliza o tipo de gateway "ExpressRoute". Isso também é referido como um gateway do ExpressRoute e é o tipo de gateway utilizado quando configurar o ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute.

## <a name="gwsku"></a>SKUs de Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se pretender atualizar o gateway de um SKU de gateway mais poderosa, na maioria dos casos pode utilizar o cmdlet do PowerShell 'Redimensionamento AzVirtualNetworkGateway'. Isso funcionará para atualizações para Standard e o SKU HighPerformance. No entanto, para atualizar para o SKU de UltraPerformance, precisa de recriar o gateway. Recriação de um gateway, incorre em tempo de inatividade.

### <a name="aggthroughput"></a>Desempenhos estimados pelo SKU do gateway
A tabela seguinte mostra os tipos de gateway e os desempenhos estimados. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Desempenho da aplicação depende de vários fatores, como a latência de ponto-a-ponto e o número de fluxos de tráfego, que o aplicativo é aberto. Os números na tabela representam o limite superior que o aplicativo, teoricamente, pode alcançar num ambiente ideal.
>
>

### <a name="zrgw"></a>SKUs de gateway com redundância de zona

Também pode implementar gateways do ExpressRoute em zonas de disponibilidade do Azure. Isso física e logicamente separa-los em diferentes zonas de disponibilidade, a proteger a conectividade da rede no local para o Azure de falhas de nível de zona.

![Gateway do ExpressRoute com redundância de zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Os gateways com redundância de zona utilizam novos SKUs de gateway específico para o gateway do ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Os novos SKU de gateway também suportam outras opções de implementação para melhor satisfazerem as suas necessidades. Ao criar um gateway de rede virtual com o novo gateway SKUs, tem também a opção de implementar o gateway de uma zona específica. Isto é referido como um gateway zona. Quando implementa um gateway zonal, todas as instâncias do gateway são implementadas na mesma zona de disponibilidade.

## <a name="resources"></a>Cmdlets do PowerShell e REST APIs
Para recursos técnicos adicionais e requisitos de sintaxe específica ao utilizar REST APIs e cmdlets do PowerShell para configurações de gateway de rede virtual, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passos Seguintes
Ver [descrição geral do ExpressRoute](expressroute-introduction.md) para obter mais informações sobre configurações de ligação disponíveis.

Ver [criar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) para obter mais informações sobre a criação de gateways do ExpressRoute.

Ver [criar um gateway de rede virtual com redundância de zona](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) para obter mais informações sobre como configurar gateways com redundância de zona.
