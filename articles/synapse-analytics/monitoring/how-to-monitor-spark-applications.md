---
title: Monitor Apache Spark aplicações
description: Utilize o Estúdio Azure Synapse para monitorizar as suas aplicações Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430750"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Utilize o Estúdio Azure Synapse (pré-visualização) para monitorizar as suas aplicações Apache Spark

Com a Azure Synapse Analytics, pode utilizar a Spark para executar cadernos, empregos e outros tipos de aplicações nas suas piscinas Spark no seu espaço de trabalho.

Este artigo explica como monitorizar as suas aplicações Spark, permitindo-lhe manter um olho no estado mais recente, questões e progresso.

## <a name="accessing-the-list-of-spark-applications"></a>Aceder à lista de aplicações spark

Para ver a lista de aplicações Spark no seu espaço de trabalho, abra primeiro [o Estúdio Azure Synapse](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

  > [!div class="mx-imgBorder"]
  > ![Iniciar sessão no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

  > [!div class="mx-imgBorder"]
  > ![Selecione monitor hub](./media/common/left-nav.png)

Selecione **aplicações Spark** para visualizar a lista de aplicações Spark.

  > [!div class="mx-imgBorder"]
  > ![Selecione aplicações Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrar as suas aplicações Spark

Pode filtrar a lista de aplicações spark para as que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas as aplicações Spark que contêm o nome "vendas":

  > [!div class="mx-imgBorder"]
  > ![Botão de filtro](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Filtro de amostra](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Visualização de detalhes sobre uma aplicação específica da Spark

Para ver os detalhes sobre uma das suas aplicações Spark, selecione a aplicação Spark e veja os detalhes. Se a aplicação Spark ainda estiver em funcionamento, pode monitorizar o progresso.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a monitorização do gasoduto, consulte o pipeline Monitor executa o artigo do [Estúdio Azure Synapse.](how-to-monitor-pipeline-runs.md)  
