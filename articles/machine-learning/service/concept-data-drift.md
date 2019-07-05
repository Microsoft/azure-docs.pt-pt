---
title: Descompassos de dados de monitorização (pré-visualização)
titleSuffix: Azure Machine Learning service
description: Saiba como monitorizar o serviço Azure Machine Learning para desvios de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: a03e3124647869e7148f271810bb523986a851c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442390"
---
# <a name="what-is-data-drift-monitoring-preview"></a>O que são os dados de monitorização (pré-visualização) inconsistências?

Desvios de dados são a alteração na distribuição de dados. No contexto do machine learning, com formação modelos de machine learning poderão ter de predição de degradação de desempenho devido a desvios. Descompassos entre dados de treinamento e os dados utilizados para fazer previsões de monitorização pode ajudar a detectar problemas de desempenho.

Modelos de aprendizagem automática só são tão boas quanto os dados usados para treiná-los. Implementação de modelos para produção sem a monitorização de desempenho dele pode levar a impactos detectados e prejudiciais. Com a monitorização de desvios de dados, pode detetar e adaptar-se às descompassos de dados. 

## <a name="when-to-monitor-for-data-drift"></a>Quando monitorar os descompassos de dados?

As métricas que pode monitorizar incluem:

+ Magnitude do descompassos (Descompassos coeficiente)
+ Causa de desvios (Descompassos de contribuição pela funcionalidade)
+ Métricas de distância (Wasserstein, energia, etc.)

Com esta monitorização in-loco, alertas ou ações podem ser configuradas quando é detetado descompassos e cientista de dados pode investigar a causa de raiz do problema. 

Se acha que os dados de entrada para o seu modelo implementado podem ser alterados, deve considerar a utilização de deteção de desvios de dados.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Como os descompassos de dados está sendo monitorado no serviço Azure Machine Learning

Usando **serviço Azure Machine Learning**, descompassos de dados está a ser monitorizado através de conjuntos de dados ou implementações. Para monitorar os descompassos de dados, um conjunto de dados de linha de base - normalmente, o treinamento conjunto de dados para um modelo - é especificado. Normalmente, de recolha de dados de entrada de modelo de uma implementação - é testado um segundo conjunto de dados - conjunto de dados da linha de base. Ambos os conjuntos de dados são [profiled](how-to-explore-prepare-data.md#explore-with-summary-statistics) e serviço de monitoramento de inconsistências de entrada para os dados. A preparar um modelo de machine learning é utilizado para detetar as diferenças entre os dois conjuntos de dados. Desempenho do modelo é convertido para o coeficiente de desvios, que mede a magnitude da descompassos entre os dois conjuntos de dados. Usando [modelar interpretability](machine-learning-interpretability-explainability.md) os recursos que contribuíram para o coeficiente de desvios são computados. O perfil de conjunto de dados, são rastreadas informações estatísticas sobre cada funcionalidade. 

## <a name="data-drift-metric-output"></a>Saída de métrica de inconsistências de dados

Existem várias formas para visualizar as métricas de desvios:

* Utilize o widget do Jupyter.
* Utilize o `get_metrics()` função em qualquer `datadriftRun` objeto.
* Ver as métricas no portal do Azure no seu modelo

As métricas seguintes são guardadas em cada iteração de execução de uma tarefa de desvios de dados:

|Métrica|Descrição|
--|--|
wasserstein_distance|Distância estatística definida para distribuição numérica unidimensional.|
energy_distance|Distância estatística definida para distribuição numérica unidimensional.|
datadrift_coefficient|Formalmente Matthews coeficiente de correlação, um número real que variam entre -1 e 1. No contexto de desvios, 0 indica sem descompassos e 1 descompassos máximo.|
datadrift_contribution|Importância da funcionalidade dos recursos que contribuem para divergir.|

## <a name="next-steps"></a>Passos Seguintes

Ver exemplos e saiba como monitorizar para desvios de dados:

+ [Saiba como monitorizar os descompassos de dados em modelos implementados através do Azure Kubernetes Service (AKS)](how-to-monitor-data-drift.md)
+ Experimentar o [exemplos de bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)