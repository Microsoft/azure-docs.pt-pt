---
title: Como monitorizar as aplicações da Apache Spark no Synapse Studio
description: Aprenda a monitorizar as suas aplicações Apache Spark utilizando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: c1545efc43d034dba5b8ffe8d19b9bbee95dff68
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455471"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Use o Synapse Studio para monitorizar as suas aplicações Apache Spark

Com a Azure Synapse Analytics, você pode usar a Spark para executar cadernos, empregos e outros tipos de aplicações nas suas piscinas Spark no seu espaço de trabalho.

Este artigo explica como monitorizar as suas aplicações Apache Spark, permitindo-lhe estar atento ao estado mais recente, problemas e progressos.

## <a name="access-apache-spark-applications-list"></a>Aceder lista de aplicações do Apache Spark

Para ver a lista de aplicações Apache Spark no seu espaço de trabalho, abra primeiro [o Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

![Selecione Monitor hub](./media/common/left-nav.png)

Selecione **as aplicações Apache Spark** para ver a lista de aplicações apache spark.

 ![Selecione aplicações Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrar as suas aplicações Apache Spark

Pode filtrar a lista de aplicações apache spark para as que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas as aplicações Apache Spark que contêm o nome "vendas":

![Filtro de amostra](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Ver detalhes sobre uma aplicação específica da Apache Spark

Para ver os detalhes sobre uma das suas aplicações Apache Spark, selecione a aplicação Apache Spark e veja os detalhes. Se a aplicação Apache Spark ainda estiver em funcionamento, pode monitorizar o progresso. [Leia mais.](apache-spark-applications.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a monitorização do gasoduto, consulte o gasoduto Monitor que executa o artigo [do Synapse Studio.](how-to-monitor-pipeline-runs.md) 

Para obter mais informações sobre a aplicação Apache Spark, consulte as aplicações Monitor Apache Spark no artigo [do Synapse Studio.](apache-spark-applications.md)