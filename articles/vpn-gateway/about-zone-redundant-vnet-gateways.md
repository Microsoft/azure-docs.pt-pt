---
title: Sobre os gateways de rede virtual com redundância de zona em zonas de disponibilidade do Azure | Documentos da Microsoft
description: Saiba mais sobre os gateways do ExpressRoute e Gateway de VPN em zonas de disponibilidade.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 0ba818ef3c24d0e88e662adf87b22cc938fe5fab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60391077"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Sobre os gateways de rede virtual com redundância de zona em zonas de disponibilidade do Azure

Pode implementar gateways VPN e ExpressRoute no [zonas de disponibilidade do Azure](../availability-zones/az-overview.md). Isso leva a maior disponibilidade, escalabilidade e resiliência a gateways de rede virtual. Implementar gateways em zonas de disponibilidade do Azure, física e logicamente separa gateways dentro de uma região, ao proteger a conectividade da rede no local para o Azure contra falhas de nível de zona.

### <a name="zrgw"></a>Gateways com redundância de zona

Para implementar automaticamente os seus gateways de rede virtual em zonas de disponibilidade, pode utilizar gateways de rede virtual com redundância de zona. Com os gateways com redundância de zona, pode beneficiar de resiliência de zonas para aceder aos seus serviços de missão crítica e dimensionáveis no Azure.

<br>
<br>

![gráfico de gateways com redundância de zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Gateways zonais

Para implementar gateways numa zona específica, pode utilizar gateways zonais. Quando implementa um gateway zonal, todas as instâncias do gateway são implementadas na mesma zona de disponibilidade.

<br>
<br>

![gráfico de zonal gateways](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKUs de Gateway

Com redundância de zona e zonais gateways estão disponíveis como novos SKUs de gateway. Adicionámos SKUs de gateway de rede virtual nova em regiões do Azure AZ. Essas SKUs são semelhantes para os SKUs correspondentes existentes para o ExpressRoute e Gateway de VPN, exceto pelo fato de são específicas para gateways com redundância de zona e zonais.

O novo gateway SKUs são:

### <a name="vpn-gateway"></a>Gateway de VPN

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>SKUs IP público

Gateways com redundância de zona e zonais gateways contam com o recurso IP público do Azure *padrão* SKU. A configuração do recurso de IP público do Azure determina se o gateway que implementar com redundância de zona, ou zonais. Se criar um recurso IP público com um *básica* SKU, o gateway não terá nenhuma redundância de zona e os recursos de gateway serão regionais.

### <a name="pipzrg"></a>Gateways com redundância de zona

Quando cria um através de endereços IP público a **padrão** SKU de IP público sem especificar uma zona, o comportamento difere consoante se o gateway é um gateway de VPN ou um gateway do ExpressRoute. 

* Para um gateway VPN, as instâncias de dois gateway serão implementadas em qualquer 2 fora essas três zonas para fornecer redundância de zona. 
* Para um gateway do ExpressRoute, uma vez que pode haver mais de duas instâncias, o gateway pode ser distribuídas em todas as três zonas.

### <a name="pipzg"></a>Gateways zonais

Quando cria um através de endereços IP público a **padrão** SKU de IP público e especificar a zona (1, 2 ou 3), todas as instâncias de gateway serão implementadas na mesma zona.

### <a name="piprg"></a>Gateways regionais

Quando cria um através de endereços IP público a **básica** SKU de IP público, o gateway é implementado como um gateway regional e não tem nenhuma redundância de zona, criada para o gateway.

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que será alterado quando eu implantar esses novos SKUs?

Do ponto de vista, pode implantar seus gateways com redundância de zona. Isso significa que todas as instâncias dos gateways serão implementadas em zonas de disponibilidade do Azure, e cada zona de disponibilidade é um domínio de falha e de atualização diferente. Isso faz com que os gateways mais confiável, disponível e resiliente a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Pode utilizar o portal do Azure?

Sim, pode utilizar o portal do Azure para implementar os novos SKU. No entanto, verá esses novos SKUs apenas nas regiões do Azure têm zonas de disponibilidade do Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Que regiões estão disponíveis para mim utilizar os novos SKU?

Os novos SKU estão disponíveis nas regiões do Azure que têm zonas de disponibilidade do Azure – centro dos E.U.A., Centro de França, Europa do Norte, Europa Ocidental e regiões do Oeste dos E.U.A. 2. Daqui em diante, faremos gateways com redundância de zona disponíveis para em outras regiões públicas do Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualização meus gateways de rede virtual existente para gateways com redundância de zona ou zonais?

Migrar os seus gateways de rede virtual existente para gateways com redundância de zona ou zonais não é atualmente suportada. No entanto, pode eliminar o gateway existente e voltar a criar um gateway com redundância de zona ou zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Pode implementar gateways VPN e o Express Route na mesma rede virtual?

Coexistência de gateways VPN e o Express Route na mesma rede virtual é suportada. No entanto, deve reservar/27 intervalo de endereços IP para a sub-rede do gateway.

## <a name="next-steps"></a>Próximos Passos

[Criar um gateway de rede virtual com redundância entre zonas](create-zone-redundant-vnet-gateway.md)
