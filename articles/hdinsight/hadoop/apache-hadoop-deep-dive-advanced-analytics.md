---
title: Mergulho profundo - análise avançada - Azure HDInsight
description: Saiba como a análise avançada utiliza algoritmos para processar grandes dados em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0780f66c981f0cebebc1ab327d783954753db965
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866731"
---
# <a name="deep-dive---advanced-analytics"></a>Mergulho profundo - análise avançada

## <a name="what-is-advanced-analytics-for-hdinsight"></a>O que é a análise avançada para HDInsight?

O HDInsight fornece a capacidade de obter informações valiosas a partir de grandes quantidades de dados estruturados, não estruturados e em movimento rápido. A análise avançada é o uso de arquiteturas altamente escaláveis, modelos estatísticos e de machine learning, e dashboards inteligentes para fornecer-lhe insights significativos. Machine learning, ou *análise preditiva,* usa algoritmos que identificam e aprendem com relacionamentos nos seus dados para fazer previsões e orientar as suas decisões.

## <a name="advanced-analytics-process"></a>Processo de análise avançada

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png" alt-text="Fluxo de processo de análise avançada" border="false":::

Depois de identificar o problema do negócio e ter começado a recolher e processar os seus dados, precisa de criar um modelo que represente a questão que pretende prever. O seu modelo utilizará um ou mais algoritmos de aprendizagem automática para fazer o tipo de previsão que melhor se adequa às necessidades do seu negócio.  A maioria dos seus dados deve ser usada para treinar o seu modelo, com os restantes utilizados para testá-lo ou avaliá-los.

Depois de criar, carregar, testar e avaliar o seu modelo, o próximo passo é implementar o seu modelo para que comece a fornecer respostas às suas perguntas. O último passo é monitorizar o desempenho do seu modelo e ajustá-lo conforme necessário.

## <a name="common-types-of-algorithms"></a>Tipos comuns de algoritmos

Soluções de análise avançadas fornecem um conjunto de algoritmos de aprendizagem automática. Aqui está um resumo das categorias de algoritmos e casos de uso de negócios comuns associados.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png" alt-text="Resumos da categoria de Machine Learning" border="false":::

Além de selecionar os algoritmos mais adequados, tem de considerar se precisa ou não fornecer dados para o treino. Os algoritmos de aprendizagem automática são categorizados da seguinte forma:

* Supervisionado - algoritmo precisa de ser treinado num conjunto de dados rotulados antes de poder fornecer resultados
* Semi-supervisionado - algoritmo pode ser aumentado por alvos extra através de consulta interativa por um treinador, que não estava disponível durante a fase inicial do treino
* Sem supervisão - algoritmo não requer dados de treino
* Reforço - algoritmo usa agentes de software para determinar o comportamento ideal dentro de um contexto específico (muitas vezes usado em robótica)

| Categoria algoritmo| Utilização | Tipo de Aprendizagem | Algoritmos |
| --- | --- | --- | -- |
| Classificação | Classificar pessoas ou coisas em grupos | Supervisionado | Árvores de decisão, regressão logística, redes neuronais |
| Clustering | Dividir um conjunto de exemplos em grupos homogéneos | Sem supervisão | K-significa agrupamento |
| Deteção de padrões | Identificar associações frequentes nos dados | Sem supervisão | Regras de associação |
| Regressão | Prever resultados numéricos | Supervisionado | Regressão linear, redes neurais |
| Reforço | Determine o comportamento ideal para robôs | Reforço | Simulações de Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Aprendizagem automática em HDInsight

O HDInsight tem várias opções de machine learning para um fluxo de trabalho de análise avançado:

* Machine Learning e Apache Spark
* Serviços R e ML
* Azure Machine Learning e Apache Hive
* Faísca Apache e Aprendizagem Profunda

### <a name="machine-learning-and-apache-spark"></a>Machine Learning e Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) é uma oferta azure-hospedada de [Apache Spark](https://spark.apache.org/), uma estrutura de processamento de dados unificada e aberta que usa o processamento de dados na memória para impulsionar a análise de Big Data. O motor de processamento de faíscas é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas na memória da Spark fazem com que seja uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e computações de gráficos.

Existem três bibliotecas de machine learning escaláveis que trazem capacidades de modelação algorítmica para este ambiente distribuído:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib contém a API original construída em cima de RDDs spark.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) - SparkML é um pacote mais recente que fornece uma API de nível superior construída em cima de Spark DataFrames para a construção de oleodutos ML.
* [**MMLSpark**](https://github.com/Azure/mmlspark)  - A biblioteca microsoft machine learning para Apache Spark (MMLSpark) foi projetada para tornar os cientistas de dados mais produtivos em Spark, aumentar a taxa de experimentação, e alavancar técnicas de aprendizagem automática de ponta, incluindo aprendizagem profunda, em conjuntos de dados muito grandes. A biblioteca MMLSpark simplifica tarefas comuns de modelação para modelos de construção em PySpark.

### <a name="r-and-ml-services"></a>Serviços R e ML

Como parte do HDInsight, pode criar um cluster HDInsight com [serviços ML](../r-server/r-server-overview.md) prontos para serem usados com conjuntos de dados e modelos maciços. Esta nova capacidade fornece aos cientistas de dados e estatísticos uma interface R familiar que pode escalar a pedido através do HDInsight, sem a sobrecarga de configuração e manutenção do cluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

[O Azure Machine Learning Studio (clássico)](https://studio.azureml.net/) fornece ferramentas para modelar análises preditivas, e um serviço totalmente gerido que pode usar para implementar os seus modelos preditivos como serviços web prontos a consumir. O Azure Machine Learning fornece ferramentas para criar soluções de análise preditivas completas na nuvem para criar, testar, operacionalizar e gerir modelos preditivos. Selecione de uma grande biblioteca de algoritmos, use um estúdio baseado na web para construir modelos e implemente facilmente o seu modelo como um serviço web.

### <a name="apache-spark-and-deep-learning"></a>Faísca Apache e Aprendizagem Profunda

[Deep learning](https://www.microsoft.com/research/group/dltc/) é um ramo de aprendizagem automática que usa *redes neuronais profundas* (DNNs), inspiradas nos processos biológicos do cérebro humano. Muitos investigadores vêem a aprendizagem profunda como uma abordagem promissora para a inteligência artificial. Alguns exemplos de aprendizagem profunda são tradutores de língua falada, sistemas de reconhecimento de imagem e raciocínio de máquinas. Para ajudar a progredir no seu próprio trabalho em deep learning, a Microsoft desenvolveu o kit de [ferramentas cognitivas](https://www.microsoft.com/en-us/cognitive-toolkit/)de código aberto gratuito e fácil de usar. O toolkit está a ser usado extensivamente por uma grande variedade de produtos da Microsoft, por empresas de todo o mundo com a necessidade de implementar aprendizagem profunda em escala, e por estudantes interessados nos algoritmos e técnicas mais recentes.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Cenário - Marcar imagens para identificar padrões no desenvolvimento urbano

Vamos rever um exemplo de um oleoduto de aprendizagem de máquinas de análise avançada usando HDInsight.

Neste cenário, você verá como os DNNs produzidos num quadro de aprendizagem profunda, o Cognitive Toolkit (CNTK) da Microsoft, pode ser operacionalizado para obter grandes coleções de imagem armazenadas numa conta de Armazenamento Azure Blob usando o PySpark num cluster HDInsight Spark. Esta abordagem é aplicada a um caso comum de utilização de DNN, classificação de imagem aérea, e pode ser usada para identificar padrões recentes no desenvolvimento urbano.  Vais usar um modelo de classificação de imagem pré-treinado. O modelo é pré-treinado no [conjunto de dados CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) e foi aplicado a 10.000 imagens retidas.

Existem três tarefas-chave neste avançado cenário de análise:

1. Crie um cluster Azure HDInsight Hadoop com uma distribuição Apache Spark 2.1.0.
2. Execute um script personalizado para instalar o Microsoft Cognitive Toolkit em todos os nós de um cluster Azure HDInsight Spark.
3. Faça o upload de um Pre-built Jupyter Notebook para o seu cluster HDInsight Spark spark para aplicar um modelo de aprendizagem profunda da Microsoft Cognitive Toolkit treinado para ficheiros numa Conta de Armazenamento Azure Blob utilizando a API Spark Python (PySpark).

Este exemplo utiliza o conjunto de imagens CIFAR-10 compilado e distribuído por Alex Krizhevsky, Vinod Nair e Geoffrey Hinton. O conjunto de dados CIFAR-10 contém 60.000 imagens a cores 32×32 pertencentes a 10 classes mutuamente exclusivas:

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png" alt-text="Imagens de exemplo de aprendizagem automática" border="false":::

Para obter mais informações sobre o conjunto de dados, consulte [As Camadas Múltiplas de Funcionalidades de Alex](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf)Krizhevsky de Tiny Images .

O conjunto de dados foi dividido num conjunto de treino de 50.000 imagens e num conjunto de testes de 10.000 imagens. O primeiro conjunto foi usado para treinar um modelo de rede residual convolucional de 20 camadas (ResNet) usando o Microsoft Cognitive Toolkit seguindo [este tutorial](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) do repositório de Ferramentas Cognitivas GitHub. As restantes 10.000 imagens foram usadas para testar a precisão do modelo. É aqui que entra a computação distribuída: a tarefa de pré-processamento e pontuação das imagens é altamente paralestaizável. Com o modelo treinado guardado na mão, usamos:

* PySpark para distribuir as imagens e modelo treinado para os nós operários do cluster.
* Python para pré-processar as imagens em cada nó do cluster HDInsight Spark.
* Cognitive Toolkit para carregar o modelo e marcar as imagens pré-processadas em cada nó.
* Jupyter Notebooks para executar o script PySpark, agregar os resultados, e usar [Matplotlib](https://matplotlib.org/) para visualizar o desempenho do modelo.

Todo o pré-processamento/pontuação das 10.000 imagens demora menos de um minuto num aglomerado com 4 nós de trabalhadores. O modelo prevê com precisão os rótulos de ~9.100 (91%) imagens. Uma matriz de confusão ilustra os erros de classificação mais comuns. Por exemplo, a matriz mostra que a rotulagem errada dos cães como gatos e vice-versa ocorre mais frequentemente do que para outros pares de rótulos.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png" alt-text="Gráfico de resultados de Machine Learning" border="false":::

### <a name="try-it-out"></a>Experimente!

Siga [este tutorial](../spark/apache-spark-microsoft-cognitive-toolkit.md) para implementar esta solução de ponta a ponta: crie um cluster HDInsight Spark, instale o Cognitive Toolkit e execute o Jupyter Notebook que marca 10.000 imagens CIFAR.

## <a name="next-steps"></a>Passos seguintes

Apache Hive e Azure Machine Learning

* [Apache Hive e Azure Machine Learning de ponta a ponta](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Usando um cluster hadoop Azure HDInsight num conjunto de dados de 1-TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Faísca Apache e MLLib

* [Aprendizagem automática com Apache Spark em HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Faísca Apache com Machine Learning: Use Apache Spark em HDInsight para analisar a temperatura do edifício usando dados de AVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use Apache Spark em HDInsight para prever os resultados da inspeção alimentar](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Cognitive Toolkit, e outros

* [Data Science Azure Virtual Machine](../../machine-learning/data-science-virtual-machine/overview.md)
* [Apresentando H2O.ai no Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
