---
title: Sobre gateways de rede virtual com redundância de zona no Zonas de Disponibilidade do Azure
description: Saiba mais sobre gateway de VPN e gateways de ExpressRoute no Zonas de Disponibilidade.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: f1bbaab99b6422de4053839e2099869d2d08db95
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864303"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Sobre gateways de rede virtual com redundância de zona no Zonas de Disponibilidade do Azure

Você pode implantar gateways VPN e ExpressRoute no [zonas de disponibilidade do Azure](../availability-zones/az-overview.md). Isto traz resiliência, escalabilidade e uma maior disponibilidade para os gateways de redes virtuais. Implementar gateways nas Zonas de Disponibilidade do Azure separa física e logicamente os gateways numa região, enquanto protege a sua conectividade de rede no local para o Azure contra falhas ao nível das zonas.

### <a name="zrgw"></a>Gateways com redundância de zona

Para implantar automaticamente seus gateways de rede virtual entre zonas de disponibilidade, você pode usar gateways de rede virtual com redundância de zona. Com gateways com redundância de zona, você pode se beneficiar da resiliência de zona para acessar seus serviços críticos e escalonáveis no Azure.

<br>
<br>

![gráfico de gateways com redundância de zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Gateways zonais

Para implantar gateways em uma zona específica, você pode usar gateways zonais. Quando você implanta um gateway zonal, todas as instâncias do gateway são implantadas na mesma zona de disponibilidade.

<br>
<br>

![gráfico de gateways zonais](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKUs de Gateway

Os gateways com e com redundância de zona estão disponíveis como novas SKUs de gateway. Adicionamos novas SKUs de gateway de rede virtual nas regiões AZ do Azure. Essas SKUs são semelhantes às SKUs existentes correspondentes para o ExpressRoute e o gateway de VPN, exceto pelo fato de que elas são específicas para gateways com redundância de zona e zonas. Você pode identificar essas SKUs pelo "AZ" no nome do SKU.

Para obter informações sobre SKUs de gateway, consulte [SKUs de gateway de VPN](vpn-gateway-about-vpngateways.md#gwsku) e SKUs de gateway de [ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="pipskus"></a>SKUs de IP público

Gateways com redundância de zona e gateways zonais dependem do SKU *padrão* do recurso de IP público do Azure. A configuração do recurso de IP público do Azure determina se o gateway que você implanta tem redundância de zona ou zonas. Se você criar um recurso de IP público com um SKU *básico* , o gateway não terá nenhuma redundância de zona e os recursos do gateway serão regionais.

### <a name="pipzrg"></a>Gateways com redundância de zona

Quando você cria um endereço IP público usando o SKU IP público **padrão** sem especificar uma zona, o comportamento difere dependendo se o gateway é um gateway de VPN ou um gateway de ExpressRoute. 

* Para um gateway de VPN, as duas instâncias de gateway serão implantadas em qualquer 2 dessas três zonas para fornecer redundância de zona. 
* Para um gateway de ExpressRoute, como pode haver mais de duas instâncias, o gateway pode se estender por todas as três zonas.

### <a name="pipzg"></a>Gateways zonais

Quando você cria um endereço IP público usando o SKU IP público **padrão** e especifica a zona (1, 2 ou 3), todas as instâncias de gateway serão implantadas na mesma zona.

### <a name="piprg"></a>Gateways regionais

Quando você cria um endereço IP público usando o SKU IP público **básico** , o gateway é implantado como um gateway regional e não tem nenhuma redundância de zona embutida no gateway.

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que será alterado quando eu implantar essas novas SKUs?

Da sua perspectiva, você pode implantar seus gateways com redundância de zona. Isso significa que todas as instâncias dos gateways serão implantadas em Zonas de Disponibilidade do Azure, e cada zona de disponibilidade será um domínio de atualização e de falha diferente. Isso torna seus gateways mais confiáveis, disponíveis e resilientes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal do Azure?

Sim, você pode usar o portal do Azure para implantar as novas SKUs. No entanto, você verá esses novos SKUs somente nas regiões do Azure que têm Zonas de Disponibilidade do Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Quais regiões estão disponíveis para usar as novas SKUs?

As novas SKUs estão disponíveis nas regiões do Azure que têm as regiões Zonas de Disponibilidade do Azure-EUA Central, França central, Europa Setentrional, Europa Ocidental e oeste dos EUA 2, leste dos EUA, leste dos Estados Unidos 2, Sudeste Asiático, leste do Japão, Sul do Reino Unido. No futuro, disponibilizaremos gateways com redundância de zona para você em outras regiões públicas do Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualizar meus gateways de rede virtual existentes para gateways com redundância de zona ou zonas.

No momento, não há suporte para migrar seus gateways de rede virtual existentes para gateways com redundância de zona ou zonas. Você pode, no entanto, excluir o gateway existente e recriar um gateway com redundância de zona ou zonas.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implantar os gateways de VPN e de rota expressa na mesma rede virtual?

Há suporte para a coexistência de gateways de VPN e de rota expressa na mesma rede virtual. No entanto, você deve reservar um intervalo de endereços IP/27 para a sub-rede de gateway.

## <a name="next-steps"></a>Passos seguintes

[Criar um gateway de rede virtual com redundância entre zonas](create-zone-redundant-vnet-gateway.md)
