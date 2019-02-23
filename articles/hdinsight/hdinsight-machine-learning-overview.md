---
title: Machine learning a descrição geral - Azure HDInsight
description: Descreve o machine learning opções no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: fc7c957b766c1c95ea247ccb63cad5bedec6feec
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669007"
---
# <a name="machine-learning-on-hdinsight"></a>Machine learning no HDInsight

HDInsight permite que o machine learning com grandes volumes de dados, fornecendo a capacidade para obter informações valiosas de grandes quantidades (petabytes ou até mesmo exabytes) de não estruturados e os dados de rápida evolução. Existem vários aprendizagem automática opções no HDInsight:  SparkML e Apache Spark MLlib, R, Apache Hive e o Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML e o MLlib

[Spark do HDInsight](spark/apache-spark-overview.md) é uma oferta alojado no Azure de [Apache Spark](https://spark.apache.org/), um unificada de código fonte aberto, a arquitetura de processamento de dados paralela que suporta o processamento de dentro da memória para melhorar a análise de macrodados. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação distribuída de dentro da memória do Spark tornam uma boa opção para algoritmos iterativos, utilizada em cálculos de machine learning e do graph. Existem duas bibliotecas de aprendizado de máquina dimensionável que fornecer capacidades de modelação de algoritmos para este ambiente distribuído: MLlib e SparkML. MLlib contém a API original criada com base no RDDs. SparkML é um pacote mais recente que fornece uma API de nível mais alto assentes DataFrames para a criação de pipelines de ML. SparkML ainda não suporta todas as funcionalidades de MLlib, mas está a substituir o MLlib como biblioteca de aprendizagem automática padrão do Spark.

A biblioteca do Microsoft Machine Learning para Apache Spark está [MMLSpark](https://github.com/Azure/mmlspark). Esta biblioteca foi concebida para tornar os cientistas de dados mais produtivos no Spark, aumentam a taxa de experimentação e tirar partido das técnicas de aprendizagem de última geração, incluindo a aprendizagem profunda, em conjuntos de dados muito grandes. MMLSpark fornece uma camada sobre APIs de baixo nível do SparkML durante a criação de modelos de ML dimensionáveis, como cadeias de caracteres de indexação, impor dados num layout esperado pela máquina algoritmos de aprendizagem e a criação de vetores de funcionalidade. A biblioteca de MMLSpark simplifica estas e outras tarefas comuns para a criação de modelos no PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) está, atualmente, a linguagem de programação estatística mais popular do mundo. É uma ferramenta de visualização de dados de código-fonte aberto com uma Comunidade de mais de 2,5 milhões de utilizadores e a crescer. Com sua base de usuários próspera e mais de 8.000 pacotes fornecidos pela, R é uma opção provável para muitas empresas que precisam de aprendizagem automática. Pode criar um cluster do HDInsight com os serviços de ML pronta para ser utilizada com modelos e conjuntos de dados maciços. Esta capacidade fornece cientistas de dados e estatísticos com uma interface familiar do R que pode ser dimensionado a pedido através do HDInsight, sem a sobrecarga de manutenção e configuração do cluster.

![Treinamento de previsão com R server](./media/hdinsight-machine-learning-overview/r-training.png)

Nó de extremidade de um cluster fornece um local conveniente para ligar ao cluster e para executar os scripts R.  Também tem a opção para executar R scripts em todos os nós do cluster através da utilização Hadoop mapeamento redução do ScaleR ou contextos de computação do Spark.

Com os serviços no HDInsight com o Spark ML, consegue paralelizar tão treinamento em todos os nós de um cluster através de um contexto de cálculo do Spark. Pode executar R scripts diretamente no nó de extremidade, com todos os núcleos disponíveis em paralelo, conforme necessário. Em alternativa, pode executar o código a partir do nó de extremidade para iniciar o processamento que é distribuído por todos os nós do cluster. Os serviços de ML no HDInsight com o Spark também permitem a paralelização de funções a partir de pacotes de R de código aberto, se assim o desejar.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e o Apache Hive

O Azure Machine Learning fornece ferramentas para Análise Preditiva do modelo, bem como um serviço totalmente gerido que pode utilizar para implementar os seus modelos preditivos como serviços web prontos a consumir. O Azure Machine Learning é uma solução completa de Análise Preditiva na cloud que pode utilizar para criar, testar, operacionalizar e gerir modelos de previsão. Selecionar a partir de uma grande biblioteca de algoritmos, utilize um studio baseado na web para a criação de modelos e implementar facilmente o seu modelo como um serviço web.

![Tomada de advanced analytics acessível ao Hadoop com o Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Criar características para dados num Hadoop do HDInsight cluster usando [consultas do Hive](../machine-learning/team-data-science-process/create-features-hive.md). *"Feature Engineering"* tenta aumentam a capacidade de previsão de algoritmos de aprendizagem através da criação de recursos de dados não processados que facilitam o processo de aprendizado. Pode executar consultas de HiveQL a partir do Azure Machine Learning studio e aceder a dados processados no Hive e armazenados no armazenamento de BLOBs, utilizando o [módulo importar dados](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Aprendizagem profunda](https://www.microsoft.com/en-us/research/group/dltc/) é um ramo do machine learning que utiliza as redes neurais, inspiradas pelos processos biológicos do cérebro humano. Pesquisadores de muitos aprendizagem profunda, consulte como uma abordagem promissor para aprimorar a inteligência artificial. Exemplos de aprendizagem profunda são conversores de um idioma falado, sistemas de reconhecimento de imagem e raciocínio de máquina.

Para ajudar a desenvolver a sua própria trabalho na aprendizagem profunda, a Microsoft desenvolveu o gratuito, fácil de usar, código-fonte aberto [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Esse Kit de ferramentas está a ser utilizado por uma grande variedade de produtos da Microsoft, as empresas em todo o mundo com a necessidade de implementar a aprendizagem profunda à escala e estudantes interessados nos algoritmos e as técnicas mais recentes.

## <a name="see-also"></a>Consulte também

### <a name="scenarios"></a>Cenários

* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Gerar recomendações de filmes com o Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive e do Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive e o Azure Machine Learning ponto-a-ponto](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine learning com o Apache Spark no HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Recursos de aprendizagem profunda

* [Utilizar o Microsoft Cognitive Toolkit modelo com o cluster do Azure HDInsight Spark de aprendizagem profunda](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Utilizar o Caffe para aprendizagem profunda distribuída no Azure HDInsight Spark](spark/apache-spark-deep-learning-caffe.md)
* [Estruturas de aprendizagem profunda e AI na máquina Virtual de ciência de dados (DSVM)](https://docs.microsoft.com/en-us/azure/machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks)
