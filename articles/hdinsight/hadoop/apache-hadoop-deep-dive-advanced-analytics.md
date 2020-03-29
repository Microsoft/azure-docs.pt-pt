---
title: Mergulho profundo - análise avançada - Azure HDInsight
description: Saiba como a análise avançada utiliza algoritmos para processar big data no Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 6fc6ea9ca0ce3bf82f027b964db94df50f07f2bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646528"
---
# <a name="deep-dive---advanced-analytics"></a>Mergulho profundo - análise avançada

## <a name="what-is-advanced-analytics-for-hdinsight"></a>O que é aanálise avançada para o HDInsight?

O HDInsight fornece a capacidade de obter informações valiosas a partir de grandes quantidades de dados estruturados, não estruturados e em movimento rápido. Análise avançada é o uso de arquiteturas altamente escaláveis, modelos estatísticos e de aprendizagem automática, e dashboards inteligentes para lhe fornecer insights significativos. Machine learning, ou *análise preditiva,* usa algoritmos que identificam e aprendem com relações nos seus dados para fazer previsões e orientar as suas decisões.

## <a name="advanced-analytics-process"></a>Processo de análise avançada

![Fluxo avançado de processo de análise](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Depois de identificar o problema do negócio e ter começado a recolher e processar os seus dados, precisa de criar um modelo que represente a questão que pretende prever. O seu modelo utilizará um ou mais algoritmos de aprendizagem automática para fazer o tipo de previsão que melhor se adequa às necessidades do seu negócio.  A maioria dos seus dados deve ser usado para treinar o seu modelo, com os restantes utilizados para testar ou avaliar.

Depois de criar, carregar, testar e avaliar o seu modelo, o próximo passo é implementar o seu modelo para que comece a fornecer respostas às suas perguntas. O último passo é monitorizar o desempenho do seu modelo e ajustá-lo conforme necessário.

## <a name="common-types-of-algorithms"></a>Tipos comuns de algoritmos

Soluções avançadas de análise fornecem um conjunto de algoritmos de aprendizagem automática. Aqui está um resumo das categorias de algoritmos e casos de uso de negócios comuns associados.

![Resumos da categoria machine learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

Juntamente com a seleção do algoritmo(s) mais adequado, você precisa considerar se você precisa ou não fornecer dados para o treino. Os algoritmos de aprendizagem automática são categorizados da seguinte forma:

* Supervisionado - algoritmo precisa de ser treinado num conjunto de dados rotulados antes de poder fornecer resultados
* Semi-supervisionado - algoritmo pode ser aumentado por alvos extra através de consulta interativa por um treinador, que não estava disponível durante a fase inicial do treino
* Sem supervisão - algoritmo não requer dados de treino
* Reforço - algoritmo usa agentes de software para determinar o comportamento ideal dentro de um contexto específico (frequentemente usado em robótica)

| Categoria algoritmo| Utilizar | Tipo de Aprendizagem | Algoritmos |
| --- | --- | --- | -- |
| Classificação | Classificar pessoas ou coisas em grupos | Supervisionado | Árvores de decisão, regressão logística, redes neurais |
| Clustering | Dividir um conjunto de exemplos em grupos homogéneos | Sem supervisão | Agrupamento de k significa |
| Deteção de padrões | Identificar associações frequentes nos dados | Sem supervisão | Regras de associação |
| Regressão | Prever resultados numéricos | Supervisionado | Regressão linear, redes neurais |
| Reforço | Determine o comportamento ideal para robôs | Reforço | Simulações de Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Aprendizagem automática em HDInsight

O HDInsight tem várias opções de machine learning para um fluxo de trabalho de análise avançada:

* Machine Learning e Apache Spark
* Serviços R e ML
* Aprendizagem automática azure e colmeia Apache
* Apache Spark e Aprendizagem Profunda

### <a name="machine-learning-and-apache-spark"></a>Machine Learning e Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) é uma oferta hospedada pelo Azure de [Apache Spark](https://spark.apache.org/), uma estrutura unificada, de código aberto, de processamento de dados paralelo que utiliza o processamento na memória para impulsionar a análise do Big Data. O motor de processamento Spark é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas pela Spark fazem dele uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e nas computações de gráficos.

Existem três bibliotecas escaláveis de aprendizagem automática que trazem capacidades de modelação algorítmica para este ambiente distribuído:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib contém a API original construída em cima de RDDs spark.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) - SparkML é um pacote mais recente que fornece um API de nível mais alto construído em cima de Spark DataFrames para a construção de oleodutos ML.
* [**MMLSpark**](https://github.com/Azure/mmlspark) - A biblioteca de Machine Learning da Microsoft para Apache Spark (MMLSpark) foi concebida para tornar os cientistas de dados mais produtivos em Spark, para aumentar a taxa de experimentação, e para alavancar técnicas de aprendizagem automática de ponta, incluindo aprendizagem profunda, em conjuntos de dados muito grandes. A biblioteca MMLSpark simplifica tarefas comuns de modelação para modelos de construção em PySpark.

### <a name="r-and-ml-services"></a>Serviços R e ML

Como parte do HDInsight, pode criar um cluster HDInsight com [serviços ML prontos](../r-server/r-server-overview.md) a ser usados com conjuntos e modelos maciços. Esta nova capacidade fornece aos cientistas de dados e estatísticos uma interface R familiar que pode escalar a procura através do HDInsight, sem a sobrecarga da configuração e manutenção do cluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Aprendizagem automática azure e colmeia Apache

[O Azure Machine Learning Studio (clássico)](https://studio.azureml.net/) fornece ferramentas para modelar análises preditivas, e um serviço totalmente gerido pode usar para implementar os seus modelos preditivos como serviços web prontos a consumir. O Azure Machine Learning fornece ferramentas para criar soluções de análise preditiva completas na nuvem para criar, testar, operacionalizar e gerir modelos preditivos. Selecione a partir de uma grande biblioteca de algoritmos, use um estúdio baseado na Web para construir modelos e facilmente implemente o seu modelo como um serviço web.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark e Aprendizagem Profunda

[A aprendizagem profunda](https://www.microsoft.com/research/group/dltc/) é um ramo da aprendizagem automática que usa *redes neuronais profundas* (DNNs), inspirada nos processos biológicos do cérebro humano. Muitos investigadores vêem a aprendizagem profunda como uma abordagem promissora para a inteligência artificial. Alguns exemplos de aprendizagem profunda são tradutores de línguafala falada, sistemas de reconhecimento de imagem e raciocínio de máquinas. Para ajudar a progredir no seu próprio trabalho em deep learning, a Microsoft desenvolveu o kit de [ferramentas cognitivas](https://www.microsoft.com/en-us/cognitive-toolkit/)gratuito, fácil de usar e de código aberto da Microsoft . O toolkit está a ser usado extensivamente por uma grande variedade de produtos da Microsoft, por empresas de todo o mundo com a necessidade de implementar aprendizagem profunda em escala, e por estudantes interessados nos algoritmos e técnicas mais recentes.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Cenário - Pontuar imagens para identificar padrões no desenvolvimento urbano

Vamos rever um exemplo de um pipeline avançado de aprendizagem automática de análise usando o HDInsight.

Neste cenário, você verá como os DNNs produzidos em um quadro de aprendizagem profunda, o Cognitive Toolkit (CNTK) da Microsoft, pode ser operacionalizado para a pontuação de grandes coleções de imagem armazenadas numa conta de Armazenamento Azure Blob usando pySpark em um cluster HDInsight Spark. Esta abordagem é aplicada a um caso comum de uso dNN, classificação de imagem aérea, e pode ser usada para identificar padrões recentes no desenvolvimento urbano.  Usará um modelo de classificação de imagem pré-treinado. O modelo é pré-treinado no conjunto de [dados CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) e foi aplicado a 10.000 imagens retidas.

Existem três tarefas-chave neste cenário de análise avançada:

1. Crie um cluster Hadoop Azure HDInsight com uma distribuição Apache Spark 2.1.0.
2. Execute um script personalizado para instalar o Microsoft Cognitive Toolkit em todos os nós de um cluster De Spark Azure HDInsight.
3. Faça upload de um caderno Jupyter pré-construído para o seu cluster HDInsight Spark para aplicar um modelo de aprendizagem profunda de ferramentas cognitivas da Microsoft para ficheiros numa Conta de Armazenamento Azure Blob utilizando a Spark Python API (PySpark).

Este exemplo utiliza o conjunto de imagens CIFAR-10 compilado e distribuído por Alex Krizhevsky, Vinod Nair e Geoffrey Hinton. O conjunto de dados CIFAR-10 contém 60.000 imagens de cores 32×32 pertencentes a 10 classes mutuamente exclusivas:

![Imagens de exemplo de aprendizagem automática](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Para obter mais informações sobre o conjunto de dados, consulte A [Aprendizagem de Várias Camadas de Funcionalidades de Alex](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf)Krizhevsky a partir de Imagens Minúsculas .

O conjunto de dados foi dividido num conjunto de treino de 50.000 imagens e um conjunto de testes de 10.000 imagens. O primeiro conjunto foi usado para treinar um modelo de rede residual convolucional de vinte camadas de profundidade (ResNet) usando o Microsoft Cognitive Toolkit seguindo [este tutorial](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) do repositório Cognitive Toolkit GitHub. As restantes 10.000 imagens foram usadas para testar a precisão do modelo. É aqui que entra a computação distribuída: a tarefa de pré-processamento e pontuação das imagens é altamente paralela. Com o modelo treinado guardado na mão, usamos:

* PySpark para distribuir as imagens e modelo treinado para os nós operários do cluster.
* Python para pré-processar as imagens em cada nó do cluster HDInsight Spark.
* Toolkit cognitivo para carregar o modelo e marcar as imagens pré-processadas em cada nó.
* Jupyter Notebooks para executar o script PySpark, agregar os resultados, e usar [Matplotlib](https://matplotlib.org/) para visualizar o desempenho do modelo.

Todo o pré-processamento/pontuação das 10.000 imagens demora menos de um minuto num aglomerado com 4 nós de trabalhador. O modelo prevê com precisão os rótulos de ~9.100 (91%) imagens. Uma matriz de confusão ilustra os erros de classificação mais comuns. Por exemplo, a matriz mostra que rotular mal os cães como gatos e vice-versa ocorre com mais frequência do que para outros pares de etiquetas.

![Gráfico de resultados de Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Experimente!

Siga [este tutorial](../spark/apache-spark-microsoft-cognitive-toolkit.md) para implementar esta solução de ponta a ponta: instale um cluster HDInsight Spark, instale o Cognitive Toolkit e execute o Jupyter Notebook que marca 10.000 imagens CIFAR.

## <a name="next-steps"></a>Passos seguintes

Apache Hive e Aprendizagem automática azure

* [Apache Hive e Azure Machine Learning de ponta a ponta](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Utilizando um cluster de hadoop Azure HDInsight num conjunto de dados de 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark e MLLib

* [Aprendizagem automática com Apache Spark no HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark com Machine Learning: Use Apache Spark em HDInsight para analisar a temperatura do edifício usando dados de AVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Apache Spark no HDInsight para prever resultados da inspeção alimentar](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Cognitive Toolkit, entre outros

* [Máquina Virtual Data Science Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introdução H2O.ai no Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
