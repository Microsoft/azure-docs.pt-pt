---
title: O pipeline monitor executa o Azure Synapse Studio (pré-visualização)
description: Utilize o Azure Synapse Studio para monitorizar as suas operações de gasoduto de espaço de trabalho.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 16400d7f292ad5844add3d4a5fc019e84bd27127
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85194897"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Use o Azure Synapse Studio para monitorizar as suas corridas de gasodutos de espaço de trabalho

Com o Azure Synapse Analytics, pode criar oleodutos complexos que podem automatizar e orquestrar o seu movimento de dados, transformação de dados e atividades de computação dentro da sua solução. Pode autorizar e monitorizar estes oleodutos utilizando o Azure Synapse Studio (pré-visualização).

Este artigo explica como monitorizar o seu pipeline runs, o que lhe permite estar atento ao estado mais recente, problemas e progresso dos seus oleodutos.

## <a name="access-the-list-of-pipeline-runs"></a>Aceda à lista de corridas de gasodutos

Para ver a lista de gasodutos que funciona no seu espaço de trabalho, abra primeiro [o Azure Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

![Selecione Monitor hub](./media/common/left-nav.png)

Select **Pipeline corre** para ver a lista de corridas de gasodutos.

![Selecione o gasoduto corre](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrar o seu oleoduto corre

Pode filtrar a lista de oleodutos para os que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas o gasoduto correr para o gasoduto chamado "férias":

![Botão de filtro](./media/common/filter-button.png)

![Filtro de amostra](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Visualização de detalhes sobre uma corrida específica do gasoduto

Para ver detalhes sobre o seu percurso de gasoduto, selecione o curso do gasoduto. Em seguida, ver a atividade funciona associada à corrida do gasoduto. Se o oleoduto ainda estiver a funcionar, pode monitorizar o progresso. 
  
## <a name="next-steps"></a>Próximos passos

Para saber mais sobre aplicações de monitorização, consulte o artigo [de aplicações monitor Apache Spark.](how-to-monitor-spark-applications.md) 
