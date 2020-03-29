---
title: Defina uma tarefa de aprendizagem automática para uma execução automatizada de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Aprenda a definir uma tarefa de aprendizagem automática para uma corrida automatizada de aprendizagem automática
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75540532"
---
# <a name="how-to-define-a-machine-learning-task"></a>Como definir uma tarefa de aprendizagem automática 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se as tarefas de aprendizagem automática e como defini-las para uma experiência automatizada de aprendizagem automática (ML).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>O que é uma tarefa de aprendizagem automática?

Uma tarefa de aprendizagem automática representa o tipo de problema que está a ser resolvido através da criação de um modelo preditivo. O ML automatizado suporta três tipos diferentes de tarefas, incluindo classificação, regressão e previsão de séries temporais.

Tipo de tarefa| Descrição| Exemplo
----|----|----
Classificação | Tarefa de prever a categoria de uma determinada linha num conjunto de dados. | Deteção de fraude num cartão de crédito. A coluna-alvo seria **fraude detetada** com categorias de *Verdade* ou *Falso*. Neste caso, estamos classificando cada linha nos dados como verdadeira ou falsa.
Regressão | Tarefa para prever uma produção contínua de quantidade. | O custo do automóvel com base nas suas características, a coluna-alvo seria o **preço.**
Previsão |Tarefa de fazer estimativas informadas na determinação do rumo das tendências futuras.| Prevendo a procura de energia nas próximas 48 horas. A coluna-alvo seria **a procura** e os valores previstos seriam utilizados para mostrar padrões na procura de energia.

Ml automatizado suporta os seguintes algoritmos durante o processo de automatização e afinação. Como utilizador, não há necessidade de especificar o algoritmo.

Classificação | Regressão | Previsão de Série Temporal
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)|[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)|[Gbm leve](https://lightgbm.readthedocs.io/en/latest/index.html)
[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Reforço do Gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação do vetor de suporte C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classe DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador Linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regresso linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regresso linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Descida do Gradiente Estocástico (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Definir o tipo de tarefa
Pode definir o tipo de tarefa para as suas experiências automatizadas de ML com o SDK ou o estúdio Azure Machine Learning.

Utilize `task` o parâmetro `AutoMLConfig` no construtor para especificar o seu tipo de experiência.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Pode definir a tarefa como parte da sua experiência automatizada ml executar criação no estúdio Azure Machine Learning. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Seleção de tipos de tarefas](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [ml automatizado](concept-automated-ml.md) em Azure Machine Learning.
+ Saiba mais sobre [auto-formação de um modelo de previsão de série seletiva](how-to-auto-train-forecast.md) em Azure Machine Learning
+ Experimente o tutorial automatizado de classificação de [aprendizagem automática.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)
+ Experimente o caderno de amostras de regressão de [aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) de máquinas.

