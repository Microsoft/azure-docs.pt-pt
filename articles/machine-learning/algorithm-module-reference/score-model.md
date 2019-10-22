---
title: 'Modelo de Pontuação: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo modelo de pontuação no serviço Azure Machine Learning para gerar previsões usando um modelo de classificação ou regressão treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f046b41d02ac392d003a9ecd15f25b43e5449cd0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692679"
---
# <a name="score-model-module"></a>Módulo do modelo de pontuação

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para gerar previsões usando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como utilizar

1. Adicione o módulo **modelo de Pontuação** ao seu pipeline.

2. Anexe um modelo treinado e um conjunto de dados que contenha novos dado de entrada. 

    Os dados devem estar em um formato compatível com o tipo de modelo treinado que você está usando. O esquema do conjunto de dados de entrada também deve corresponder ao esquema dos dados usados para treinar o modelo.

3. Executar o pipeline.

## <a name="results"></a>Resultados

Depois de gerar um conjunto de pontuações usando o [modelo de Pontuação](./score-model.md):

+ Para gerar um conjunto de métricas usado para avaliar a precisão do modelo (desempenho).  Você pode conectar o conjunto de pontos de Pontuação para [avaliar o modelo](./evaluate-model.md), 
+ Clique com o botão direito do mouse no módulo e selecione **Visualizar** para ver um exemplo dos resultados.
+ Salvar os resultados em um DataSet.

A pontuação, ou o valor previsto, pode estar em vários formatos diferentes, dependendo do modelo e dos dados de entrada:

- Para modelos de classificação, o [modelo de Pontuação](./score-model.md) gera um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para modelos de regressão, o [modelo de Pontuação](./score-model.md) gera apenas o valor numérico previsto.
- Para modelos de classificação de imagem, a pontuação pode ser a classe de objeto na imagem ou um booliano que indica se um determinado recurso foi encontrado.

## <a name="publish-scores-as-a-web-service"></a>Publicar pontuações como um serviço Web

Um uso comum de pontuação é retornar a saída como parte de um serviço Web de previsão. Para obter mais informações, consulte este tutorial sobre como criar um serviço Web baseado em um pipeline no Azure Machine Learning:


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 