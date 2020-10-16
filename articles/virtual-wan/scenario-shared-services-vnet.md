---
title: 'Cenário: Rota para Serviços Partilhados VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - crie rotas para aceder a um VNet de Serviço Partilhado com uma carga de trabalho que pretende que cada VNet e Branch acedam.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: b8cc59b805cd757edce79a14d124ea244b4652a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267487"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Cenário: Rota para Serviços Partilhados VNets

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o objetivo é criar rotas para aceder a um VNet **de Serviço Partilhado** com cargas de trabalho que pretende que cada VNet e Branch (VPN/ER/P2S) acedam. Exemplos destas cargas de trabalho partilhadas podem incluir Máquinas Virtuais com serviços como Controladores de Domínio ou Partilhas de Ficheiros, ou serviços Azure expostos internamente através de [Azure Private Endpoints](../private-link/private-endpoint-overview.md).

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Podemos usar uma matriz de conectividade para resumir os requisitos deste cenário. Na matriz, cada célula descreve se uma ligação WAN virtual (o lado "From" do fluxo, os cabeçalhos de linha na tabela) aprende um prefixo de destino (o lado "To" do fluxo, os cabeçalhos da coluna em itálico na tabela) para um fluxo de tráfego específico. Um "X" significa que a conectividade é fornecida pela VIRTUAL WAN:

**Matriz de conectividade**

| De             | Para:   |*VNets isolados*|*VNet compartilhado*|*Ramos*|
|---|---|---|---|---|
|**VNets isolados**|&#8594;|                |        X        |       X      |
|**VNets compartilhados**  |&#8594;|       X        |        X        |       X      |
|**Ramos**      |&#8594;|       X        |        X        |       X      |

Semelhante ao [cenário VNet isolado,](scenario-isolate-vnets.md)esta matriz de conectividade dá-nos dois padrões de linha diferentes, que se traduzem em duas tabelas de rotas (os VNets de serviços partilhados e os ramos têm os mesmos requisitos de conectividade). A WAN virtual já tem uma tabela de rotas padrão, por isso vamos precisar de outra tabela de rotas personalizada, que chamaremos **RT_SHARED** neste exemplo.

Os VNets serão associados à tabela de rotas **RT_SHARED.** Uma vez que precisam de conectividade com os balcões e com os VNets de serviço partilhado, o serviço partilhado VNet e os ramos terão de se propagar para **RT_SHARED** (caso contrário, os VNets não aprenderiam o ramo e os prefixos VNet partilhados). Como os balcões estão sempre associados à tabela de rotas padrão, e os requisitos de conectividade são os mesmos para os VNets de serviços partilhados, também associaremos os VNets de serviço partilhado à tabela de rotas Padrão.

Como resultado, este é o design final:

* Redes virtuais isoladas:
  * Tabela de rotas associada: **RT_SHARED**
  * Propagação para tabelas de rotas: **Predefinido**
* Redes virtuais de Serviços Partilhados:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **RT_SHARED** e **Padrão**
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **RT_SHARED** e **Padrão**

> [!NOTE]
> Se o seu WAN Virtual estiver implantado em várias regiões, terá de criar a tabela de rotas **RT_SHARED** em todos os hubs, e as rotas de cada serviço partilhado VNet e conexão de filial precisam de ser propagadas às tabelas de rotas em todos os hubs virtuais usando etiquetas de propagação.

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para configurar o cenário, considere os seguintes passos:

1. Identifique os **Serviços Partilhados** VNet.
2. Crie uma tabela de rotas personalizada. No exemplo, referimo-nos à tabela de rotas como **RT_SHARED**. Para obter passos para criar uma tabela de rotas, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md). Utilize os seguintes valores como orientação:

   * **Associação**
     * Para **VNets, *exceto* o VNet dos Serviços Partilhados,** selecione os VNets para isolar. Isto implicará que todos estes VNets (exceto os serviços partilhados VNet) poderão chegar ao destino com base nas rotas de RT_SHARED tabela de rotas.

   * **Propagação**
      * Para **o Branches,** propagar rotas para esta tabela de rotas, para além de quaisquer outras tabelas de rotas que já tenha selecionado. Por causa deste passo, a tabela de rotas RT_SHARED aprenderá rotas de todas as ligações de ramificações (VPN/ER/User VPN).
      * Para **VNets**, selecione o **VNet dos Serviços Partilhados**. Por causa deste passo, RT_SHARED tabela de rotas aprenderá rotas a partir da ligação VNet dos Serviços Partilhados.

Isto resultará na configuração de encaminhamento mostrada na seguinte figura:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Serviços partilhados VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
