---
title: Sobre os gateways de rede virtual do ExpressRoute - Azure | Documentos da Microsoft
description: Saiba mais sobre os gateways de rede virtual para o ExpressRoute. Este artigo inclui informações sobre SKUs de gateway e de tipos.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 18615cf737eedcd188fd59d2aa98482210b9333a
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991596"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Gateway de rede virtual do ExpressRoute e FastPath
Para ligar a rede virtual do Azure e a sua rede no local através do ExpressRoute, tem de criar primeiro um gateway de rede virtual. Um gateway de rede virtual tem duas finalidades: rotas IP do exchange entre as redes e encaminhar o tráfego de rede. Este artigo explica os tipos de gateway, o SKU de gateway e o desempenho estimado pelo SKU. Este artigo também explica ExpressRoute [FastPath](#fastpath), uma funcionalidade que permite o tráfego de rede da sua rede no local para ignorar o gateway de rede virtual para melhorar o desempenho.

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

## <a name="fastpath"></a>FastPath
Gateway de rede virtual do ExpressRoute foi concebido para trocar as rotas de rede e encaminha o tráfego de rede. FastPath foi concebido para melhorar o desempenho do caminho de dados entre a sua rede no local e a rede virtual. Quando ativada, o FastPath envia o tráfego de rede diretamente a máquinas virtuais na rede virtual, ignorando o gateway. 

FastPath está disponível no [ExpressRoute Direct](expressroute-erdirect-about.md) apenas. Em outras palavras, pode ativar esta funcionalidade apenas se for [ligar a sua rede virtual](expressroute-howto-linkvnet-arm.md) para um circuito ExpressRoute criado numa porta direta do ExpressRoute. FastPath ainda requer um gateway de rede virtual a ser criada para trocar rotas entre a rede virtual e de rede no local. O gateway de rede virtual tem de ser desempenho Ultra ou ErGw3AZ.

FastPath não suporta as seguintes funcionalidades:
* A UDR na sub-rede de Gateway: se aplicar um UDR para a sub-rede do Gateway da sua rede virtual, o tráfego de rede da sua rede no local irá continuar a ser enviados para o gateway de rede virtual.
* Peering de VNet: Se tiver outras redes virtuais em modo de peering com aquele que está ligada ao ExpressRoute, o tráfego de rede da sua rede no local para as outras redes virtuais (ou seja, as chamados "Spoke" VNets) irá continuar a ser enviados para a rede virtual gateway. A solução alternativa é ligar todas as redes virtuais ao circuito ExpressRoute diretamente.

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

Ver [rede virtual de ligação ao ExpressRoute](expressroute-howto-linkvnet-arm.md) para obter mais informações sobre como ativar FastPath. 
