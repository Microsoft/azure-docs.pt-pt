---
title: Aprofunde-se-advanced analytics – Azure HDInsight
description: Saiba como a análise avançada usa algoritmos para processar grandes volumes de dados.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 893273fc325eaf878814c07eb095c67f5ea3c3f1
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675063"
---
# <a name="deep-dive---advanced-analytics"></a>Aprofunde-se-análises avançadas

## <a name="what-is-advanced-analytics-for-hdinsight"></a>O que é o advanced analytics para o HDInsight?

HDInsight fornece a capacidade para obter informações valiosas de grandes quantidades de não estruturados e dados de rápida evolução. Análise avançada é o uso de arquiteturas de aplicações altamente escaláveis, estatísticas e modelos de aprendizagem automática e inteligentes dashboards para lhe fornecer informações relevantes. Machine learning, ou *análises preditivas*, usa algoritmos que identificam e Aprenda com relações nos seus dados para fazer previsões e orientar suas decisões.

## <a name="advanced-analytics-process"></a>Processo de análise avançada

![Processo](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Depois que identificou o problema de negócios e começaram a recolher e processar os dados, tem de criar um modelo que representa a pergunta que pretende prever. Seu modelo irá utilizar um ou mais algoritmos de machine learning para tornar o tipo de predição que melhor se adequa às suas necessidades empresariais.  A maioria dos seus dados deve ser usada para preparar o seu modelo, com o restante utilizado para testar ou avaliá-lo. 

Depois de criar, carregar, testar e avaliar o seu modelo, a próxima etapa é implementar o seu modelo, de modo que ele começa a fornecer respostas para suas perguntas. A última etapa é monitorizar o desempenho de seu modelo e ajustá-lo conforme necessário. 

## <a name="common-types-of-algorithms"></a>Tipos comuns de algoritmos

Soluções de análise avançadas fornecem um conjunto de algoritmos de machine learning. Aqui está um resumo das categorias de algoritmos e casos de utilização empresarial comuns associados.

![Casos de utilização do Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Além de selecionar o melhor fitting algorithm(s), precisa considerar se ou não tem de fornecer dados de treinamento. Algoritmos de Machine learning são classificados da seguinte forma:

* Supervisionado - algoritmo tem de ser treinados num conjunto de dados etiquetados antes de poder fornecer resultados
* Quarto supervisionado - algoritmo pode ser aumentado mediante destinos Extras através de uma consulta interativa por um instrutor, que não estavam disponíveis durante a fase inicial de treinamento
* Não supervisionado - algoritmo não necessita de dados de formação 
* Por reforço - o algoritmo utiliza os agentes de software para determinar o comportamento ideal dentro de um contexto específico (muitas vezes utilizado robotics)


| Categoria do algoritmo| Utilizar | Tipo de aprendizado | Algoritmos |
| --- | --- | --- | -- |
| Classificação | Classificar as pessoas ou de coisas em grupos | Supervisionado | Árvores de decisões, regressão logística, redes neurais |
| Clustering | Divisão de um conjunto de exemplos em grupos homogéneo | Não supervisionado | Clustering de K-means |
| Deteção de padrão | Identificar as associações frequentes dos dados | Não supervisionado | Regras de associação |
| Regressão | Prever resultados numéricos | Supervisionado | Regressão linear, redes neurais |
| Por reforço | Determinar comportamento ideal para robots | Por reforço | Simulações Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine learning no HDInsight

HDInsight tem aprendizagem várias opções para um fluxo de trabalho de análise avançada:

* Machine Learning e do Apache Spark
* R e os serviços de ML
* Azure Machine Learning e o Apache Hive
* Apache Spark e de aprendizagem profunda

### <a name="machine-learning-and-apache-spark"></a>Machine Learning e do Apache Spark


[Spark do HDInsight](../spark/apache-spark-overview.md) é uma oferta alojado no Azure de [Apache Spark](https://spark.apache.org/), um unificada de código fonte aberto, a arquitetura de processamento de dados paralela que utiliza o processamento dentro da memória para melhorar a análise de macrodados. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação distribuída de dentro da memória do Spark tornam uma boa opção para algoritmos iterativos, utilizada em cálculos de machine learning e do graph. 


Existem três bibliotecas de aprendizado de máquina dimensionável que fornecer capacidades de modelação de algoritmos para este ambiente distribuído:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib contém a API original criada com base no Spark RDDs.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML é um pacote mais recente que fornece uma API de nível mais alto assentes DataFrames do Spark para a criação de pipelines de ML.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) - Machine Learning biblioteca para o Apache Spark (MMLSpark) foi concebido para tornar os cientistas de dados mais produtivos no Spark, para aumentar a taxa de experimentação e tirar partido de vanguarda de aprendizagem da Microsoft técnicas, incluindo a aprendizagem profunda, em conjuntos de dados muito grandes. A biblioteca de MMLSpark simplifica tarefas comuns de modelagem para a criação de modelos no PySpark. 

### <a name="r-and-ml-services"></a>R e os serviços de ML

Como parte do HDInsight, pode criar um cluster do HDInsight com o [serviços ML](../r-server/r-server-overview.md) pronta para ser utilizada com modelos e conjuntos de dados maciços. Esta nova capacidade fornece cientistas de dados e estatísticos com uma interface familiar do R que pode ser dimensionado a pedido através do HDInsight, sem a sobrecarga de manutenção e configuração do cluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e o Apache Hive

[O Azure Machine Learning Studio](https://studio.azureml.net/) fornece ferramentas para Análise Preditiva do modelo, bem como um serviço totalmente gerido que pode utilizar para implementar os seus modelos preditivos como serviços web prontos a consumir. O Azure Machine Learning fornece ferramentas para criar soluções completas de Análise Preditiva na cloud para rapidamente criar, testar, operacionalizar e gerir modelos de previsão. Selecionar a partir de uma grande biblioteca de algoritmos, utilize um studio baseado na web para a criação de modelos e implementar facilmente o seu modelo como um serviço web.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark e de aprendizagem profunda

[Aprendizagem profunda](https://www.microsoft.com/research/group/dltc/) é um ramo do machine learning que utiliza *redes neurais profundas* (DNNs), inspirado pelos processos biológicos do cérebro humano. Pesquisadores de muitos aprendizagem profunda, consulte como uma abordagem promissor para inteligência artificial. Alguns exemplos de aprendizagem profunda são conversores de um idioma falado, sistemas de reconhecimento de imagem e raciocínio de máquina. Para ajudar a avançar seu próprio trabalho na aprendizagem profunda, a Microsoft desenvolveu o gratuito, fácil de usar, open source [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). O Kit de ferramentas está sendo usada extensivamente por uma grande variedade de produtos da Microsoft, as empresas em todo o mundo com a necessidade de implementar a aprendizagem profunda à escala e estudantes interessados nos algoritmos e as técnicas mais recentes. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Cenário – classificação de imagens para identificar padrões no desenvolvimento urbanos

Vamos examinar um exemplo de um análise avançada de machine learning de pipeline com o HDInsight.

Neste cenário, que verá como DNNs produziram numa estrutura de aprendizagem profunda, o Microsoft do Cognitive Toolkit (CNTK), pode ser operacionalizado para a classificação grandes coleções de imagens armazenadas numa conta de armazenamento de Blobs do Azure com PySpark num cluster do HDInsight Spark. Esta abordagem é aplicada a um caso de utilização comuns de DNN, classificação de imagens aéreas e pode ser utilizada para identificar padrões recentes no desenvolvimento urbanos.  Irá utilizar um modelo de classificação de imagens com formação prévia. O modelo é com formação prévia no [conjunto de dados de CIFAR 10](https://www.cs.toronto.edu/~kriz/cifar.html) e tenha sido aplicada a imagens de retidas de 10 000.

Neste cenário de análises avançadas, existem três principais tarefas:

1. Crie um cluster do Hadoop de HDInsight do Azure com uma distribuição 2.1.0 do Apache Spark. 
2. Execute um script personalizado para instalar o Microsoft Cognitive Toolkit em todos os nós de um cluster do Azure HDInsight Spark. 
3. Carregar um bloco de notas do Jupyter criado previamente para o seu cluster do HDInsight Spark para aplicar um treinado Microsoft Cognitive Toolkit modelo em ficheiros numa conta de armazenamento de Blobs do Azure com a API de Python do Spark (PySpark) de aprendizagem profunda. 

Este exemplo utiliza a imagem de CIFAR 10 definir compilados e distribuídos por Alex Krizhevsky, Vinod Nair e Geoffrey hinton-consultora. O conjunto de dados de CIFAR 10 contém 32 × 32 60 000 imagens que pertencem a 10 classes mutuamente exclusivos de cores:

![Imagens](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Para obter mais detalhes sobre o conjunto de dados, consulte de Alex Krizhevsky [Learning várias camadas de funcionalidades de imagens muito pequena](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

O conjunto de dados eram particionado num conjunto de treinamento de 50 000 imagens e um conjunto de teste de 10 000 imagens. O primeiro conjunto foi utilizado para preparar um modelo de rede residuais vinte-camada-profunda convolucionais (utilizar o ResNet) usando o Microsoft Cognitive Toolkit seguindo [deste tutorial](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) partir do repositório GitHub do Cognitive Toolkit. As imagens de 10 000 restantes foram utilizadas para testar a precisão do modelo. É aí que a computação distribuída entra em cena: a tarefa de pré-processamento e as imagens de classificação é altamente ponto pode ser paralelizada. Com o modelo treinado guardado em mãos, usamos:

* PySpark para distribuir as imagens e o modelo preparado para nós de trabalho do cluster.
* Python para processar previamente as imagens em cada nó do cluster do HDInsight Spark.
* Conjunto de ferramentas cognitivos para carregar o modelo e a pontuação as imagens de pré-processadas em cada nó.
* Blocos de notas do Jupyter para executar o script do PySpark, agregar os resultados e usar [Matplotlib](https://matplotlib.org/) para visualizar o desempenho do modelo.

A todo o pré-processamento/classificação das 10 000 imagens demora menos de um minuto num cluster com 4 nós de trabalho. O modelo prevê com precisão as etiquetas de ~ 9,100 (91%) imagens. Uma matriz de confusão ilustra os erros mais comuns de classificação. Por exemplo, a matriz mostra que mislabeling cães como gatos e vice versa ocorre mais frequentemente do que para outras pares de etiqueta.

![Resultados](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Experimente!

Siga [deste tutorial](../spark/apache-spark-microsoft-cognitive-toolkit.md) para implementar esta solução ponto-a-ponto: configurar um cluster do Spark do HDInsight, Cognitive Toolkit de instalar e executar o bloco de notas do Jupyter que pontua 10 000 imagens CIFAR.

## <a name="next-steps"></a>Passos Seguintes

Apache Hive e do Azure Machine Learning

* [Apache Hive e o Azure Machine Learning ponto-a-ponto](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Utilizar um Cluster de Hadoop do HDInsight do Azure num conjunto de dados de 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark e o MLLib

* [Machine learning com o Apache Spark no HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark com Machine Learning: Utilizar o Apache Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Apache Spark no HDInsight para prever resultados de inspeções alimentares](../spark/apache-spark-machine-learning-mllib-ipython.md)

Aprendizagem profunda, Cognitive Toolkit e outros

* [Ciência de dados Máquina Virtual do Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introdução ao H2O.ai no Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
