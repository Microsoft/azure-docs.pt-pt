---
title: Aprofundamento – análise avançada – Azure HDInsight
description: Saiba como a análise avançada usa algoritmos para processar Big Data no Azure HDInsight.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 882c52995d788b0cd65c71a935ab795a7e7d2f53
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882391"
---
# <a name="deep-dive---advanced-analytics"></a>Aprofundamento – análise avançada

## <a name="what-is-advanced-analytics-for-hdinsight"></a>O que é análise avançada para o HDInsight?

O HDInsight fornece a capacidade de obter informações valiosas de grandes quantidades de dados estruturados, não estruturados e de movimentação rápida. A análise avançada é o uso de arquiteturas altamente escalonáveis, modelos estatísticos e de aprendizado de máquina e painéis inteligentes para fornecer informações significativas. O Machine Learning, ou *análise preditiva*, usa algoritmos que identificam e aprendem com relações em seus dados para fazer previsões e orientar suas decisões.

## <a name="advanced-analytics-process"></a>Processo de análise avançada

![Processo](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Depois de identificar o problema de negócios e começar a coletar e processar seus dados, você precisará criar um modelo que represente a pergunta que deseja prever. Seu modelo usará um ou mais algoritmos de aprendizado de máquina para fazer o tipo de previsão que melhor se adapta às suas necessidades de negócios.  A maioria dos seus dados deve ser usada para treinar seu modelo, com o restante usado para testá-lo ou avaliá-lo. 

Depois de criar, carregar, testar e avaliar seu modelo, a próxima etapa é implantar seu modelo para que ele comece a fornecer respostas às suas perguntas. A última etapa é monitorar o desempenho do modelo e ajustá-lo conforme necessário. 

## <a name="common-types-of-algorithms"></a>Tipos comuns de algoritmos

As soluções de análise avançada fornecem um conjunto de algoritmos de aprendizado de máquina. Aqui está um resumo das categorias de algoritmos e casos de uso de negócios comuns associados.

![Machine Learning casos de uso](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Juntamente com a seleção dos algoritmos de melhor ajuste, você precisa considerar se precisa ou não fornecer dados para treinamento. Os algoritmos de aprendizado de máquina são categorizados da seguinte maneira:

* Supervisionado-o algoritmo precisa ser treinado em um conjunto de dados rotulados antes que possa fornecer resultados
* Semisupervisionado-o algoritmo pode ser aumentado por destinos extras por meio de consulta interativa por um instrutor, que não estava disponível durante o estágio inicial de treinamento
* Não supervisionado-o algoritmo não requer dados de treinamento 
* Reforço-o algoritmo usa agentes de software para determinar o comportamento ideal em um contexto específico (geralmente usado em robótica)


| Categoria de algoritmo| Utilizar | Tipo de aprendizagem | Algoritmos |
| --- | --- | --- | -- |
| Classificação | Classificar pessoas ou coisas em grupos | Supervisionado | Árvores de decisão, regressão logística, redes neurais |
| Clustering | Dividindo um conjunto de exemplos em grupos homogêneos | Sem supervisão | Clustering de K-means |
| Detecção de padrões | Identificar associações frequentes nos dados | Sem supervisão | Regras de associação |
| Regressão | Prever resultados numéricos | Supervisionado | Regressão linear, redes neurais |
| Reforço | Determinar comportamento ideal para robôs | Reforço | Simulações do Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Aprendizado de máquina no HDInsight

O HDInsight tem várias opções de aprendizado de máquina para um fluxo de trabalho de análise avançada:

* Machine Learning e Apache Spark
* Serviços R e ML
* Azure Machine Learning e Apache Hive
* Apache Spark e aprendizado profundo

### <a name="machine-learning-and-apache-spark"></a>Machine Learning e Apache Spark


O [HDInsight Spark](../spark/apache-spark-overview.md) é uma oferta de [Apache Spark](https://spark.apache.org/)hospedada pelo Azure, uma estrutura de processamento de dados paralela, unificada e de código aberto que usa o processamento na memória para impulsionar a análise de Big Data. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação distribuída de dentro da memória do Spark tornam uma boa opção para algoritmos iterativos, utilizada em cálculos de machine learning e do graph. 


Há três bibliotecas de aprendizado de máquina escalonáveis que trazem recursos de modelagem de algoritmos para esse ambiente distribuído:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib contém a API original criada com base no Spark RDDs.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML é um pacote mais recente que fornece uma API de nível superior criada com base no Spark dataframes para construir pipelines de ml.
* [**MMLSpark**](https://github.com/Azure/mmlspark) -a MMLSpark (Microsoft Machine Learning library for Apache Spark) foi projetada para tornar os cientistas de dados mais produtivos no Spark, aumentar a taxa de experimentação e aproveitar as técnicas de aprendizado de máquina de ponta, incluindo aprendizado profundo, em conjuntos de grandes demais. A biblioteca MMLSpark simplifica tarefas comuns de modelagem para a criação de modelos no PySpark. 

### <a name="r-and-ml-services"></a>Serviços R e ML

Como parte do HDInsight, você pode criar um cluster HDInsight com os [serviços de ml](../r-server/r-server-overview.md) prontos para serem usados com grandes conjuntos de volumes e modelos. Essa nova funcionalidade fornece cientistas de dados e estatísticos com uma interface de R familiar que pode ser dimensionada sob demanda por meio do HDInsight, sem a sobrecarga de configuração e manutenção do cluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

O [Azure Machine Learning Studio](https://studio.azureml.net/) fornece ferramentas para modelar análises preditivas, bem como um serviço totalmente gerenciado que você pode usar para implantar seus modelos de previsão como serviços Web prontos para consumo. O Azure Machine Learning fornece ferramentas para criar soluções completas de análise preditiva na nuvem para criar, testar, colocar em operação e gerenciar modelos de previsão rapidamente. Selecione em uma biblioteca de algoritmos grande, use um estúdio baseado na Web para criar modelos e implante facilmente seu modelo como um serviço Web.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark e aprendizado profundo

O [aprendizado profundo](https://www.microsoft.com/research/group/dltc/) é uma ramificação do aprendizado de máquina que usa *redes neurais profundas* (DNNs), inspiradas pelos processos biológicos do cérebro humano. Muitos pesquisadores veem o aprendizado profundo como uma abordagem promissora para inteligência artificial. Alguns exemplos de aprendizado profundo são tradutores de linguagem falada, sistemas de reconhecimento de imagem e raciocínio de máquina. Para ajudar a avançar seu próprio trabalho no aprendizado profundo, a Microsoft desenvolveu o [Microsoft cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)de código aberto gratuito, fácil de usar. O kit de ferramentas está sendo usado extensivamente por uma ampla variedade de produtos da Microsoft, por empresas em todo o mundo, com uma necessidade de implantar o aprendizado profundo em escala e por alunos interessados nos algoritmos e técnicas mais recentes. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Cenário-imagens de Pontuação para identificar padrões no desenvolvimento urbano

Vamos examinar um exemplo de um pipeline de aprendizado de máquina de análise avançada usando o HDInsight.

Neste cenário, você verá como o DNNs produzido em uma estrutura de aprendizado profundo, o Microsoft Cognitive Toolkit (CNTK), pode ser operacional para a pontuação de coleções de imagens grandes armazenadas em uma conta de armazenamento de BLOBs do Azure usando PySpark em um cluster HDInsight Spark. Essa abordagem é aplicada a um caso de uso comum de DNN, classificação de imagem aérea e pode ser usada para identificar padrões recentes no desenvolvimento urbano.  Você usará um modelo de classificação de imagem pré-treinado. O modelo é previamente treinado no conjunto de [CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) e foi aplicado a imagens retidas 10.000.

Há três tarefas principais neste cenário de análise avançada:

1. Crie um cluster Azure HDInsight Hadoop com uma distribuição 2.1.0 Apache Spark. 
2. Execute um script personalizado para instalar Microsoft Cognitive Toolkit em todos os nós de um cluster Azure HDInsight Spark. 
3. Carregue um bloco de anotações Jupyter predefinido em seu cluster HDInsight Spark para aplicar um modelo de aprendizado detalhado treinado Microsoft Cognitive Toolkit a arquivos em uma conta de armazenamento de BLOBs do Azure usando a API Python do Spark (PySpark). 

Este exemplo usa o conjunto de imagens CIFAR-10 compilado e distribuído por Alex Krizhevsky, Vinod Nair e Geoffrey Hinton. O conjunto de CIFAR-10 contém 60.000 32 × 32 imagens coloridas que pertencem a 10 classes mutuamente exclusivas:

![Imagens](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Para obter mais detalhes sobre o conjunto de informações, confira Alex Krizhevsky [aprendendo várias camadas de recursos de pequenas imagens](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

O conjunto de testes foi particionado em um conjunto de treinamento de 50.000 imagens e um conjunto de teste de 10.000 imagens. O primeiro conjunto foi usado para treinar um modelo de ResNet (rede residual de convolução) de vinte camadas usando Microsoft Cognitive Toolkit seguindo [este tutorial](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) do cognitive Toolkit repositório github. As 10.000 imagens restantes foram usadas para testar a precisão do modelo. É aí que a computação distribuída entra em cena: a tarefa de pré-processamento e Pontuação das imagens é altamente paralelizáveis. Com o modelo treinado salvo em mãos, usamos:

* PySpark para distribuir as imagens e o modelo treinado para os nós de trabalho do cluster.
* Python para pré-processar as imagens em cada nó do cluster HDInsight Spark.
* Cognitive Toolkit carregar o modelo e pontuar as imagens previamente processadas em cada nó.
* Jupyter notebooks para executar o script PySpark, agregar os resultados e usar [matplotlib](https://matplotlib.org/) para visualizar o desempenho do modelo.

Todo o pré-processamento/Pontuação das imagens 10.000 leva menos de um minuto em um cluster com 4 nós de trabalho. O modelo prevê com precisão os rótulos de ~ 9.100 (91%) imagens. Uma matriz de confusão ilustra os erros de classificação mais comuns. Por exemplo, a matriz mostra que a rotulagem inverso de cachorros como gatos e vice-versa ocorre com mais frequência do que para outros pares de rótulo.

![Resultados](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Experimente!

Siga [este tutorial](../spark/apache-spark-microsoft-cognitive-toolkit.md) para implementar essa solução de ponta a ponta: configurar um cluster HDInsight Spark, instalar cognitive Toolkit e executar os Jupyter notebook que pontuam 10.000 imagens CIFAR.

## <a name="next-steps"></a>Passos Seguintes

Apache Hive e Azure Machine Learning

* [Apache Hive e Azure Machine Learning de ponta a ponta](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Usando um cluster Azure HDInsight Hadoop em um conjunto de um DataSet de 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark e MLLib

* [Aprendizado de máquina com o Apache Spark no HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark com Machine Learning: Usar Apache Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar Apache Spark no HDInsight para prever os resultados da inspeção de alimentos](../spark/apache-spark-machine-learning-mllib-ipython.md)

Aprendizado profundo, Cognitive Toolkit e outros

* [Máquina virtual do Azure de ciência de dados](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introdução ao H2O.ai no Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
