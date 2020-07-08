---
title: 'Cenário: Isolar as Redes'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - VNets isolados
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569011"
---
# <a name="scenario-isolating-vnets"></a>Cenário: Isolar as Redes

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o objetivo é evitar que os VNets sejam capazes de chegar a outros. Isto é conhecido como isolante VNets. A carga de trabalho dentro do VNet permanece isolada e não é capaz de comunicar com outros VNets, como em qualquer cenário. No entanto, os VNets são necessários para chegar a todos os ramos (VPN, ER e User VPN). Neste cenário, todas as ligações VPN, ExpressRoute e User VPN estão associadas à mesma tabela de rotas e a uma tabela de rotas. Todas as ligações VPN, ExpressRoute e User VPN propagam rotas para o mesmo conjunto de tabelas de rotas. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

A fim de configurar este cenário, tome em consideração os seguintes passos:

1. Crie uma tabela de rotas personalizada. No exemplo, a tabela de rotas é **RT_VNet**. Para criar uma tabela de rotas, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md). Para obter mais informações sobre as tabelas de rotas, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
2. Quando criar a tabela de rota **RT_VNet,** configufique as seguintes definições:

   * **Associação**: Selecione os VNets que pretende isolar.
   * **Propagação**: Selecione a opção para os balcões, implicando ligações de ramo (VPN/ER/P2S) propagar-se-ão rotas para este quadro de rotas.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="VNets isolados":::

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
