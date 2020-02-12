---
title: 'Modelo de Pontuação: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de pontuação em Azure Machine Learning para gerar previsões usando um modelo de classificação ou regressão treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: c06d1268abe8afdeb03668131c3c61cfbafa44cd
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138181"
---
# <a name="score-model-module"></a>Módulo do modelo de pontuação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para gerar previsões usando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como utilizar

1. Adicione o módulo **'Modelo de Pontuação'** ao seu pipeline.

2. Anexe um modelo treinado e um conjunto de dados que contenha novos dado de entrada. 

    Os dados devem estar em um formato compatível com o tipo de modelo treinado que você está usando. O esquema do conjunto de dados de entrada também deve corresponder ao esquema dos dados usados para treinar o modelo.

3. Executar o pipeline.

## <a name="results"></a>Resultados

Depois de ter gerado um conjunto de pontuações usando [o Modelo de Pontuação:](./score-model.md)

+ Para gerar um conjunto de métricas usado para avaliar a precisão do modelo (desempenho).  pode ligar o conjunto de dados pontuado ao [Modelo de Avaliação,](./evaluate-model.md) 
+ Clique no módulo e **selecione Visualize** para ver uma amostra dos resultados.
+ Salvar os resultados em um DataSet.

A pontuação, ou o valor previsto, pode estar em vários formatos diferentes, dependendo do modelo e dos dados de entrada:

- Para os modelos de classificação, [o Score Model](./score-model.md) produz um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para os modelos de regressão, [o Score Model](./score-model.md) gera apenas o valor numérico previsto.


## <a name="publish-scores-as-a-web-service"></a>Publicar pontuações como um serviço Web

Um uso comum de pontuação é retornar a saída como parte de um serviço Web de previsão. Para obter mais informações, consulte este tutorial sobre como criar um serviço Web baseado em um pipeline no Azure Machine Learning:

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 