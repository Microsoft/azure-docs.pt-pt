---
title: Como monitorizar as aplicações apache spark
description: Utilize o Azure Synapse Studio para monitorizar as suas aplicações Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: e646f1bc1a25f58dd54437cbd77750479ac9ae99
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970909"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Utilize o Azure Synapse Studio (pré-visualização) para monitorizar as suas aplicações Apache Spark

Com a Azure Synapse Analytics, você pode usar a Spark para executar cadernos, empregos e outros tipos de aplicações nas suas piscinas Spark no seu espaço de trabalho.

Este artigo explica como monitorizar as suas aplicações Apache Spark, permitindo-lhe estar atento ao estado mais recente, problemas e progressos.

## <a name="accessing-the-list-of-apache-spark-applications"></a>Aceder à lista de aplicações da Apache Spark

Para ver a lista de aplicações Apache Spark no seu espaço de trabalho, abra primeiro [o Azure Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

![Selecione Monitor hub](./media/common/left-nav.png)

Selecione **as aplicações Apache Spark** para ver a lista de aplicações apache spark.

 ![Selecione aplicações Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>Filtrar as suas aplicações Apache Spark

Pode filtrar a lista de aplicações apache spark para as que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas as aplicações Apache Spark que contêm o nome "vendas":

![Botão de filtro](./media/common/filter-button.png)

![Filtro de amostra](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>Visualização de detalhes sobre uma aplicação específica do Apache Spark

Para ver os detalhes sobre uma das suas aplicações Apache Spark, selecione a aplicação Apache Spark e veja os detalhes. Se a aplicação Apache Spark ainda estiver em funcionamento, pode monitorizar o progresso. [Leia mais.](apache-spark-applications.md)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o gasoduto de monitorização, consulte o gasoduto Monitor que executa o artigo [do Azure Synapse Studio.](how-to-monitor-pipeline-runs.md) 

Para obter mais informações sobre a aplicação Apache Spark, consulte as aplicações Monitor Apache Spark no artigo do [Azure Synapse Studio.](apache-spark-applications.md)