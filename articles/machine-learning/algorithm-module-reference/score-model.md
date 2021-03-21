---
title: 'Modelo de pontuação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Score Model em Azure Machine Learning para gerar previsões utilizando um modelo de classificação ou regressão treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 67d17af4f615907ca50b27ce8fa26973e5869608
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93314251"
---
# <a name="score-model"></a>Modelo de Classificação

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para gerar previsões utilizando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como utilizar

1. Adicione o módulo **'Modelo de Pontuação'** ao seu oleoduto.

2. Anexar um modelo treinado e um conjunto de dados contendo novos dados de entrada. 

    Os dados devem estar num formato compatível com o tipo de modelo treinado que está a utilizar. O esquema do conjunto de dados de entrada também deve corresponder geralmente ao esquema dos dados utilizados para treinar o modelo.

3. Envie o oleoduto.

## <a name="results"></a>Resultados

Depois de ter gerado um conjunto de pontuações usando [o Score Model](./score-model.md):

+ Para gerar um conjunto de métricas utilizadas para avaliar a precisão (desempenho) do modelo, pode ligar o conjunto de dados pontuado ao [Modelo de Avaliação,](./evaluate-model.md) 
+ Clique com o botão direito no módulo e **selecione Visualize** para ver uma amostra dos resultados.
<!-- + To Save the results to a dataset. -->

A pontuação, ou valor previsto, pode estar em vários formatos diferentes, dependendo do modelo e dos dados de entrada:

- Para os modelos de classificação, [o Score Model](./score-model.md) produz um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para os modelos de regressão, [o Score Model](./score-model.md) gera apenas o valor numérico previsto.


## <a name="publish-scores-as-a-web-service"></a>Publicar pontuações como um serviço web

Um uso comum de pontuação é devolver a saída como parte de um serviço web preditivo. Para mais informações, consulte [este tutorial](../tutorial-designer-automobile-price-deploy.md) sobre como implementar um ponto final em tempo real baseado num pipeline no designer de Machine Learning Azure.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.