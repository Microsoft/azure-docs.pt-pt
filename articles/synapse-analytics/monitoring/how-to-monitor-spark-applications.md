---
title: Monitore aplicações Apache Spark
description: Utilize o Azure Synapse Studio para monitorizar as suas aplicações Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 0e3b3178cd7600d21032fb8dd4b79dd41ec3f601
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194880"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Utilize o Azure Synapse Studio (pré-visualização) para monitorizar as suas aplicações Apache Spark

Com a Azure Synapse Analytics, você pode usar a Spark para executar cadernos, empregos e outros tipos de aplicações nas suas piscinas Spark no seu espaço de trabalho.

Este artigo explica como monitorizar as suas aplicações Spark, permitindo-lhe estar atento ao estado mais recente, problemas e progressos.

## <a name="accessing-the-list-of-spark-applications"></a>Aceder à lista de aplicações Spark

Para ver a lista de aplicações Spark no seu espaço de trabalho, abra primeiro [o Azure Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

  > [!div class="mx-imgBorder"]
  > ![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

  > [!div class="mx-imgBorder"]
  > ![Selecione Monitor hub](./media/common/left-nav.png)

Selecione **as aplicações Spark** para ver a lista de aplicações Spark.

  > [!div class="mx-imgBorder"]
  > ![Selecione aplicações Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrar as suas aplicações Spark

Pode filtrar a lista de aplicações Spark para as que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas as aplicações Spark que contêm o nome "vendas":

  > [!div class="mx-imgBorder"]
  > ![Botão de filtro](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Filtro de amostra](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Visualização de detalhes sobre uma aplicação específica de Spark

Para ver os detalhes sobre uma das suas aplicações Spark, selecione a aplicação Spark e veja os detalhes. Se a aplicação Spark ainda estiver em funcionamento, pode monitorizar o progresso.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o gasoduto de monitorização, consulte o gasoduto Monitor que executa o artigo [do Azure Synapse Studio.](how-to-monitor-pipeline-runs.md)  
