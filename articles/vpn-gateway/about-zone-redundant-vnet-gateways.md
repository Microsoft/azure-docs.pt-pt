---
title: Sobre as portas de rede virtuais redundantes em zonas de disponibilidade do Azure
description: Implementar gateways VPN e ExpressRoute em Zonas de Disponibilidade Azure, para trazer resiliência, escalabilidade e maior disponibilidade para gateways de rede virtuais.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2030469262baf406635fd170af384e154fec6ae6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401117"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Sobre as portas de rede virtuais redundantes em zonas de disponibilidade do Azure

Pode implantar gateways VPN e ExpressRoute em [Zonas de Disponibilidade Azure](../availability-zones/az-overview.md). Isto traz resiliência, escalabilidade e uma maior disponibilidade para os gateways de redes virtuais. Implementar gateways nas Zonas de Disponibilidade do Azure separa física e logicamente os gateways numa região, enquanto protege a sua conectividade de rede no local para o Azure contra falhas ao nível das zonas.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Gateways com redundância entre zonas

Para implementar automaticamente os seus gateways de rede virtuais através de zonas de disponibilidade, pode utilizar gateways de rede virtuais redundantes em zonas. Com portais redundantes de zona, você pode beneficiar da resiliência da zona para aceder aos seus serviços críticos e escaláveis da missão em Azure.

<br>
<br>

![gráfico de gateways redundantes de zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Gateways zonais

Para implantar gateways numa zona específica, pode utilizar gateways zonais. Quando implementa um gateway zonal, todas as instâncias do gateway são implantadas na mesma Zona de Disponibilidade.

<br>
<br>

![gráfico de gateways zonal](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>SKUs de Gateway

Os portões de entrada de zonas redundantes e zonais estão disponíveis como novos SKUs de gateway. Adicionámos novas redes virtuais de gateway SKUs nas regiões Azure AZ. Estes SKUs são semelhantes aos SKUs existentes correspondentes para ExpressRoute e VPN Gateway, exceto que são específicos para portas de portais zona-redundantes e zonais. Pode identificar estes SKUs pelo "AZ" no nome SKU.

Para obter informações sobre o gateway SKUs, consulte [a porta de entrada VPN SKUs](vpn-gateway-about-vpngateways.md#gwsku) e [expressRoute gateway SKUs](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>SKUs IP públicos

Os gateways redundantes de zona e os gateways zonais dependem tanto do recurso IP público Azure *Standard* SKU. A configuração do recurso IP público Azure determina se o gateway que implementa é redundante ou zonal. Se criar um recurso IP público com um SKU *Básico,* o gateway não terá qualquer redundância de zona, e os recursos de gateway serão regionais.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Gateways com redundância entre zonas

Quando cria um endereço IP público utilizando o IP SKU público **standard** sem especificar uma zona, o comportamento difere dependendo se o gateway é um gateway VPN ou um gateway ExpressRoute. 

* Para uma porta de entrada VPN, as duas instâncias de gateway serão implantadas em qualquer 2 destas três zonas para fornecer redundância de zona. 
* Para um gateway ExpressRoute, uma vez que pode haver mais de duas instâncias, o gateway pode abranger todas as três zonas.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Gateways zonais

Quando criar um endereço IP público utilizando o IP SKU público **standard** e especificar a Zona (1, 2 ou 3), todas as instâncias de gateway serão implantadas na mesma zona.

### <a name="regional-gateways"></a><a name="piprg"></a>Gateways regionais

Quando cria um endereço IP público utilizando o IP SKU do público **básico,** o gateway é implantado como uma porta de entrada regional e não tem qualquer zona-redundância incorporada no gateway.

## <a name="faq"></a><a name="faq"></a>Perguntas Frequentes

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que mudará quando eu implementar estes novos SKUs?

Do seu ponto de vista, pode implantar os seus gateways com redundância de zona. Isto significa que todas as instâncias dos gateways serão implantadas em todas as Zonas de Disponibilidade de Azure, e cada Zona de Disponibilidade é um domínio de falha e atualização diferente. Isto torna os seus gateways mais fiáveis, disponíveis e resistentes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal Azure?

Sim, pode usar o portal Azure para implantar os novos SKUs. No entanto, você verá estes novos SKUs apenas nas regiões Azure que têm Azure Availability Zones.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Que regiões estão disponíveis para eu utilizar os novos SKUs?

Os novos SKUs estão disponíveis nas regiões de Azure que têm Zonas de Disponibilidade Azure - Central EUA, França Central, Norte da Europa, Europa Ocidental e Oeste dos EUA 2 regiões, Leste dos EUA, Leste dos EUA 2, Sudeste Asiático, Japão Leste, Reino Unido Sul. Daqui para a frente, disponibilizaremos portas de zonas redundantes noutras Regiões Públicas de Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualizar os meus gateways de rede virtuais existentes para portas de entrada redundantes ou zonais?

A migração dos seus gateways de rede virtuais existentes para portas de portais redundantes ou zonais não é suportada atualmente. Pode, no entanto, apagar o seu gateway existente e recriar uma porta de entrada redundante ou zonal.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implementar os gateways VPN e Express Route na mesma rede virtual?

A coexistência de gateways VPN e Express Route na mesma rede virtual é suportada. No entanto, deverá reservar um intervalo de endereço IP /27 para a sub-rede gateway.

## <a name="next-steps"></a>Passos seguintes

[Criar um gateway de rede virtual com redundância entre zonas](create-zone-redundant-vnet-gateway.md)
