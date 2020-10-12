---
title: 'Cenário: qualquer-para-qualquer'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - Qualquer-para-qualquer
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6115ca375c3e5bf2be3335fe2231628ec7bf309f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267742"
---
# <a name="scenario-any-to-any"></a>Cenário: Qualquer-para-qualquer

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Em qualquer cenário, qualquer fala pode chegar a outro falado. Quando existem vários hubs, o encaminhamento hub-to-hub (também conhecido como inter-hub) é ativado por padrão em NORMAL VIRTUAL WAN. Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Para descobrir quantas tabelas de rota serão necessárias num cenário de WAN Virtual, pode construir uma matriz de conectividade, onde cada célula representa se uma fonte (linha) pode comunicar a um destino (coluna). A matriz de conectividade neste cenário é trivial, mas incluímo-la para ser consistente com outros cenários.

| De |   Para |  *VNets* | *Ramos* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|      X     |     X    |
| Ramos   | &#8594;|    X     |     X    |

Cada uma das células da tabela anterior descreve se uma ligação WAN virtual (o lado "De" do fluxo, os cabeçalhos de linha na tabela) aprende um prefixo de destino (o lado "To" do fluxo, os cabeçalhos da coluna em itálico na tabela) para um fluxo de tráfego específico, onde um "X" significa que a conectividade é fornecida por Virtual WAN.

Uma vez que todas as ligações de VNets e sucursais (VPN, ExpressRoute e User VPN) têm os mesmos requisitos de conectividade, é necessária uma única tabela de rotas. Como resultado, todas as ligações serão associadas e propagadas para a mesma tabela de rotas, a tabela de rotas predefinida:

* Redes virtuais:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **Predefinido**
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **Predefinido**

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Na **Figura 1**, todos os VNets e Ramos (VPN, ExpressRoute, P2S) podem chegar uns aos outros. Num centro virtual, as ligações funcionam da seguinte forma:

* Uma ligação VPN liga um site VPN a um gateway VPN.
* Uma ligação de rede virtual liga uma rede virtual a um hub virtual. O router do hub virtual fornece a funcionalidade de trânsito entre VNets.
* Uma ligação ExpressRoute liga um circuito ExpressRoute a um gateway ExpressRoute.

Estas ligações (por padrão na criação) estão associadas à tabela de rotas Predefinida, a menos que tenha configurado a configuração de encaminhamento da ligação a **Nenhum**, ou a uma tabela de rotas personalizada. Estas ligações também propagam rotas, por defeito, à tabela de rotas Predefinidos. Isto é o que permite um qualquer cenário em que qualquer porta -falado (VNet, VPN, ER, P2S) pode chegar uns aos outros.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="figura 1":::

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Este cenário é ativado por padrão para STANDARD Virtual WAN. Se a definição de ramo-a-ramo for desativada na configuração WAN, isso irá desativar a conectividade entre os raios-ramos. VPN/ExpressRoute/User VPN são considerados porta-vozes de sucursais em VIRTUAL WAN

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
