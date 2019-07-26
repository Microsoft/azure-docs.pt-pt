---
title: Monitoramento de descompasso de dados (versão prévia)
titleSuffix: Azure Machine Learning service
description: Saiba como Azure Machine Learning serviço pode monitorar a descompasso de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371093"
---
# <a name="what-is-data-drift-monitoring-preview"></a>O que é o monitoramento de descompasso de dados (versão prévia)?

A descompasso de dados é a alteração na distribuição de dados. No contexto do aprendizado de máquina, os modelos de aprendizado de máquina treinados podem enfrentar desempenho de previsão degradado devido à descompasso. O monitoramento de descompasso entre dados de treinamento e dados usados para fazer previsões pode ajudar a detectar problemas de desempenho.

Os modelos de aprendizado de máquina são tão bons quanto os dados usados para treiná-los. Implantar modelos na produção sem monitorar seu desempenho pode levar a impactos não detectados e prejudiciais. Com o monitoramento de descompasso de dados, você pode detectar e adaptar-se a descompasso de dados. 

## <a name="when-to-monitor-for-data-drift"></a>Quando monitorar a descompasso de dados?

As métricas que podemos monitorar incluem:

+ Magnitude de descompasso (coeficiente de descompasso)
+ Causa de descompasso (contribuição de descompasso por recurso)
+ Métricas de distância (Wasserstein, energia, etc.)

Com esse monitoramento em vigor, alertas ou ações podem ser configurados quando o descompasso é detectado e o cientista de dados pode investigar a causa raiz do problema. 

Se você considerar que os dados de entrada para o modelo implantado podem ser alterados, considere o uso da detecção de descompasso de dados.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Como a descompasso de dados é monitorada no serviço Azure Machine Learning

Usando o **serviço Azure Machine Learning**, a descompasso de dados é monitorada por meio de conjuntos ou de implantações. Para monitorar a descompasso de dados, é um DataSet de linha de base – geralmente o conjunto de dado de treinamento para um modelo-é especificado. Um segundo conjunto de dados-geralmente modelar os dados de entrada coletados de uma implantação-é testado no conjunto de dado de linha de base. Os dois conjuntos de dados são de [perfil](how-to-explore-prepare-data.md#explore-with-summary-statistics) e são inseridos para o serviço de monitoramento de descompasso. Um modelo de aprendizado de máquina é treinado para detectar diferenças entre os dois conjuntos de valores. O desempenho do modelo é convertido para o coeficiente de descompasso, que mede a magnitude do descompasso entre os dois conjuntos de valores. Usando a [interpretação de modelo](machine-learning-interpretability-explainability.md) , os recursos que contribuíram para o coeficiente de descompasso são computados. No perfil do conjunto de dados, as informações estatísticas sobre cada recurso são rastreadas. 

## <a name="data-drift-metric-output"></a>Saída da métrica de descompasso de dados

Há várias maneiras de exibir as métricas de descompasso:

* Use o `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Use a `get_metrics()` função em qualquer `datadriftRun` objeto.
* Exibir as métricas no portal do Azure em seu modelo

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de descompasso de dados:

|Métrica|Descrição|
--|--|
wasserstein_distance|Distância estatística definida para distribuição numérica unidimensional.|
energy_distance|Distância estatística definida para distribuição numérica unidimensional.|
datadrift_coefficient|Um coeficiente de correlação formalmente Matthews, um número real que varia de-1 a 1. No contexto de descompasso, 0 indica que não há descompasso e 1 indica o descompasso máximo.|
datadrift_contribution|Importância dos recursos que contribuem para descompasso.|

## <a name="next-steps"></a>Passos Seguintes

Veja exemplos e saiba como monitorar a descompasso de dados:

+ [Saiba como monitorar a descompasso de dados em modelos implantados por meio do AKS (serviço kubernetes do Azure)](how-to-monitor-data-drift.md)
+ Experimentar [Jupyter Notebook amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)