---
title: Visão geral da aprendizagem automática - Azure HDInsight
description: Visão geral das opções de aprendizagem de máquinas de big data para clusters em Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 492663ca35ed04da09e7f0d198444a4d40178e2b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867530"
---
# <a name="machine-learning-on-hdinsight"></a>Aprendizagem automática em HDInsight

O HDInsight permite a aprendizagem automática com big data, proporcionando a capacidade de obter informações valiosas a partir de grandes quantidades (petabytes, ou mesmo exabytes) de dados estruturados, não estruturados e em movimento rápido. Existem várias opções de machine learning em HDInsight: SparkML e Apache Spark MLlib, R, Apache Hive e o Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML e MLlib

[HDInsight Spark](spark/apache-spark-overview.md) é uma oferta azure-hospedada de [Apache Spark](https://spark.apache.org/), uma estrutura unificada e aberta de processamento de dados que suporta o processamento na memória para impulsionar a análise de big data. O motor de processamento de faíscas é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas na memória da Spark fazem com que seja uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e computações de gráficos. Existem duas bibliotecas de machine learning escaláveis que trazem capacidades de modelação algorítmica para este ambiente distribuído: MLlib e SparkML. MLlib contém a API original construída em cima de RDDs. SparkML é um pacote mais recente que fornece uma API de nível superior construída em cima de DataFrames para a construção de oleodutos ML. A SparkML ainda não suporta todas as funcionalidades da MLlib, mas está a substituir a MLlib como a biblioteca padrão de machine learning da Spark.

A biblioteca microsoft machine learning para Apache Spark é [MMLSpark](https://github.com/Azure/mmlspark). Esta biblioteca é projetada para tornar os cientistas de dados mais produtivos em Spark, aumentar a taxa de experimentação, e alavancar técnicas de aprendizagem automática de ponta, incluindo aprendizagem profunda, em conjuntos de dados muito grandes. O MMLSpark fornece uma camada em cima das APIs de baixo nível da SparkML ao construir modelos ML escaláveis, tais como cadeias de indexação, coagir dados em um layout esperado por algoritmos de aprendizagem automática, e montagem de vetores de recursos. A biblioteca MMLSpark simplifica estas e outras tarefas comuns para a construção de modelos em PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) é atualmente a linguagem de programação estatística mais popular do mundo. É uma ferramenta de visualização de dados de código aberto com uma comunidade de mais de 2,5 milhões de utilizadores e crescendo. Com a sua próspera base de utilizadores, e mais de 8.000 pacotes contribuídos, R é uma escolha provável para muitas empresas que precisam de aprendizagem automática. Pode criar um cluster HDInsight com serviços ML prontos para serem usados com conjuntos de dados e modelos maciços. Esta capacidade fornece aos cientistas de dados e estatísticos uma interface R familiar que pode escalar a pedido através do HDInsight, sem a sobrecarga de configuração e manutenção do cluster.

:::image type="content" source="./media/hdinsight-machine-learning-overview/training-for-prediction.png" alt-text="Treino para previsão com servidor R" border="false":::

O nó de borda de um cluster fornece um lugar conveniente para se conectar ao cluster e executar os seus scripts R.  Também pode executar scripts R através dos nós do cluster, utilizando os contextos de cálculo do Mapa hadoop da ScaleR ou do spark.

Com serviços ML em HDInsight com Spark, você pode paralelizar a formação através dos nós de um cluster usando um contexto de computação Spark. Pode executar scripts R diretamente no nó de borda, usando todos os núcleos disponíveis em paralelo, conforme necessário. Alternadamente, pode executar o seu código a partir do nó de borda para iniciar o processamento que é distribuído por todos os nós do cluster. Os serviços ML em HDInsight com Spark também permitem paralelizar funções a partir de pacotes R de código aberto, se desejar.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

O Azure Machine Learning fornece ferramentas para modelar análises preditivas e um serviço totalmente gerido que pode usar para implementar os seus modelos preditivos como serviços web prontos a consumir. A Azure Machine Learning é uma solução de análise preditiva completa na nuvem que pode usar para criar, testar, operacionalizar e gerir modelos preditivos. Selecione de uma grande biblioteca de algoritmos, use um estúdio baseado na web para construir modelos e implemente facilmente o seu modelo como um serviço web.

:::image type="content" source="./media/hdinsight-machine-learning-overview/azure-machine-learning.png" alt-text="Visão geral da aprendizagem da máquina do Microsoft Azure" border="false":::

Crie funcionalidades para dados num cluster hdInsight Hadoop utilizando [consultas de Hive](../machine-learning/team-data-science-process/create-features-hive.md). *A engenharia de recursos* tenta aumentar o poder preditivo dos algoritmos de aprendizagem criando funcionalidades a partir de dados brutos que facilitam o processo de aprendizagem. Você pode executar consultas de HiveQL a partir do Azure Machine Learning Studio (clássico), e aceder a dados processados na Colmeia e armazenados em armazenamento de bolhas, utilizando o [módulo de Dados de Importação.](../machine-learning/classic/import-data.md)

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Deep learning](https://www.microsoft.com/en-us/research/group/dltc/) é um ramo de aprendizagem automática que usa redes neurais, inspiradas nos processos biológicos do cérebro humano. Muitos investigadores vêem a aprendizagem profunda como uma abordagem promissora para melhorar a inteligência artificial. Exemplos de aprendizagem profunda são tradutores de língua falada, sistemas de reconhecimento de imagem e raciocínio de máquinas.

Para ajudar a progredir no seu próprio trabalho em deep learning, a Microsoft desenvolveu o kit de [ferramentas cognitivas](https://www.microsoft.com/en-us/cognitive-toolkit/)de código aberto gratuito e fácil de usar. Este conjunto de ferramentas está a ser utilizado por uma grande variedade de produtos da Microsoft, por empresas de todo o mundo com a necessidade de implementar aprendizagem profunda em escala, e por estudantes interessados nos mais recentes algoritmos e técnicas.

## <a name="see-also"></a>Ver também

### <a name="scenarios"></a>Cenários

* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Gerar recomendações de filme com Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive e Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive e Azure Machine Learning de ponta a ponta](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Aprendizagem automática com Apache Spark em HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Recursos de aprendizagem profunda

* [Use o modelo de aprendizagem profunda do Microsoft Cognitive Toolkit com cluster Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Deep Learning e IA frameworks on the Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
