---
title: 'Cenário: qualquer-para-qualquer'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - Qualquer-para-qualquer
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569028"
---
# <a name="scenario-any-to-any"></a>Cenário: Qualquer-para-qualquer

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Em qualquer cenário, qualquer fala pode chegar a outro falado. Quando existem vários hubs, o encaminhamento hub-to-hub (também conhecido como inter-hub) é ativado por padrão em NORMAL VIRTUAL WAN. 

Neste cenário, as ligações VPN, ExpressRoute e User VPN estão associadas à mesma tabela de rotas. Todas as ligações VPN, ExpressRoute e User VPN propagam rotas para o mesmo conjunto de tabelas de rotas. Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura do cenário

Na **Figura 1**, todos os VNets e Ramos (VPN, ExpressRoute, P2S) podem chegar uns aos outros. Num centro virtual, as ligações funcionam da seguinte forma:

* Uma ligação VPN liga um site VPN a um gateway VPN.
* Uma ligação de rede virtual liga uma rede virtual a um hub virtual. O router do hub virtual fornece a funcionalidade de trânsito entre VNets.
* Uma ligação ExpressRoute liga um circuito ExpressRoute a um gateway ExpressRoute.

Estas ligações (por padrão na criação) estão associadas à tabela de rotas Predefinida, a menos que tenha configurado a configuração de encaminhamento da ligação a **Nenhum**, ou a uma tabela de rotas personalizada. Estas ligações também propagam rotas, por defeito, à tabela de rotas Predefinidos. Isto é o que permite um qualquer cenário em que qualquer porta -falado (VNet, VPN, ER, P2S) pode chegar uns aos outros.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

Este cenário é ativado por padrão para STANDARD Virtual WAN. Se a definição de ramo-a-ramo for desativada na configuração WAN, isso irá desativar a conectividade entre os raios-ramos. VPN/ExpressRoute/User VPN são considerados porta-vozes de sucursais em VIRTUAL WAN

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
