---
title: 'Cenário: Rota para serviços partilhados VNets'
titleSuffix: Azure Virtual WAN
description: Cenários para encaminhamento - crie rotas para aceder a um VNet de serviço partilhado com uma carga de trabalho que pretende que cada VNet e sucursal acedam.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e0d05d2cb960e760809ab35a8f9e4ca04acf250
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442966"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Cenário: Rota para serviços partilhados VNets

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Neste cenário, o objetivo é criar rotas para aceder a um VNet **de Serviço Partilhado** com cargas de trabalho que pretende que cada VNet e Branch (VPN/ER/P2S) acedam. Exemplos destas cargas de trabalho partilhadas podem incluir Máquinas Virtuais com serviços como Controladores de Domínio ou Partilhas de Ficheiros, ou serviços Azure expostos internamente através de [Azure Private Endpoints](../private-link/private-endpoint-overview.md).

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Podemos usar uma matriz de conectividade para resumir os requisitos deste cenário:

**Matriz de conectividade**

| De             | Para:   |*VNets isolados*|*VNet compartilhado*|*Ramos*|
|---|---|---|---|---|
|**VNets isolados**| ->|        | Direct | Direct |
|**VNets compartilhados**  |->| Direct | Direct | Direct |
|**Ramos**      |->| Direct | Direct | Direct |

Cada uma das células da tabela anterior descreve se uma ligação WAN virtual (o lado "From" do fluxo, os cabeçalhos de linha) comunica com um destino (o lado "To" do fluxo, os cabeçalhos da coluna em itálico). Neste cenário não existem firewalls ou Aparelhos Virtuais de Rede, pelo que a comunicação flui diretamente sobre o WAN Virtual (daí a palavra "Direto" na tabela).

À semelhança do [cenário VNet isolado,](scenario-isolate-vnets.md)esta matriz de conectividade dá-nos dois padrões de linha diferentes, que se traduzem em duas tabelas de rotas (os VNets de serviços partilhados e os ramos têm os mesmos requisitos de conectividade). A WAN virtual já tem uma tabela de rotas padrão, por isso vamos precisar de outra tabela de rotas personalizada, que chamaremos **RT_SHARED** neste exemplo.

Os VNets serão associados à tabela de rotas **RT_SHARED.** Uma vez que precisam de conectividade com os balcões e com os VNets de serviço partilhado, o serviço partilhado VNet e os ramos terão de se propagar para **RT_SHARED** (caso contrário, os VNets não aprenderiam o ramo e os prefixos VNet partilhados). Como os balcões estão sempre associados à tabela de rotas padrão, e os requisitos de conectividade são os mesmos para os VNets de serviços partilhados, também associaremos os VNets de serviço partilhado à tabela de rotas Padrão.

Como resultado, este é o design final:

* Redes virtuais isoladas:
  * Tabela de rotas associada: **RT_SHARED**
  * Propagação para tabelas de rotas: **Predefinido**
* Redes virtuais de serviços partilhados:
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

1. Identifique os **serviços partilhados** VNet.
2. Crie uma tabela de rotas personalizada. No exemplo, referimo-nos à tabela de rotas como **RT_SHARED**. Para obter passos para criar uma tabela de rotas, consulte [Como configurar o encaminhamento do hub virtual](how-to-virtual-hub-routing.md). Utilize os seguintes valores como orientação:

   * **Associação**
     * Para **VNets, *exceto* os serviços partilhados VNet,** selecione os VNets para isolar. Isto implicará que todos estes VNets (exceto os serviços partilhados VNet) poderão chegar ao destino com base nas rotas de RT_SHARED tabela de rotas.

   * **Propagação**
      * Para **o Branches,** propagar rotas para esta tabela de rotas, para além de quaisquer outras tabelas de rotas que já tenha selecionado. Por causa deste passo, a tabela de rotas RT_SHARED aprenderá rotas de todas as ligações de ramificações (VPN/ER/User VPN).
      * Para **VNets,** selecione os **serviços partilhados VNet**. Por causa deste passo, RT_SHARED tabela de rotas aprenderá rotas a partir da ligação VNet de serviços partilhados.

Isto resultará na configuração de encaminhamento mostrada na seguinte figura:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Diagrama para serviços partilhados VNet." lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Passos seguintes

* Para configurar utilizando um modelo ARM, consulte [Quickstart: Rota para serviços partilhados VNets usando um modelo ARM](quickstart-route-shared-services-vnet-template.md).
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
