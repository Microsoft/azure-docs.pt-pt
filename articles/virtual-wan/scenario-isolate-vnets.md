---
title: 'Cenário: Isolar as Redes'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - VNets isolados
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f725932b30fad062123d6c752f2d563b84f98b2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267640"
---
# <a name="scenario-isolating-vnets"></a>Cenário: Isolar as Redes

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o objetivo é evitar que os VNets sejam capazes de chegar a outros. Isto é conhecido como isolante VNets. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Neste cenário, a carga de trabalho dentro de um determinado VNet permanece isolada e não é capaz de comunicar com outros VNets. No entanto, os VNets são necessários para chegar a todos os ramos (VPN, ER e User VPN). Para descobrir quantas tabelas de rotas serão necessárias, você pode construir uma matriz de conectividade. Para este cenário será a seguinte tabela, onde cada célula representa se uma fonte (linha) pode comunicar a um destino (coluna):

| De |   Para |  *VNets* | *Ramos* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|           |     X    |
| Ramos   | &#8594;|    X     |     X    |

Cada uma das células da tabela anterior descreve se uma ligação WAN virtual (o lado "De" do fluxo, os cabeçalhos de linha) aprende um prefixo de destino (o lado "To" do fluxo, os cabeçalhos da coluna em itálico) para um fluxo de tráfego específico, onde um "X" significa que a conectividade é fornecida por VIRTUAL WAN.

Esta matriz de conectividade dá-nos dois padrões de linha diferentes, que se traduzem em duas tabelas de rota. A WAN virtual já tem uma tabela de rotas padrão, por isso vamos precisar de outra tabela de rotas. Para este exemplo, vamos nomear a tabela de rotas **RT_VNET**.

Os VNets serão associados a esta **tabela de rota RT_VNET.** Como precisam de conectividade com os ramos, os ramos terão de se propagar para **RT_VNET** (caso contrário, os VNets não aprenderiam os prefixos do ramo). Uma vez que os ramos estão sempre associados à tabela de rotas predefinida, os VNets terão de se propagar à tabela de rotas Predefinida. Como resultado, este é o design final:

* Redes virtuais:
  * Tabela de rotas associada: **RT_VNET**
  * Propagação para tabelas de rotas: **Predefinido**
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **RT_VNET** e **Padrão**

Note que, uma vez que apenas os ramos se propagam à tabela de rotas **RT_VNET,** esses serão os únicos prefixos que os VNets irão aprender, e não os de outros VNets.

Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

A fim de configurar este cenário, tome em consideração os seguintes passos:

1. Crie uma tabela de rotas personalizada em cada centro. No exemplo, a tabela de rotas é **RT_VNet**. Para criar uma tabela de rotas, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md). Para obter mais informações sobre as tabelas de rotas, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
2. Quando criar a tabela de rota **RT_VNet,** configufique as seguintes definições:

   * **Associação**: Selecione os VNets que pretende isolar.
   * **Propagação**: Selecione a opção para os balcões, implicando ligações de ramo (VPN/ER/P2S) propagar-se-ão rotas para este quadro de rotas.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="VNets isolados":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
