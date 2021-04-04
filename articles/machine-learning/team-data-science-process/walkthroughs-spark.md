---
title: Analytics on HDInsight Spark com PySpark, Scala - Processo de Ciência de Dados de Equipa
description: Exemplos do Processo de Ciência de Dados de Equipa que percorrem a utilização de PySpark e Scala numa Faísca Azure HDInsight.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320140"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark data science walkthroughs usando PySpark e Scala em Azure

Estes walkthroughs usam PySpark e Scala em um cluster Azure Spark para fazer análises preditivas. Seguem os passos delineados no Processo de Ciência de Dados da Equipa. Para uma visão geral do processo de ciência de dados da equipa, consulte o [Processo de Ciência de Dados.](overview.md) Para obter uma visão geral da Spark on HDInsight, consulte [Introdução à Faísca em HDInsight](../../hdinsight/spark/apache-spark-overview.md).

As análises adicionais da ciência dos dados que executam o Processo de Ciência de Dados de Equipa são agrupadas pela **plataforma** que utilizam. Consulte [Walkthroughs executando o Processo de Ciência de Dados de Equipa](walkthroughs.md) para uma itemização destes exemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Prever dicas de táxi usando PySpark em Azure Spark

Utilizando dados de táxi de Nova Iorque, o [Use Spark on Azure HDInsight](spark-overview.md) walkthrough prevê se uma gorjeta é paga e a gama de valores esperados. Este exemplo utiliza o Processo de Ciência de Dados de Equipa num cenário que usa um [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenheiros da viagem de táxi e dataset de tarifas de NYC publicamente disponíveis. Este tópico de visão geral usa um cluster HDInsight Spark e cadernos Jupyter PySpark. Estes cadernos mostram-lhe como explorar os seus dados e, em seguida, como criar e consumir modelos. O notebook avançado de exploração e modelação de dados mostra como incluir validação cruzada, varredura de hiper-parâmetros e avaliação de modelos.

### <a name="data-exploration-and-modeling-with-spark"></a>Exploração e modelação de dados com faísca 
Explore o conjunto de dados e crie, marque e avalie os modelos de machine learning trabalhando através dos [modelos de classificação e regressão de Criar binários para dados com o tópico do kit de ferramentas Spark MLlib.](spark-data-exploration-modeling.md)

### <a name="model-consumption"></a>Consumo de modelos
Para aprender a pontuar os modelos de classificação e regressão criados neste tópico, consulte [Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Trans-validação e varredura de hiperparímetro
Consulte [a exploração e modelação avançadas de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando a validação cruzada e a varredura de hiper-parâmetros.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Prever dicas de táxi usando Scala em Azure Spark

O [Use Scala com Spark on Azure](scala-walkthrough.md) walkthrough prevê se uma gorjeta é paga e a gama de valores que se espera que sejam pagos. Mostra como usar o Scala para tarefas de aprendizagem automática supervisionadas com a biblioteca de machine learning Spark (MLlib) e pacotes SparkML num cluster Azure HDInsight Spark. Acompanha-o através das tarefas que constituem o Processo de Ciência de [Dados](./index.yml): ingestão e exploração de dados, visualização, engenharia de recursos, modelação e consumo de modelos. Os modelos construídos incluem regressão logística e linear, florestas aleatórias e árvores impulsionadas pelo gradiente.


## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do processo de ciência de dados da equipa, consulte [a visão geral do processo de ciência de dados da equipa.](overview.md)

Para uma discussão sobre o ciclo de vida do Processo de Ciência de Dados de Equipa, consulte [o ciclo de vida do Processo de Ciência de Dados da Equipa.](lifecycle.md) Este ciclo de vida traça os passos, do início ao fim, que os projetos geralmente seguem quando são executados.