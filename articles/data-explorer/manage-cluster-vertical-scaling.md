---
title: Aumentar verticalmente um cluster do Explorador de dados do Azure para acomodar a pedido de alteração
description: Este artigo descreve os passos para aumentar verticalmente e reduzir verticalmente um cluster do Explorador de dados do Azure com base no pedido de alteração.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: dc9ca8bb592e699d19835efeafb91e81408ae297
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571533"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gerir o aumento vertical de cluster para acomodar a pedido de alteração

Existem dois fluxos de trabalho para dimensionar um cluster do Explorador de dados do Azure:
1. [Dimensionamento horizontal](manage-cluster-horizontal-scaling.md), também conhecido como aumentar horizontalmente.
2. Dimensionamento vertical, também denominado aumentar e reduzir verticalmente.

Este artigo mostra como gerir o dimensionamento vertical de cluster.

Dimensionar um cluster adequadamente é essencial para o desempenho do Explorador de dados do Azure. Mas a pedido num cluster não é possível prever com precisão absoluto. Um tamanho de cluster estático pode levar a subutilização ou utilização, nenhum dos quais é ideal. Uma abordagem melhor é *dimensionamento* um cluster, adicionar e remover a capacidade e recursos de CPU com o pedido de alteração. 

## <a name="steps-to-configure-vertical-scaling"></a>Passos para configurar o dimensionamento vertical

1. Aceda ao seu cluster. Sob **configurações**, selecione **aumentar verticalmente**.

    É-lhe apresentada uma lista de SKUs disponíveis. Por exemplo, na figura a seguir, apenas quatro SKUs estão disponíveis.

    ![Aumentar verticalmente](media/manage-cluster-vertical-scaling/scale-up.png)

    SKUs estão desativadas porque eles são o SKU atual, ou não estão disponíveis na região onde está localizado o cluster.

1. Para alterar o SKU, selecione o SKU que pretende e escolha o **selecione** botão.

> [!NOTE]
> O processo de dimensionamento vertical pode demorar alguns minutos e, durante esse período o cluster será suspensa. Tenha em atenção que a reduzir verticalmente pode prejudicar o desempenho do seu cluster.

Agora que fez uma operação de aumentar ou reduzir verticalmente para o seu cluster do Explorador de dados do Azure.

Se precisar de assistência com problemas de dimensionamento de clusters, [abra um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

* [Gerir o dimensionamento horizontal do cluster](manage-cluster-horizontal-scaling.md) para dinamicamente aumentar horizontalmente a contagem de instâncias com base nas métricas que especificar.

* Monitorize a utilização de recursos ao seguir este artigo: [Monitorizar o desempenho do Explorador de dados do Azure, estado de funcionamento e a utilização com métricas](using-metrics.md).

