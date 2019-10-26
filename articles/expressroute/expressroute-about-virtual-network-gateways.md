---
title: Sobre gateways de rede virtual do ExpressRoute – Azure | Microsoft Docs
description: Saiba mais sobre gateways de rede virtual para ExpressRoute. Este artigo inclui informações sobre os tipos e SKUs de gateway.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 8860a297332a3572890ceb4c843040f530b8a897
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935535"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Gateway de rede virtual do ExpressRoute e FastPath
Para conectar sua rede virtual do Azure e sua rede local por meio do ExpressRoute, você deve primeiro criar um gateway de rede virtual. Um gateway de rede virtual tem duas finalidades: rotas de IP do Exchange entre as redes e rotear o tráfego de rede. Este artigo explica os tipos de gateway, as SKUs de gateway e o desempenho estimado por SKU. Este artigo também explica o ExpressRoute [FastPath](#fastpath), um recurso que permite que o tráfego de rede da sua rede local ignore o gateway de rede virtual para melhorar o desempenho.

## <a name="gateway-types"></a>Tipos de gateway

Ao criar um gateway de rede virtual, você precisa especificar várias configurações. Uma das configurações necessárias, '-Gatewaytype ', especifica se o gateway é usado para o ExpressRoute ou para o tráfego de VPN. Os dois tipos de gateway são:

* **VPN** – para enviar tráfego criptografado pela Internet pública, use o tipo de gateway ' VPN '. Isso também é conhecido como um gateway de VPN. As ligações Sites para Site, Ponto para site e VNet para VNet utilizam todas um gateway de VPN.

* **ExpressRoute** – para enviar tráfego de rede em uma conexão privada, use o tipo de gateway ' ExpressRoute '. Isso também é conhecido como um gateway de ExpressRoute e é o tipo de gateway usado ao configurar o ExpressRoute.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, pode ter um gateway de rede virtual que utilize -GatewayType Vpn e outro que utilize GatewayType ExpressRoute.

## <a name="gwsku"></a>SKUs de Gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se você quiser atualizar seu gateway para um SKU de gateway mais potente, na maioria dos casos, você pode usar o cmdlet ' Resize-AzVirtualNetworkGateway ' do PowerShell. Isso funcionará para atualizações para SKUs Standard e HighPerformance. No entanto, para atualizar para o SKU do UltraPerformance, será necessário recriar o gateway. Recriar um gateway incorre em tempo de inatividade.

### <a name="aggthroughput"></a>Desempenho estimado por SKU de gateway
A tabela a seguir mostra os tipos de gateway e o desempenho estimado. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> O desempenho do aplicativo depende de vários fatores, como a latência de ponta a ponta e o número de fluxos de tráfego que o aplicativo abre. Os números na tabela representam o limite superior que o aplicativo pode obter teoricamente em um ambiente ideal.
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

Você também pode implantar gateways de ExpressRoute no Zonas de Disponibilidade do Azure. Isso os separa fisicamente e logicamente em diferentes Zonas de Disponibilidade, protegendo sua conectividade de rede local ao Azure de falhas no nível de zona.

![Gateway de ExpressRoute com redundância de zona](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Os gateways com redundância de zona usam novos SKUs de gateway específicos para o gateway de ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

As novas SKUs de gateway também dão suporte a outras opções de implantação para atender melhor às suas necessidades. Ao criar um gateway de rede virtual usando as novas SKUs de gateway, você também tem a opção de implantar o gateway em uma zona específica. Isso é chamado de gateway zonal. Quando você implanta um gateway zonal, todas as instâncias do gateway são implantadas na mesma zona de disponibilidade.

## <a name="fastpath"></a>FastPath
O gateway de rede virtual ExpressRoute foi projetado para trocar rotas de rede e rotear o tráfego de rede. O FastPath foi projetado para melhorar o desempenho do caminho de dados entre sua rede local e sua rede virtual. Quando habilitado, o FastPath envia o tráfego de rede diretamente às máquinas virtuais na rede virtual, ignorando o gateway. 

O FastPath está disponível em todos os circuitos do ExpressRoute. Ele ainda requer que um gateway de rede virtual seja criado para trocar rotas entre a rede virtual e a rede local. O gateway de rede virtual deve ser ultra performance ou ErGw3AZ.

O FastPath não dá suporte aos seguintes recursos:
* UDR na sub-rede de gateway: se você aplicar um UDR à sub-rede de gateway de sua rede virtual, o tráfego de rede da rede local continuará a ser enviado para o gateway de rede virtual.
* Emparelhamento VNet: se você tiver outras redes virtuais emparelhadas com aquela que está conectada ao ExpressRoute, o tráfego de rede da sua rede local para as outras redes virtuais (ou seja, a chamada de "spoke" VNets) continuará a ser enviado para a rede virtual Gateway. A solução alternativa é conectar todas as redes virtuais ao circuito do ExpressRoute diretamente.
* Load Balancer básica: se você implantar um balanceador de carga interno básico em sua rede virtual ou o serviço de PaaS do Azure que você implantar em sua rede virtual usar um balanceador de carga interno básico, o tráfego de rede da sua rede local para os IPs virtuais hospedados no O Load Balancer básico será enviado para o gateway de rede virtual. A solução é atualizar o balanceador de carga básico para um [balanceador de carga padrão](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview).
* Link privado: se você se conectar a um [ponto de extremidade privado](../private-link/private-link-overview.md) em sua rede virtual a partir de sua rede local, a conexão passará pelo gateway de rede virtual.
 
## <a name="resources"></a>APIs REST e cmdlets do PowerShell
Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar APIs REST e cmdlets do PowerShell para configurações de gateway de rede virtual, consulte as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passos seguintes
Consulte [visão geral do ExpressRoute](expressroute-introduction.md) para obter mais informações sobre as configurações de conexão disponíveis.

Consulte [criar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) para obter mais informações sobre como criar gateways de ExpressRoute.

Consulte [criar um gateway de rede virtual com redundância de zona](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) para obter mais informações sobre como configurar gateways com redundância de zona.

Consulte [vincular rede virtual ao ExpressRoute](expressroute-howto-linkvnet-arm.md) para obter mais informações sobre como habilitar o FastPath. 
