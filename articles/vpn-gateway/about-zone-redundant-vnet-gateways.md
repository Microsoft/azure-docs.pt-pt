---
title: Sobre portais de rede virtual redundantes em zonas de disponibilidade de Azure
description: Conheça os gateways VPN Gateway e ExpressRoute em Zonas de Disponibilidade.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: f1bbaab99b6422de4053839e2099869d2d08db95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864303"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Sobre portais de rede virtual redundantes em zonas de disponibilidade de Azure

Pode implementar gateways VPN e ExpressRoute em Zonas de [Disponibilidade Azure](../availability-zones/az-overview.md). Isto traz resiliência, escalabilidade e uma maior disponibilidade para os gateways de redes virtuais. Implementar gateways nas Zonas de Disponibilidade do Azure separa física e logicamente os gateways numa região, enquanto protege a sua conectividade de rede no local para o Azure contra falhas ao nível das zonas.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Gateways com redundância entre zonas

Para implementar automaticamente os seus gateways de rede virtual em zonas de disponibilidade, pode utilizar gateways de rede virtual redundantes em zonas. Com portas redundantes em zona, você pode beneficiar da resiliência da zona para aceder aos seus serviços críticos de missão e escaláveis em Azure.

<br>
<br>

![gráfico de gateways de zona-redundante](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Gateways zonais

Para implantar gateways numa zona específica, pode utilizar gateways zonais. Quando você implanta um gateway zonal, todos os casos do gateway são implantados na mesma Zona de Disponibilidade.

<br>
<br>

![gráfico de gateways zonal](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>SKUs de gateway

Os gateways zona-redundantes e zonais estão disponíveis como novas SKUs de gateway. Adicionámos novas SKUs de rede virtual nas regiões de Azure AZ. Estas SKUs são semelhantes às SKUs existentes correspondentes para a ExpressRoute e VPN Gateway, exceto que são específicas para gateways zona-redundantes e zonais. Pode identificar estas SKUs pelo "AZ" no nome SKU.

Para obter informações sobre gateway SKUs, consulte [VPN gateway SKUs](vpn-gateway-about-vpngateways.md#gwsku) e [ExpressRoute gateway SKUs](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>Ip Público SKUs

Os portões e os portões zonais redundantes dependem tanto do recurso IP público Azure *Standard* SKU. A configuração do recurso IP público Azure determina se o portal que implementa é redundante em zona ou zonal. Se criar um recurso IP público com um SKU *básico,* o portal não terá qualquer redundância de zona, e os recursos de gateway serão regionais.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Gateways com redundância entre zonas

Quando cria um endereço IP público utilizando o IP SKU público **Standard** sem especificar uma zona, o comportamento difere consoante o gateway seja um gateway VPN ou um gateway ExpressRoute. 

* Para um gateway VPN, as duas instâncias de gateway serão implantadas em qualquer uma destas três zonas para fornecer redundância de zona. 
* Para um gateway ExpressRoute, uma vez que pode haver mais de dois casos, o gateway pode estender-se por todas as três zonas.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Gateways zonais

Quando criar um endereço IP público utilizando o IP SKU público **Standard** e especificar a Zona (1, 2 ou 3), todas as instâncias de gateway serão implantadas na mesma zona.

### <a name="regional-gateways"></a><a name="piprg"></a>Gateways regionais

Quando se cria um endereço IP público utilizando o IP SKU público **básico,** o portal é implantado como porta de entrada regional e não tem qualquer redundância de zona incorporada na porta de entrada.

## <a name="faq"></a><a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que mudará quando eu implementar estas novas SKUs?

Do seu ponto de vista, pode implantar os seus portões com redundância de zona. Isto significa que todos os casos dos gateways serão implantados em zonas de disponibilidade do Azure, e cada Zona de Disponibilidade é um domínio de falha e atualização diferente. Isto torna os seus gateways mais fiáveis, disponíveis e resistentes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal Azure?

Sim, pode usar o portal Azure para implementar as novas SKUs. No entanto, você verá estas novas UsS apenas nas regiões azure que têm Zonas de Disponibilidade Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Que regiões estão disponíveis para eu usar as novas SKUs?

As novas SKUs estão disponíveis em regiões de Azure que têm Zonas de Disponibilidade Azure - Centro dos EUA, França Central, Norte da Europa, Europa Ocidental e Regiões Oeste dos EUA 2, Leste dos EUA, Leste dos EUA 2, Sudeste Asiático, Japão Leste, Reino Unido Sul. Daqui para a frente, disponibilizaremos portas de entrada redundantes noutras regiões públicas de Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso mudar/migrar/atualizar os meus portões de rede virtual existentes para gateways de zona redundante ou zonal?

Migrar as suas portas de rede virtual existentes para gateways zonas redundantes ou zonais não é suportado atualmente. Pode, no entanto, eliminar o seu portal existente e recriar um gateway zona-redundante ou zonal.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implantar os gateways VPN e Express Route na mesma rede virtual?

A coexistência de gateways VPN e Express Route na mesma rede virtual é suportada. No entanto, deve reservar uma gama de endereços IP /27 para a sub-rede gateway.

## <a name="next-steps"></a>Passos seguintes

[Criar um gateway de rede virtual com redundância entre zonas](create-zone-redundant-vnet-gateway.md)
