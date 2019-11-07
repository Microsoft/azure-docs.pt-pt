---
title: Definir uma tarefa de aprendizado de máquina para uma execução automatizada do Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como definir uma tarefa de aprendizado de máquina para uma execução automatizada do Machine Learning
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: c34a8e999267a77fa4aabbd9dc3a73b2c4f5a0ef
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647219"
---
# <a name="how-to-define-a-machine-learning-task"></a>Como definir uma tarefa de aprendizado de máquina 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende as tarefas de aprendizado de máquina com suporte e como defini-las para uma execução de teste automatizado do Machine Learning (ML automatizado).


## <a name="What is a machine learning task?"></a>O que é uma tarefa de aprendizado de máquina?

Uma tarefa de aprendizado de máquina representa o tipo de problema que está sendo resolvido criando um modelo de previsão. O ML automatizado dá suporte a três tipos diferentes de tarefas, incluindo classificação, regressão e previsão de série temporal.

Tipo de tarefa| Descrição| Exemplo
----|----|----
Classificação | Tarefa para prever a categoria de uma linha específica em um conjunto de dado. | Detecção de fraudes em um cartão de crédito. A coluna de destino seria **detectada por fraude** com categorias de *verdadeiro* ou *falso*. Nesse caso, estamos classificando cada linha dos dados como true ou false.
Regressão | Tarefa para prever uma saída de quantidade contínua. | Custo de automóvel com base em seus recursos, a coluna de destino seria o **preço**.
Previsão |Tarefa para fazer estimativas informadas na determinação da direção das tendências futuras.| Previsão da demanda de energia para as próximas 48 horas. A coluna de destino seria **demanda** e os valores previstos seriam usados para mostrar padrões na demanda de energia.

O ML automatizado dá suporte aos seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não é necessário especificar o algoritmo.

Classificação | Regressão | Previsão de série temporal
-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Aumento de gradiente](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Aumento de gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Aumento de gradiente](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de decisão](https://scikit-learn.org/stable/modules/tree.html#regression)
[Os vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Os vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Os vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)|[Laço LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Laço LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificação de vetor de suporte do C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Descendente estocástico Grading (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Descendente estocástico Grading (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador linear DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Descendente estocástico Grading (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Definir o tipo de tarefa
Você pode definir o tipo de tarefa para seus experimentos de ML automatizados com o SDK ou o Azure Machine Learning Studio.

Use o parâmetro `task` no Construtor `AutoMLConfig` para especificar o tipo de experimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Você pode definir a tarefa como parte de sua criação de execução de experimento do ML automatizado no Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Seleção do tipo de tarefa](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre o [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Saiba mais sobre [o treinamento automático de um modelo de previsão de série temporal](how-to-auto-train-forecast.md) no Azure Machine Learning
+ Experimente o tutorial de [classificação de Machine Learning automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Experimente o bloco de anotações de amostra de [regressão automatizada de Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .

