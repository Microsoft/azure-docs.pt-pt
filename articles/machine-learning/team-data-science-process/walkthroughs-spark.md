---
title: Análise no HDInsight Spark com PySpark, Scala - processo de ciência de dados de equipa
description: Exemplos do processo de ciência de dados de equipe que percorrem o uso de PySpark e escalabilidade em um Azure HDInsight Spark.
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
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864150"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Instruções de ciência de dados HDInsight Spark com PySpark e Scala no Azure

Esta instruções passo a passo usar PySpark e Scala num cluster do Spark do Azure para fazer a Análise Preditiva. Eles seguem as etapas descritas no processo de ciência de dados de equipa. Para uma descrição geral do processo de ciência de dados de equipa, consulte [processo de ciência de dados](overview.md). Para uma descrição geral do Spark no HDInsight, consulte [introdução ao Spark no HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Instruções de ciência de dados adicionais que executar o processo de ciência de dados de equipa estão agrupadas pela **plataforma** que utilizam. Ver [instruções passo a passo, executar o processo de ciência de dados de equipa](walkthroughs.md) para também uma itemização destes exemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Prever gorjetas para táxis com PySpark no Spark do Azure

Com o uso de dados de táxi de Nova York, a instrução [use Spark no Azure HDInsight](spark-overview.md) prevê se uma gorjeta é paga e o intervalo de valores esperados. Este exemplo usa o processo de ciência de dados de equipe em um cenário usando um [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e recursos de engenharia de recurso do conjunto de dados publicamente disponível de corridas e tarifas de táxi de NYC. Este tópico de visão geral usa um cluster HDInsight Spark e notebooks Jupyter PySpark. Estes blocos de notas mostram-lhe como explorar os seus dados e, em seguida, como criar e consumir modelos. O bloco de notas do exploração e modelação avançada de dados mostra como incluir a validação cruzada, hyper varrimento, parâmetro e modelar a avaliação.

### <a name="data-exploration-and-modeling-with-spark"></a>Exploração de dados e modelação com o Spark 
Explorar o conjunto de dados e criar, classificação e avaliar os modelos de machine learning ao trabalhar com o [criar modelos de classificação e regressão binários para dados com o Kit de ferramentas do Spark MLlib](spark-data-exploration-modeling.md) tópico.

### <a name="model-consumption"></a>Consumo de modelo
Para saber como classificar modelos de classificação e regressão criados neste tópico, consulte [pontuação e avaliar modelos de aprendizagem automática criados com o Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>A validação cruzada e varrimento de hiper-parâmetros
Ver [avançada de exploração de dados e modelagem com o Spark](spark-advanced-data-exploration-modeling.md) na forma como os modelos podem ser treinado varrimento de validação cruzada e de hyper-parâmetro a utilizar.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Prever gorjetas para táxis com o Scala Spark do Azure

A instrução [use escalar com Spark no Azure](scala-walkthrough.md) prevê se uma gorjeta é paga e o intervalo de valores esperado a ser pago. Ele mostra como utilizar o Scala para supervisionadas do machine learning tarefas com o Spark do machine learning biblioteca (MLlib) e pacotes de SparkML num cluster do Azure HDInsight Spark. Ele explica as tarefas que constituem a [processo de ciência de dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestão de dados e exploração, visualização, engenharia de funcionalidades, modelação e consumo do modelo. Modelos construídos incluem regressão logística e linear, florestas aleatórias e árvores de elevada gradação.


## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral do processo de ciência de dados de equipe, consulte [visão geral do processo de ciência de dados de equipe](overview.md).

Para obter uma discussão sobre o ciclo de vida do processo de ciência de dados de equipe, consulte [ciclo de vida do processo de ciência de dados](lifecycle.md) Esse ciclo de vida descreve as etapas, do início ao fim, que os projetos geralmente seguem quando são executados. 

