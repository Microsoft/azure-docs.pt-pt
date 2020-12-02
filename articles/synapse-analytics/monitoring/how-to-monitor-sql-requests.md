---
title: Como monitorizar os pedidos do SQL no Synapse Studio
description: Saiba como monitorizar os seus pedidos SQL utilizando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467403"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Use o Synapse Studio para monitorizar os seus pedidos SQL

Com o Synapse Studio, pode executar scripts SQL nas piscinas SQL no seu espaço de trabalho.

Este artigo explica como monitorizar os seus pedidos SQL, permitindo-lhe estar atento ao estado de execução de pedidos e descobrir detalhes de pedidos históricos.

## <a name="access-sql-requests-list"></a>Lista de pedidos de acesso AO SQL

Para ver a lista de pedidos SQL no seu espaço de trabalho, abra primeiro [o Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

![Selecione Monitor hub](./media/common/left-nav.png)

Selecione **pedidos DE SQL** para ver a lista de pedidos SQL.

 ![Selecione pedidos DE SQL](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Selecione uma piscina SQL

Por predefinição, o histórico de pedidos SQL para a piscina SQL sem servidor incorporada é mostrado nesta vista. Você pode selecionar uma das suas piscinas SQL dedicadas para ver o histórico de pedidos SQL dessa piscina.

![Selecione piscina SQL](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Filtrar os seus pedidos SQL

Pode filtrar a lista de pedidos SQL para os que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas os pedidos SQL submetidos `maria@contoso.com` por:

![Filtro de amostra](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Ver detalhes sobre um pedido específico do SQL

Para ver os detalhes sobre um dos seus pedidos SQL, abra o pedido do SQL para navegar para a vista de detalhes. Para os pedidos complexos em piscinas SQL dedicadas, você pode monitorizar o progresso.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a monitorização do gasoduto, consulte o gasoduto Monitor que funciona no artigo [do Synapse Studio.](how-to-monitor-pipeline-runs.md) 

Para obter mais informações sobre a monitorização das aplicações apache Spark, consulte as aplicações Monitor Apache Spark no artigo [do Synapse Studio.](how-to-monitor-spark-applications.md)