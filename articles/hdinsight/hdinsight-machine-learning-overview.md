---
title: Visão geral da aprendizagem automática - Azure HDInsight
description: Visão geral das opções de aprendizagem automática de big data para clusters em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 6c7f50e4ba3803b5cb929b75f5ad1127f7f063d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75931670"
---
# <a name="machine-learning-on-hdinsight"></a>Aprendizagem automática em HDInsight

O HDInsight permite machine learning com big data, fornecendo a capacidade de obter informações valiosas a partir de grandes quantidades (petabytes, ou mesmo exabytes) de dados estruturados, desestruturados e em movimento rápido. Existem várias opções de machine learning no HDInsight: SparkML e Apache Spark MLlib, R, Apache Hive e o Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML e MLlib

[HDInsight Spark](spark/apache-spark-overview.md) é uma oferta hospedada pelo Azure de [Apache Spark](https://spark.apache.org/), uma estrutura unificada, de código aberto, de processamento de dados paralelo que suporta o processamento na memória para impulsionar a análise de big data. O motor de processamento Spark é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas pela Spark fazem dele uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e nas computações de gráficos. Existem duas bibliotecas escaláveis de aprendizagem automática que trazem capacidades de modelação algorítmica para este ambiente distribuído: MLlib e SparkML. MLlib contém a API original construída em cima de RDDs. SparkML é um pacote mais recente que fornece um API de nível mais alto construído em cima de DataFrames para a construção de oleodutos ML. A SparkML ainda não suporta todas as funcionalidades do MLlib, mas está a substituir a MLlib como biblioteca padrão de aprendizagem automática da Spark.

A biblioteca microsoft machine learning para Apache Spark é [MMLSpark](https://github.com/Azure/mmlspark). Esta biblioteca foi concebida para tornar os cientistas de dados mais produtivos em Spark, aumentar a taxa de experimentação e alavancar técnicas de aprendizagem automática de ponta, incluindo aprendizagem profunda, em conjuntos de dados muito grandes. O MMLSpark fornece uma camada em cima das APIs de baixo nível da SparkML ao construir modelos ML escaláveis, tais como cadeias de indexação, coagir dados num layout esperado por algoritmos de aprendizagem automática e montagem de vetores de recursos. A biblioteca MMLSpark simplifica estas e outras tarefas comuns para a construção de modelos em PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) é atualmente a linguagem de programação estatística mais popular do mundo. É uma ferramenta de visualização de dados de código aberto com uma comunidade de mais de 2,5 milhões de utilizadores e em crescimento. Com a sua próspera base de utilizadores, e mais de 8.000 pacotes contribuídos, r é uma escolha provável para muitas empresas que precisam de machine learning. Pode criar um cluster HDInsight com serviços ML prontos a ser utilizados com conjuntos e modelos maciços. Esta capacidade fornece aos cientistas de dados e estatísticos uma interface R familiar que pode escalar a procura através do HDInsight, sem a sobrecarga da configuração e manutenção do cluster.

![Treino para previsão com servidor R](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

O nó de borda de um cluster fornece um lugar conveniente para ligar ao cluster e executar os seus scripts R.  Também pode executar scripts R através dos nós do cluster utilizando os contextos de cálculo de Hadoop Map Reduce ou Spark da ScaleR.

Com os Serviços ML no HDInsight com spark, pode paralelamente a formação através dos nós de um cluster utilizando um contexto de computação Spark. Pode executar scripts R diretamente no nó da borda, utilizando todos os núcleos disponíveis em paralelo, conforme necessário. Alternadamente, pode executar o seu código a partir do nó de borda para iniciar o processamento que é distribuído por todos os nós do cluster. Os serviços ML no HDInsight com a Spark também permitem paralelizar funções a partir de pacotes R de código aberto, se desejar.

## <a name="azure-machine-learning-and-apache-hive"></a>Aprendizagem automática azure e colmeia Apache

O Azure Machine Learning fornece ferramentas para modelar análises preditivas, e um serviço totalmente gerido que pode utilizar para implementar os seus modelos preditivos como serviços web prontos a consumir. O Azure Machine Learning é uma solução de análise preditiva completa na nuvem que pode usar para criar, testar, operacionalizar e gerir modelos preditivos. Selecione a partir de uma grande biblioteca de algoritmos, use um estúdio baseado na Web para construir modelos e facilmente implemente o seu modelo como um serviço web.

![Visão geral da aprendizagem automática do Microsoft Azure](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Crie funcionalidades para dados num cluster Hadoop HDInsight utilizando consultas de [Colmeia](../machine-learning/team-data-science-process/create-features-hive.md). *A engenharia* de recursos tenta aumentar o poder preditivo dos algoritmos de aprendizagem, criando funcionalidades a partir de dados brutos que facilitam o processo de aprendizagem. Você pode executar consultas hiveQL do Azure Machine Learning Studio (clássico), e aceder a dados processados na Colmeia e armazenados em armazenamento blob, usando o [módulo de dados](../machine-learning/studio/import-data.md)de importação .

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[A aprendizagem profunda](https://www.microsoft.com/en-us/research/group/dltc/) é um ramo da aprendizagem automática que usa redes neurais, inspirada nos processos biológicos do cérebro humano. Muitos investigadores vêem a aprendizagem profunda como uma abordagem promissora para melhorar a inteligência artificial. Exemplos de aprendizagem profunda são tradutores de línguafala, sistemas de reconhecimento de imagem e raciocínio de máquinas.

Para ajudar a progredir no seu próprio trabalho em deep learning, a Microsoft desenvolveu o kit de [ferramentas cognitivas](https://www.microsoft.com/en-us/cognitive-toolkit/)gratuito, fácil de usar e de código aberto da Microsoft . Este kit de ferramentas está a ser usado por uma grande variedade de produtos da Microsoft, por empresas de todo o mundo com a necessidade de implementar aprendizagem profunda em escala, e por estudantes interessados nos algoritmos e técnicas mais recentes.

## <a name="see-also"></a>Consulte também

### <a name="scenarios"></a>Cenários

* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Gerar recomendações de filme com Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive e Aprendizagem automática azure](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive e Azure Machine Learning de ponta a ponta](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Aprendizagem automática com Apache Spark no HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Recursos de aprendizagem profunda

* [Use o modelo de aprendizagem profunda do Microsoft Cognitive Toolkit com o cluster Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Deep Learning e quadros de IA na Máquina Virtual da Ciência dos Dados (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
