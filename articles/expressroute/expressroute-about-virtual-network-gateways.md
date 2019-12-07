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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894386"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Sobre gateways de rede virtual do ExpressRoute

Para conectar sua rede virtual do Azure e sua rede local por meio do ExpressRoute, você deve primeiro criar um gateway de rede virtual. Um gateway de rede virtual tem duas finalidades: rotas de IP do Exchange entre as redes e rotear o tráfego de rede. Este artigo explica os tipos de gateway, as SKUs de gateway e o desempenho estimado por SKU. Este artigo também explica o ExpressRoute [FastPath](#fastpath), um recurso que permite que o tráfego de rede da sua rede local ignore o gateway de rede virtual para melhorar o desempenho.

## <a name="gateway-types"></a>Tipos de gateway

Quando cria um gateway de rede virtual, tem de especificar várias definições. Uma das definições necessárias, '-GatewayType ", especifica se o gateway é utilizado para o ExpressRoute, ou tráfego VPN. Os tipos de dois gateway são:

* **VPN** - para enviar tráfego encriptado através da Internet pública, utiliza o tipo de gateway "Vpn". Isto é também referido como um gateway VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

* **ExpressRoute** - para enviar tráfego de rede numa ligação privada, utiliza o tipo de gateway "ExpressRoute". Isso também é referido como um gateway do ExpressRoute e é o tipo de gateway utilizado quando configurar o ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute.

## <a name="gwsku"></a>SKUs de Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se você quiser atualizar seu gateway para um SKU de gateway mais potente, na maioria dos casos, você pode usar o cmdlet ' Resize-AzVirtualNetworkGateway ' do PowerShell. Isso funcionará para atualizações para Standard e o SKU HighPerformance. No entanto, para atualizar para o SKU de UltraPerformance, precisa de recriar o gateway. Recriação de um gateway, incorre em tempo de inatividade.

### <a name="aggthroughput"></a>Desempenhos estimados pelo SKU do gateway
A tabela seguinte mostra os tipos de gateway e os desempenhos estimados. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Desempenho da aplicação depende de vários fatores, como a latência de ponto-a-ponto e o número de fluxos de tráfego, que o aplicativo é aberto. Os números na tabela representam o limite superior que o aplicativo, teoricamente, pode alcançar num ambiente ideal.
>
>

## <a name="gwsub"></a>Sub-rede de gateway

Antes de criar um gateway de ExpressRoute, você deve criar uma sub-rede de gateway. A sub-rede de gateway contém os endereços IP que as VMs e os serviços do gateway de rede virtual usam. Quando você cria seu gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e configuradas com as configurações de gateway de ExpressRoute necessárias. Nunca implante mais nada (por exemplo, VMs adicionais) para a sub-rede de gateway. A sub-rede de gateway deve ser nomeada como ' GatewaySubnet ' para funcionar corretamente. Nomear a sub-rede de gateway ' GatewaySubnet ' permite que o Azure saiba que essa é a sub-rede para a qual implantar as VMs e serviços de gateway de rede virtual.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando cria a sub-rede do gateway, especifica o número de endereços IP que a sub-rede contém. Os endereços IP na sub-rede de gateway são alocados para as VMs de gateway e serviços de gateway. Algumas configurações requerem mais endereços IP do que outras. 

Ao planejar o tamanho da sub-rede do gateway, consulte a documentação da configuração que você planeja criar. Por exemplo, a configuração de gateway ExpressRoute/VPN coexistente requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, talvez você queira garantir que sua sub-rede de gateway contenha endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora seja possível criar uma sub-rede de gateway tão pequena quanto/29, recomendamos que você crie uma sub-rede de gateway de/27 ou maior (/27,/26, etc.) se tiver o espaço de endereço disponível para fazer isso. Isso irá acomodar a maioria das configurações.

O exemplo do PowerShell do Gerenciador de recursos a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver a notação CIDR especifica a/27, que permite endereços IP suficientes para a maioria das configurações que existem atualmente.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>SKUs de gateway com redundância de zona

Também pode implementar gateways do ExpressRoute em zonas de disponibilidade do Azure. Isso física e logicamente separa-los em diferentes zonas de disponibilidade, a proteger a conectividade da rede no local para o Azure de falhas de nível de zona.

![Gateway do ExpressRoute com redundância de zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Os gateways com redundância de zona utilizam novos SKUs de gateway específico para o gateway do ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Os novos SKU de gateway também suportam outras opções de implementação para melhor satisfazerem as suas necessidades. Ao criar um gateway de rede virtual com o novo gateway SKUs, tem também a opção de implementar o gateway de uma zona específica. Isto é referido como um gateway zona. Quando implementa um gateway zonal, todas as instâncias do gateway são implementadas na mesma zona de disponibilidade.

## <a name="fastpath"></a>FastPath

O gateway de rede virtual ExpressRoute foi projetado para trocar rotas de rede e rotear o tráfego de rede. O FastPath foi projetado para melhorar o desempenho do caminho de dados entre sua rede local e sua rede virtual. Quando habilitado, o FastPath envia o tráfego de rede diretamente às máquinas virtuais na rede virtual, ignorando o gateway.

Para obter mais informações sobre FastPath, incluindo limitações e requisitos, consulte [about FastPath](about-fastpath.md).

## <a name="resources"></a>Cmdlets do PowerShell e REST APIs
Para recursos técnicos adicionais e requisitos de sintaxe específica ao utilizar REST APIs e cmdlets do PowerShell para configurações de gateway de rede virtual, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações de conexão disponíveis, consulte [visão geral do ExpressRoute](expressroute-introduction.md).

Para obter mais informações sobre como criar gateways de ExpressRoute, consulte [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Para obter mais informações sobre como configurar gateways com redundância de zona, consulte [criar um gateway de rede virtual com redundância de zona](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Para obter mais informações sobre o FastPath, consulte [about FastPath](about-fastpath.md).