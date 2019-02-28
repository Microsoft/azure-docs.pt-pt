---
title: Dimensionar cluster do Explorador de dados do Azure para acomodar a pedido de alteração
description: Este artigo descreve os passos para aumentar verticalmente e reduzir verticalmente um cluster do Explorador de dados do Azure com base no pedido de alteração.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: a74c529fc3543d5cbdcf009a5b7736309e15569e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961708"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gerir o aumento vertical de cluster para acomodar a pedido de alteração

Dimensionar um cluster adequadamente é essencial para o desempenho do Explorador de dados do Azure. Mas a pedido num cluster não é possível prever com precisão absoluto. Um tamanho de cluster estático pode levar a subutilização ou utilização, nenhum dos quais é ideal.

Uma abordagem melhor é *dimensionamento* um cluster, adicionar e remover a capacidade e recursos de CPU com o pedido de alteração. Existem dois fluxos de trabalho de dimensionamento: vertical e horizontal. Este artigo mostra como gerir o aumento vertical de cluster.

1. Aceda ao seu cluster. Sob **configurações**, selecione **aumentar verticalmente**.

    É-lhe apresentada uma lista de SKUs disponíveis. Por exemplo, na figura a seguir, apenas quatro SKUs estão disponíveis.

    ![Aumentar verticalmente](media/manage-cluster-scale-up/scale-up.png)

    SKUs estão desativadas porque eles são o SKU atual, ou não estão disponíveis na região onde está localizado o cluster.

1. Para alterar o SKU, selecione o SKU que pretende e escolha o **selecione** botão.

> [!NOTE]
> O processo de aumentar verticalmente pode demorar alguns minutos e, durante esse período o cluster será suspensa. Tenha em atenção que a reduzir verticalmente pode prejudicar o desempenho do seu cluster.

Agora que fez uma operação de aumentar ou reduzir verticalmente para o seu cluster do Explorador de dados do Azure. Também pode [gerir cluster horizontal](manage-cluster-scale-out.md) para dinamicamente aumentar horizontalmente a contagem de instâncias com base nas métricas que especificar.

Se precisar de assistência com problemas de dimensionamento de clusters, [abra um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.
