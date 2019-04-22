---
title: Aumentar verticalmente um cluster do Explorador de dados do Azure para acomodar a pedido de alteração
description: Este artigo descreve os passos para aumentar verticalmente e reduzir verticalmente um cluster do Explorador de dados do Azure com base no pedido de alteração.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783871"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gerir o aumento vertical de cluster para acomodar a pedido de alteração

Existem dois fluxos de trabalho para dimensionar um cluster do Explorador de dados do Azure: vertical e [horizontal](manage-cluster-scale-out.md). Este artigo mostra como gerir o aumento vertical de cluster.

Dimensionar um cluster adequadamente é essencial para o desempenho do Explorador de dados do Azure. Mas a pedido num cluster não é possível prever com precisão absoluto. Um tamanho de cluster estático pode levar a subutilização ou utilização, nenhum dos quais é ideal. Uma abordagem melhor é *dimensionamento* um cluster, adicionar e remover a capacidade e recursos de CPU com o pedido de alteração. 

## <a name="steps-to-scale-up"></a>Passos para aumentar verticalmente
1. Aceda ao seu cluster. Sob **configurações**, selecione **aumentar verticalmente**.

    É-lhe apresentada uma lista de SKUs disponíveis. Por exemplo, na figura a seguir, apenas quatro SKUs estão disponíveis.

    ![Aumentar verticalmente](media/manage-cluster-scale-up/scale-up.png)

    SKUs estão desativadas porque eles são o SKU atual, ou não estão disponíveis na região onde está localizado o cluster.

1. Para alterar o SKU, selecione o SKU que pretende e escolha o **selecione** botão.

> [!NOTE]
> O processo de aumentar verticalmente pode demorar alguns minutos e, durante esse período o cluster será suspensa. Tenha em atenção que a reduzir verticalmente pode prejudicar o desempenho do seu cluster.

Agora que fez uma operação de aumentar ou reduzir verticalmente para o seu cluster do Explorador de dados do Azure.

## <a name="next-steps"></a>Passos Seguintes
Também pode [gerir cluster horizontal](manage-cluster-scale-out.md) para dinamicamente aumentar horizontalmente a contagem de instâncias com base nas métricas que especificar.

Se precisar de assistência com problemas de dimensionamento de clusters, [abra um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.

Monitorize a utilização de recursos ao seguir este artigo: [Monitorizar o desempenho do Explorador de dados do Azure, estado de funcionamento e a utilização com métricas](using-metrics.md).
