---
title: Analytics em HDInsight Spark com PySpark, Scala - Team Data Science Process
description: Exemplos do Processo de Ciência de Dados da Equipa que passam pelo uso de PySpark e Scala numa Faísca Azure HDInsight.
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
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864150"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark data science walkthroughs usando PySpark e Scala em Azure

Estes passadiços usam PySpark e Scala num aglomerado de Faíscas Azure para fazer análises preditivas. Seguem os passos delineados no Processo de Ciência de Dados da Equipa. Para uma visão geral do Processo de Ciência de Dados da Equipa, consulte o [Processo de Ciência](overview.md)de Dados . Para uma visão geral da Spark no HDInsight, consulte [Introdução à Faísca no HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Os walkthroughs adicionais de ciência de dados que executam o Processo de Ciência de Dados da Equipa são agrupados pela **plataforma** que utilizam. Consulte [walkthroughs executando o Processo](walkthroughs.md) de Ciência de Dados da Equipa para uma itemização destes exemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Preveja dicas de táxi usando PySpark em Azure Spark

Utilizando dados de táxi nova-iorquinos, o [passeio de Use Spark no Azure HDInsight](spark-overview.md) prevê se uma gorjeta é paga e a gama de valores esperados. Este exemplo utiliza o Processo de Ciência de Dados da Equipa num cenário que utiliza um [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e utilizar dados de engenheiros da viagem de táxi e datação de tarifas de NYC publicamente disponíveis. Este tópico de visão geral utiliza um cluster HDInsight Spark e cadernos Jupyter PySpark. Estes cadernos mostram-lhe como explorar os seus dados e, em seguida, como criar e consumir modelos. O caderno avançado de exploração de dados e modelação mostra como incluir validação cruzada, varredura de hiperparâmetros e avaliação de modelos.

### <a name="data-exploration-and-modeling-with-spark"></a>Exploração e modelação de dados com faísca 
Explore o conjunto de dados e crie, marque e avalie os modelos de machine learning trabalhando através dos modelos de [classificação binária e regressão para dados com o tópico de toolkit Spark MLlib.](spark-data-exploration-modeling.md)

### <a name="model-consumption"></a>Consumo de modelos
Para aprender a pontuar os modelos de classificação e regressão criados neste tópico, consulte [o Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Validação cruzada e varredura de hiperparâmetro
Consulte a [pesquisa e modelação avançada de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando validação cruzada e varredura de hiperparâmetros.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Preveja dicas de táxi usando Scala em Azure Spark

O [Use Scala com Faísca no](scala-walkthrough.md) passeio de Azure prevê se uma gorjeta é paga e o intervalo de valores que se espera que sejam pagos. Mostra como usar o Scala para tarefas de aprendizagem automática supervisionadas com a biblioteca de machine learning Spark (MLlib) e os pacotes SparkML num cluster Azure HDInsight Spark. Percorre-o através das tarefas que constituem o Processo de Ciência de [Dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestão e exploração de dados, visualização, engenharia de recursos, modelação e consumo de modelos. Os modelos construídos incluem regressão logística e linear, florestas aleatórias e árvores impulsionadas por gradientes.


## <a name="next-steps"></a>Passos seguintes

Para uma visão geral do Processo de Ciência de Dados da Equipa, consulte a [visão geral](overview.md)do Processo de Ciência de Dados da Equipa .

Para uma discussão sobre o ciclo de vida do Processo de Ciência de Dados da Equipa, consulte o ciclo de vida do Processo de Ciência de Dados da [Equipa.](lifecycle.md) Este ciclo de vida descreve os passos, do início ao fim, que os projetos geralmente seguem quando são executados. 

