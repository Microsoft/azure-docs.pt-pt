---
title: 'Cenário: Rota para Serviços Partilhados VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - crie rotas para aceder a um VNet de Serviço Partilhado com uma carga de trabalho que pretende que cada VNet e Branch acedam.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569260"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Cenário: Rota para Serviços Partilhados VNets

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o objetivo é criar rotas para aceder a um VNet **de Serviço Partilhado** com carga de trabalho que pretende que todos os VNet e Branch (VPN/ER/P2S) tenham acesso. Para obter informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho de cenário

A fim de configurar este cenário, tome em consideração os seguintes passos:

1. Identifique os **Serviços Partilhados** VNet.
2. Crie uma tabela de rotas personalizada. No exemplo, referimo-nos a esta tabela de rotas como **RT_SHARED**. Para obter passos para criar uma tabela de rotas, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md). Utilize os seguintes valores como orientação:

   * **Associação**
     * Para **VNets, *exceto* o VNet dos Serviços Partilhados,** selecione os VNets para isolar. Isto implicará que todos estes VNets (exceto os serviços partilhados VNet) poderão chegar ao destino com base nas rotas de RT_SHARED tabela de rotas.

   * **Propagação**
      * Para **o Branches,** propagar rotas para esta tabela de rotas, para além de quaisquer outras tabelas de rotas que já tenha selecionado. Devido a este passo, RT_SHARED tabela de rotas aprenderá rotas de todas as ligações de ramificação (VPN/ER/User VPN).
      * Para **VNets**, selecione o **VNet dos Serviços Partilhados**. Devido a este passo, RT_SHARED tabela de rotas aprenderá rotas a partir da ligação VNet dos Serviços Partilhados.

     Isto resultará em alterações na configuração do encaminhamento, como visto na figura abaixo

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Serviços partilhados VNet":::

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
