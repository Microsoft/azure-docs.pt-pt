---
title: Como monitorizar as piscinas Apache Spark no Synapse Studio
description: Aprenda a monitorizar as suas piscinas Apache Spark utilizando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467243"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Use o Synapse Studio para monitorizar as suas piscinas Apache Spark

Com a Azure Synapse Analytics, você pode usar Spark para executar cadernos, empregos e outros tipos de aplicações em piscinas Spark no seu espaço de trabalho.

Este artigo explica como monitorizar as suas piscinas Apache Spark, permitindo-lhe manter um olho no estado das suas piscinas, incluindo quantos vCores estão a ser utilizados por diferentes utilizadores do espaço de trabalho.

## <a name="access-spark-pools-list"></a>Lista de piscinas de acesso Spark

Para ver a lista de piscinas Apache Spark no seu espaço de trabalho, abra primeiro [o Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

![Selecione Monitor hub](./media/common/left-nav.png)

Selecione **as piscinas Apache Spark** para ver a lista de piscinas Apache Spark.

 ![Selecione piscinas Apache Spark](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Filtrar as suas piscinas spark

Você pode filtrar a lista de piscinas Spark para aqueles que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas os pools Spark contendo o nome "dataprep":

![Filtro de amostra](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Ver detalhes sobre uma piscina de faíscas específica

Para ver os detalhes sobre uma das suas piscinas Spark, selecione a piscina Spark para ver os detalhes.

![Detalhes da piscina Apache Spark](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a monitorização do gasoduto, consulte o gasoduto Monitor que funciona no artigo [do Synapse Studio.](how-to-monitor-pipeline-runs.md) 

Para obter mais informações sobre a monitorização das aplicações apache Spark, consulte as aplicações Monitor Apache Spark no artigo [do Synapse Studio.](how-to-monitor-spark-applications.md)
