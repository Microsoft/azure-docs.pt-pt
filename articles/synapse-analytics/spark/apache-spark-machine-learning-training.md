---
title: Modelos de aprendizagem de máquinas de trem com Faísca Apache
description: Use Apache Spark in Azure Synapse Analytics para treinar modelos de aprendizagem automática
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116811"
---
# <a name="train-machine-learning-models"></a>Modelos de aprendizagem de máquinas de comboio
Apache Spark in Azure Synapse Analytics permite a aprendizagem automática com big data, fornecendo a capacidade de obter informações valiosas de grandes quantidades de dados estruturados, desestruturados e em movimento rápido. Existem várias opções quando treinam modelos de machine learning usando Azure Spark em Azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning, e várias outras bibliotecas de código aberto. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML e MLlib
Apache Spark in Azure Synapse Analytics é uma das implementações da Microsoft de Apache Spark na nuvem. Fornece um quadro de processamento de dados paralelo, de código aberto e unificado, que suporta o processamento na memória para impulsionar a análise de big data. O motor de processamento de faíscas é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas na memória da Spark fazem com que seja uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e computações de gráficos. 

Existem duas bibliotecas de machine learning escaláveis que trazem capacidades de modelação algorítmica para este ambiente distribuído: MLlib e SparkML. MLlib contém a API original construída em cima de RDDs. SparkML é um pacote mais recente que fornece uma API de nível superior construída em cima de DataFrames para a construção de oleodutos ML. A SparkML ainda não suporta todas as funcionalidades da MLlib, mas está a substituir a MLlib como a biblioteca padrão de machine learning da Spark.

> [!NOTE]
> 
> Pode aprender mais sobre a criação de um modelo SparkML seguindo este [tutorial.](../spark/apache-spark-azure-machine-learning-tutorial.md)

## <a name="popular-libraries"></a>Bibliotecas populares
Cada piscina Apache Spark em Azure Synapse Analytics vem com um conjunto de bibliotecas de aprendizagem automática pré-carregadas e populares. Estas bibliotecas fornecem código reutilizável que poderá querer incluir nos seus programas ou projetos. Algumas das bibliotecas de aprendizagem automática relevantes que estão incluídas por padrão incluem:
- [Scikit-learn](https://scikit-learn.org/stable/index.html) é uma das mais populares bibliotecas de aprendizagem de máquinas de nó único para algoritmos clássicos de ML. O Scikit-learn suporta a maioria dos algoritmos de aprendizagem supervisionados e não supervisionados e também pode ser usado para a mineração de dados e análise de dados.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) é uma popular biblioteca de machine learning que contém algoritmos otimizados para treinar árvores de decisão e florestas aleatórias. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) são poderosas bibliotecas de aprendizagem profunda python. Dentro de uma piscina Apache Spark em Azure Synapse Analytics, pode utilizar estas bibliotecas para construir modelos de máquina única, definindo o número de executores na sua piscina para zero. Apesar de o Apache Spark não estar funcional nesta configuração, é uma forma simples e económica de criar modelos de máquina única.

Pode saber mais sobre as bibliotecas disponíveis e versões relacionadas visualizando o tempo de [execução Azure Synapse Analytics](../spark/apache-spark-version-support.md)publicado.

## <a name="mmlspark"></a>MMLSpark
A biblioteca microsoft machine learning para Apache Spark é [MMLSpark](https://github.com/Azure/mmlspark). Esta biblioteca é projetada para tornar os cientistas de dados mais produtivos em Spark, aumentar a taxa de experimentação, e alavancar técnicas de aprendizagem automática de ponta, incluindo aprendizagem profunda, em grandes conjuntos de dados. 

O MMLSpark fornece uma camada em cima das APIs de baixo nível da SparkML ao construir modelos ML escaláveis, tais como cadeias de indexação, coagir dados em um layout esperado por algoritmos de aprendizagem automática, e montagem de vetores de recursos. A biblioteca MMLSpark simplifica estas e outras tarefas comuns para a construção de modelos em PySpark.

## <a name="automated-ml-in-azure-machine-learning"></a>ML automatizado em aprendizagem automática Azure 
A azure Machine Learning é um ambiente baseado na nuvem que permite treinar, implantar, automatizar, gerir e rastrear modelos de aprendizagem de máquinas. ML automatizado em Azure Machine Learning aceita definições de dados de formação e configuração e itera automaticamente através de combinações de diferentes métodos de normalização/normalização de funcionalidades, modelos e configurações de hiperparímetro para chegar ao melhor modelo. 

Ao utilizar ml automatizado dentro do Azure Synapse Analytics, você pode aproveitar a integração profunda entre os diferentes serviços para simplificar a autenticação & formação de modelos. 

> [!NOTE]
> 
> Você pode aprender mais sobre a criação de uma experiência automatizada de ML Azure Machine Learning seguindo este [tutorial.](./spark/../apache-spark-azure-machine-learning-tutorial.md)

## <a name="azure-cognitive-services"></a>Serviços Cognitivos do Azure
[A Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) fornece capacidades de aprendizagem automática para resolver problemas gerais, como analisar texto para sentimento emocional ou analisar imagens para reconhecer objetos ou rostos. Não precisa de conhecimentos de machine learning ou ciência de dados para utilizar estes serviços. Um Serviço Cognitivo fornece parte ou todos os componentes numa solução de machine learning: dados, algoritmo e modelo treinado. Estes serviços destinam-se a exigir conhecimento geral sobre os seus dados sem necessitar de experiência com machine learning ou data science. Você pode aproveitar estes Serviços Cognitivos pré-treinados automaticamente dentro do Azure Synapse Analytics.

## <a name="next-steps"></a>Passos seguintes
Este artigo fornece uma visão geral das várias opções para treinar modelos de machine learning dentro das piscinas Apache Spark em Azure Synapse Analytics. Pode aprender mais sobre a formação de modelos seguindo o tutorial abaixo:

- Executar experiências automatizadas de ML usando Azure Machine Learning e Azure Synapse Analytics: [Tutorial automatizado de ML](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- Executar experiências SparkML: [Apache SparkML Tutorial](../spark/apache-spark-machine-learning-mllib-notebook.md)
- Ver as bibliotecas predefinidas: [Azure Synapse Analytics tempo de execução](../spark/apache-spark-version-support.md)