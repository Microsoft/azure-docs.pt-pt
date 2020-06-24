---
title: Defina uma tarefa de aprendizagem automática para uma corrida automatizada de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Saiba como definir uma tarefa de machine learning para uma corrida automatizada de aprendizagem automática
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 51b95255139a3c5c4d3b522b24144e289b233199
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84944258"
---
# <a name="how-to-define-a-machine-learning-task"></a>Como definir uma tarefa de aprendizagem automática 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende as tarefas de machine learning suportadas e como defini-las para uma experiência automatizada de aprendizagem automática (ML automatizado).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>O que é uma tarefa de aprendizagem automática?

Uma tarefa de aprendizagem automática representa o tipo de problema que está a ser resolvido através da criação de um modelo preditivo. O ML automatizado suporta três tipos diferentes de tarefas, incluindo classificação, regressão e previsão de séries de tempo.

Tipo de tarefa| Descrição| Exemplo
----|----|----
Classificação | Tarefa de prever a categoria de uma determinada linha num conjunto de dados. | Deteção de fraude num cartão de crédito. A coluna-alvo seria **fraude detetada** com categorias de *verdadeiro* ou *falso*. Neste caso, estamos a classificar cada linha dos dados como verdadeira ou falsa.
Regressão | Tarefa de prever uma produção contínua de quantidade. | Custo do automóvel com base nas suas características, a coluna-alvo seria o **preço.**
Previsão |Tarefa de edição de estimativas informadas na determinação da orientação das tendências futuras.| Previsão da procura de energia para eles nas próximas 48 horas. A coluna-alvo seria **a procura** e os valores previstos seriam utilizados para mostrar padrões na procura de energia.

O ML automatizado suporta os seguintes algoritmos durante o processo de automatização e afinação. Como utilizador, não há necessidade de especificar o algoritmo.

Classificação | Regressão | Previsão de Série Temporal
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM leve](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM leve](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM leve](https://lightgbm.readthedocs.io/en/latest/index.html)
[Aumento do gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Aumento do gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Aumento do gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore da Decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore da Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore da Decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Vizinhos Mais Próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação do vetor de suporte C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta Aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Descida estocástica do gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Definir o tipo de tarefa
Pode definir o tipo de tarefa para as suas experiências automatizadas de ML com o SDK ou o estúdio Azure Machine Learning.

Utilize o `task` parâmetro no construtor para `AutoMLConfig` especificar o seu tipo de experiência.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Pode definir a tarefa como parte da sua criação automatizada de experiências ML no estúdio Azure Machine Learning. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Seleção do tipo de tarefa](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [ml automatizado](concept-automated-ml.md) em Azure Machine Learning.
+ Saiba mais sobre [a formação automática de um modelo de previsão de séries temporizadas](how-to-auto-train-forecast.md) no Azure Machine Learning
+ Experimente o tutorial [de classificação automática de aprendizagem automática.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)
+ Experimente o caderno [de amostras de regressão de aprendizagem automática de máquinas.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb)

