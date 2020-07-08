---
title: 'Cenário: Isolamento personalizado para VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para o encaminhamento - evite que os VNets selecionados possam chegar uns aos outros
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569017"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Cenário: Isolamento personalizado para VNets

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Num cenário de isolamento personalizado para os VNets, o objetivo é evitar que um conjunto específico de VNets seja capaz de alcançar outro conjunto específico de VNets. No entanto, os VNets são necessários para chegar a todos os ramos (VPN/ER/User VPN).

Neste cenário, as ligações VPN, ExpressRoute e User VPN (colectivamente chamadas de sucursais) estão associadas à mesma tabela de rotas (Tabela de Rotas Padrão). Todas as ligações VPN, ExpressRoute e User VPN propagam rotas para o mesmo conjunto de tabelas de rotas. Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="architecture"></a>Fluxo de trabalho de cenário

Na **Figura 1,** existem ligações VNet Azul e Vermelha.

* Os VNets ligados a azul podem alcançar-se entre si, bem como alcançar todas as ligações de ramos (VPN/ER/P2S).
* Os VNets vermelhos podem chegar uns aos outros, assim como alcançar todas as ligações de ramos (VPN/ER/P2S).

Considere os seguintes passos ao configurar o encaminhamento.

1. Crie duas tabelas de rota personalizadas no portal Azure, **RT_BLUE** e **RT_RED**.
2. Para o quadro **de rota RT_BLUE,** em:
   * **Associação**: Selecione todos os VNets Azuis
   * **Propagação**: Para sucursais, selecione a opção para os balcões, implicando ligações de ramo (VPN/ER/P2S) irá propagar rotas para este quadro de rotas.
3. Repita os mesmos passos para **RT_RED** tabela de rotas para VNets e ramos vermelhos (VPN/ER/P2S).

Isto resultará em alterações na configuração do encaminhamento, como visto na figura abaixo

**Figura 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="figura 1":::

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
