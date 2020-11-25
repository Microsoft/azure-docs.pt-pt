---
title: Aprendizagem automática com faísca Apache
description: Este artigo fornece uma visão geral conceptual das capacidades de machine learning e data science disponíveis através do Apache Spark on Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: ced78955c71f37a5a6f5231f61e5327428834f00
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919802"
---
# <a name="machine-learning-with-apache-spark"></a>Aprendizagem automática com Faísca Apache

Apache Spark in Azure Synapse Analytics permite a aprendizagem automática com big data, fornecendo a capacidade de obter informações valiosas de grandes quantidades de dados estruturados, desestruturados e em movimento rápido. 

Esta secção inclui uma visão geral e tutoriais para fluxos de trabalho de machine learning, incluindo análise exploratória de dados, engenharia de recursos, formação de modelos, pontuação de modelos e implantação.  

## <a name="synapse-runtime"></a>Tempo de execução da sinapse 
O Synapse Runtime é um ambiente curado otimizado para a ciência dos dados e aprendizagem automática. O Synapse Runtime oferece uma gama de bibliotecas de código aberto populares e constrói no Azure Machine Learning SDK por padrão. O Synapse Runtime também inclui muitas bibliotecas externas, incluindo PyTorch, Scikit-Learn, XGBoost, e muito mais.

Saiba mais sobre as bibliotecas disponíveis e versões relacionadas visualizando o tempo de [execução Azure Synapse Analytics](../spark/apache-spark-version-support.md)publicado.

## <a name="exploratory-data-analysis"></a>Análise de dados exploratórios
Ao utilizar o Apache Spark in Azure Synapse Analytics, existem várias opções incorporadas para o ajudar a visualizar os seus dados, incluindo opções de gráficos de cadernos Synapse, acesso a bibliotecas de código aberto populares como Seaborn e Matplotlib, bem como integração com o Synapse SQL e Power BI.

Saiba mais sobre as opções de visualização de dados e análise de dados visualizando o artigo sobre como [visualizar dados usando Os Cadernos Azure Synapse](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Desenvolvimento de funcionalidades
Por predefinição, o Prazo de Execução da Sinapse inclui um conjunto de bibliotecas que são comumente usadas para engenharia de recursos. Para grandes conjuntos de dados, pode utilizar Spark SQL, MLlib e Koalas para engenharia de recursos. Para conjuntos de dados mais pequenos, bibliotecas de terceiros como Numpy, Pandas e Scikit-learn também fornecem métodos úteis para estes cenários.

## <a name="train-models"></a>Preparar modelos
Existem várias opções quando treinam modelos de machine learning usando Azure Spark em Azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning, e várias outras bibliotecas de código aberto. 

Saiba mais sobre as capacidades de aprendizagem automática visualizando o artigo sobre como [treinar modelos em Azure Synapse Analytics](../spark/apache-spark-machine-learning-training.md).

### <a name="sparkml-and-mllib"></a>SparkML e MLlib
As capacidades de computação distribuídas na memória da Spark fazem com que seja uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e computações de gráficos. ```spark.ml```fornece um conjunto uniforme de APIs de alto nível que ajudam os utilizadores a criar e afinar gasodutos de aprendizagem automática. Para saber mais ```spark.ml``` sobre, pode visitar o [guia de programação Apache Spark ML.](https://spark.apache.org/docs/1.2.2/ml-guide.html)

### <a name="azure-machine-learning-automl"></a>Azure Machine Learning AutoML
[A Azure Machine Learning AutoML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) (machine learning automatizado) ajuda a automatizar o processo de desenvolvimento de modelos de aprendizagem automática. Permite que cientistas de dados, analistas e desenvolvedores construam modelos ML com alta escala, eficiência e produtividade, ao mesmo tempo que sustentam a qualidade do modelo. Os componentes para executar o Azure Machine Learning AutoML SDK são incorporados diretamente no tempo de funcionamento da Sinapse.

### <a name="open-source-libraries"></a>Bibliotecas de código aberto
Cada piscina Apache Spark em Azure Synapse Analytics vem com um conjunto de bibliotecas de aprendizagem automática pré-carregadas e populares.  Algumas das bibliotecas de aprendizagem automática relevantes que estão incluídas por padrão incluem:

- [Scikit-learn](https://scikit-learn.org/stable/index.html) é uma das mais populares bibliotecas de aprendizagem de máquinas de nó único para algoritmos clássicos de ML. O Scikit-learn suporta a maioria dos algoritmos de aprendizagem supervisionados e não supervisionados e também pode ser usado para a mineração de dados e análise de dados.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) é uma popular biblioteca de machine learning que contém algoritmos otimizados para treinar árvores de decisão e florestas aleatórias. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) são poderosas bibliotecas de aprendizagem profunda python. Dentro de uma piscina Apache Spark em Azure Synapse Analytics, pode utilizar estas bibliotecas para construir modelos de máquina única, definindo o número de executores na sua piscina para zero. Apesar de o Apache Spark não estar funcional nesta configuração, é uma forma simples e económica de criar modelos de máquina única.

## <a name="track-model-development"></a>Monitorizar desenvolvimento de modelos
[O MLFlow](https://www.mlflow.org/) é uma biblioteca de código aberto para gerir o ciclo de vida das suas experiências de aprendizagem automática. MLFlow Tracking é um componente do MLflow que regista e rastreia as suas métricas de treino e artefactos de modelo. Para saber mais sobre como pode utilizar o MLFlow Tracking através do Azure Synapse Analytics e da Azure Machine Learning, visite este tutorial sobre [como usar o MLFlow](https://docs.microsoft.com/azure/machine-learning/how-to-use-mlflow).

## <a name="model-scoring"></a>Pontuação do modelo
A pontuação do modelo, ou inferenculação, é a fase em que um modelo é usado para fazer previsões. Para a pontuação do modelo com SparkML ou MLLib, pode aproveitar os métodos nativos de Spark para executar inferenculação diretamente num DataFrame de Faísca. Para outras bibliotecas de código aberto e tipos de modelos, também pode criar um Spark UDF para reduzir a inferência em grandes conjuntos de dados. Para conjuntos de dados mais pequenos, também pode utilizar os métodos de inferência do modelo nativo fornecidos pela biblioteca.

## <a name="register-and-serve-models"></a>Registar e servir modelos
Registar um modelo permite-lhe armazenar, ver e rastrear metadados sobre modelos no seu espaço de trabalho. Depois de ter terminado o treino do seu modelo, pode registar o seu modelo no registo do [modelo Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#register-package-and-deploy-models-from-anywhere). Uma vez registados, os modelos ONNX também podem ser usados para [enriquecer os dados armazenados](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) em piscinas SQL dedicadas.

## <a name="next-steps"></a>Passos seguintes
Para começar com machine learning em Azure Synapse Analytics, não se esqueça de conferer os seguintes tutoriais:
- [Analisar dados com cadernos Azure Synapse](../spark/apache-spark-data-visualization-tutorial.md)

- [Treine um modelo de aprendizagem automática com AutoML](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Treine um modelo de aprendizagem automática com Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
  