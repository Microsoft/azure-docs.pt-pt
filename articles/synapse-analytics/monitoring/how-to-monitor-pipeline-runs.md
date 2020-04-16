---
title: Monitor pipeline executa Estúdio Azure Synapse (pré-visualização)
description: Utilize o Estúdio Azure Synapse para monitorizar as suas corridas de gasodutos espaciais de trabalho.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430789"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Use o Azure Synapse Studio para monitorizar as suas corridas de gasodutos espaciais de trabalho

Com a Azure Synapse Analytics, pode criar oleodutos complexos que podem automatizar e orquestrar o seu movimento de dados, transformação de dados e atividades de computação dentro da sua solução. Pode ser autor e monitorizar estes oleodutos utilizando o Azure Synapse Studio (pré-visualização).

Este artigo explica como monitorizar as correções do seu pipeline, o que lhe permite manter um olho no estado mais recente, questões e progresso dos seus oleodutos.

## <a name="access-the-list-of-pipeline-runs"></a>Aceda à lista de corridas de gasodutos

Para ver a lista de pipeline corre no seu espaço de trabalho, abra primeiro [o Estúdio Azure Synapse](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

![Iniciar sessão no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

![Selecione monitor hub](./media/common/left-nav.png)

Selecione **Pipeline corre** para ver a lista de corridas de gasodutos.

![Selecione corridas de gasodutos](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrar o seu pipeline corre

Pode filtrar a lista de gasodutos para os que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas o gasoduto executado para o gasoduto chamado "férias":

![Botão de filtro](./media/common/filter-button.png)

![Filtro de amostra](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Visualização de detalhes sobre uma execução específica do gasoduto

Para ver detalhes sobre a execução do seu pipeline, selecione a execução do gasoduto. Em seguida, veja as operações associadas à execução do gasoduto. Se o oleoduto ainda estiver em funcionamento, pode monitorizar o progresso. 
  
## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre aplicações de monitorização, consulte o artigo [aplicações do Monitor Apache Spark.](how-to-monitor-spark-applications.md) 
