---
title: Visão geral do Machine Learning – Azure HDInsight
description: Visão geral de Big Data opções de aprendizado de máquina para clusters no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 4a1e621570f3bf5fdb78a2fd7b29ce0d1b51187f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951790"
---
# <a name="machine-learning-on-hdinsight"></a>Aprendizado de máquina no HDInsight

O HDInsight permite o aprendizado de máquina com o Big Data, fornecendo a capacidade de obter informações valiosas de grandes quantidades (petabytes ou até mesmo exabytes) de dados estruturados, não estruturados e de movimentação rápida. Há várias opções de aprendizado de máquina no HDInsight: SparkML e Apache Spark MLlib, R, Apache Hive e o Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML e MLlib

O [HDInsight Spark](spark/apache-spark-overview.md) é uma oferta do [Apache Spark](https://spark.apache.org/)hospedada pelo Azure, uma estrutura de processamento de dados paralela, unificada e de código aberto que dá suporte ao processamento na memória para impulsionar Big data Analytics. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação distribuída de dentro da memória do Spark tornam uma boa opção para algoritmos iterativos, utilizada em cálculos de machine learning e do graph. Há duas bibliotecas de aprendizado de máquina escalonáveis que trazem recursos de modelagem de algoritmos para esse ambiente distribuído: MLlib e SparkML. MLlib contém a API original criada com base em RDDs. O SparkML é um pacote mais recente que fornece uma API de nível superior criada com base em dataframes para construir pipelines ML. O SparkML ainda não dá suporte a todos os recursos do MLlib, mas substitui o MLlib como a biblioteca de aprendizado de máquina padrão do Spark.

A biblioteca do Microsoft Machine Learning para Apache Spark é [MMLSpark](https://github.com/Azure/mmlspark). Essa biblioteca foi projetada para tornar os cientistas de dados mais produtivos no Spark, aumentar a taxa de experimentação e aproveitar as técnicas de aprendizado de máquina de ponta, incluindo aprendizado profundo, em conjuntos de dados muito grandes. O MMLSpark fornece uma camada sobre as APIs de baixo nível do SparkML ao criar modelos de ML escalonáveis, como a indexação de cadeias de caracteres, a coerção de dados em um layout esperado pelos algoritmos de aprendizado de máquina e a montagem de vetores de recursos. A biblioteca MMLSpark simplifica essas e outras tarefas comuns para a criação de modelos no PySpark.

## <a name="r"></a>R.

O [R](https://www.r-project.org/) é atualmente a linguagem de programação estatística mais popular do mundo. É uma ferramenta de visualização de dados de software livre com uma comunidade de mais de 2,5 milhões usuários e crescendo. Com sua base de usuários de prosperante e mais de 8.000 pacotes contribuídos, o R é uma opção provável para muitas empresas que precisam de aprendizado de máquina. Você pode criar um cluster HDInsight com os serviços ML prontos para uso com conjuntos de grandes volumes de imagens e modelos. Esse recurso fornece cientistas de dados e estatísticos com uma interface R familiar que pode ser dimensionada sob demanda por meio do HDInsight, sem a sobrecarga de configuração e manutenção do cluster.

![Treinamento para previsão com o R Server](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts do R.  Você também pode executar scripts do R em todos os nós do cluster usando a redução de mapa do Hadoop do scaler ou contextos de computação do Spark.

Com os serviços do ML no HDInsight com Spark, você pode paralelizar o treinamento em todos os nós de um cluster usando um contexto de computação do Spark. Você pode executar scripts do R diretamente no nó de borda, usando todos os núcleos disponíveis em paralelo, conforme necessário. Como alternativa, você pode executar o código do nó de borda para disparar o processamento que é distribuído em todos os nós no cluster. Os serviços de ML no HDInsight com Spark também habilitam funções de paralelização de pacotes de R de software livre, se desejado.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

Azure Machine Learning fornece ferramentas para modelar análises preditivas e um serviço totalmente gerenciado que você pode usar para implantar seus modelos de previsão como serviços Web prontos para consumo. Azure Machine Learning é uma solução completa de análise preditiva na nuvem que você pode usar para criar, testar, colocar em operação e gerenciar modelos de previsão. Selecione em uma biblioteca de algoritmos grande, use um estúdio baseado na Web para criar modelos e implante facilmente seu modelo como um serviço Web.

![Visão geral do Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Crie recursos para dados em um cluster Hadoop do HDInsight usando [consultas do hive](../machine-learning/team-data-science-process/create-features-hive.md). A *engenharia de recursos* tenta aumentar a potência preditiva dos algoritmos de aprendizado criando recursos de dados brutos que facilitam o processo de aprendizado. Você pode executar consultas HiveQL de Azure Machine Learning Studio (clássico) e acessar dados processados no hive e armazenados no armazenamento de BLOBs usando o [módulo importar dados](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Conjunto de Ferramentas Cognitivas da Microsoft

O [aprendizado profundo](https://www.microsoft.com/en-us/research/group/dltc/) é uma ramificação do aprendizado de máquina que usa redes neurais, inspiradas pelos processos biológicos do cérebro humano. Muitos pesquisadores veem o aprendizado profundo como uma abordagem promissora para aprimorar a inteligência artificial. Exemplos de aprendizado profundo são tradutores de linguagem falada, sistemas de reconhecimento de imagem e raciocínio de máquina.

Para ajudar a avançar seu próprio trabalho no aprendizado profundo, a Microsoft desenvolveu a [Microsoft cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)de código aberto gratuita, fácil de usar. Esse kit de ferramentas está sendo usado por uma ampla variedade de produtos da Microsoft, por empresas em todo o mundo, com uma necessidade de implantar o aprendizado profundo em escala e por alunos interessados nos algoritmos e técnicas mais recentes.

## <a name="see-also"></a>Ver também

### <a name="scenarios"></a>Cenários

* [Apache Spark com Machine Learning: Use o Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use o Spark no HDInsight para prever os resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Gerar recomendações de filme com o Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive e Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive e Azure Machine Learning de ponta a ponta](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Aprendizado de máquina com o Apache Spark no HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Recursos de aprendizado profundo

* [Usar Microsoft Cognitive Toolkit modelo de aprendizado profundo com Azure HDInsight Spark cluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Usar Caffe em Azure HDInsight Spark para aprendizado profundo distribuído](spark/apache-spark-deep-learning-caffe.md)
* [Estruturas de aprendizado profundo e de ia no Máquina Virtual de Ciência de Dados (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
