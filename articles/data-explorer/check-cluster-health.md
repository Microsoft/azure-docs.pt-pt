---
title: Verifique a saúde de um cluster Azure Data Explorer
description: Este artigo descreve passos para monitorizar a saúde do seu cluster Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60861323"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Verifique a saúde de um cluster Azure Data Explorer

Existem vários fatores que impactam a saúde de um cluster azure Data Explorer, incluindo CPU, memória e o subsistema do disco. Este artigo mostra alguns passos básicos que você pode tomar para medir a saúde de um aglomerado.

1. Inscreva-se [https://dataexplorer.azure.com](https://dataexplorer.azure.com)em .

1. No painel esquerdo, selecione o seu cluster e execute o seguinte comando.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Uma saída de 1 é saudável; uma saída de 0 não é saudável.

1. Assine no [portal Azure](https://portal.azure.com)e navegue para o seu cluster.

1. Em **Monitorização,** selecione **Métricas,** em seguida, selecione **Keep Alive**, como mostrado na imagem seguinte. Uma saída próxima de 1 significa um aglomerado saudável.

    ![Métrica cluster Keep Alive](media/check-cluster-health/portal-metrics.png)

1. É possível adicionar outras métricas ao gráfico. Selecione o gráfico **e,** em seguida, Adicionar métrica . Selecione outra métrica - este exemplo mostra **CPU**.

    ![Adicionar métrica](media/check-cluster-health/add-metric.png)

1. Se necessitar de assistência para diagnosticar problemas com a saúde de um cluster, por favor abra um pedido de apoio no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).