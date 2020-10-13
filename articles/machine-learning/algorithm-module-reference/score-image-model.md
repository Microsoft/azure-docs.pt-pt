---
title: Utilize o módulo modelo de imagem de pontuação
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo De Modelo de Imagem de Pontuação em Azure Machine Learning para gerar previsões usando um modelo de imagem treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 88ca997e2d22283babf582b10d9b0eeb7de122c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905186"
---
# <a name="score-image-model"></a>Classificar o Modelo de Imagem

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para gerar previsões utilizando um modelo de imagem treinado em dados de imagem de entrada.

## <a name="how-to-configure-score-image-model"></a>Como configurar o modelo de imagem de pontuação

1. Adicione o módulo **Modelo de Imagem de Pontuação** ao seu oleoduto.

2. Anexar um modelo de imagem treinado e um conjunto de dados contendo dados de imagem de entrada. 

    Os dados devem ser do tipo ImageDirectory. Consulte o módulo [De Conversão para Diretório de Imagem](convert-to-image-directory.md) para obter mais informações sobre como obter um diretório de imagem. O esquema do conjunto de dados de entrada também deve corresponder geralmente ao esquema dos dados utilizados para treinar o modelo.

3. Envie o oleoduto.

## <a name="results"></a>Results

Depois de ter gerado um conjunto de pontuações utilizando [o Score Image Model,](score-image-model.md)para gerar um conjunto de métricas utilizadas para avaliar a precisão (desempenho) do modelo, pode ligar este módulo e o conjunto de dados pontuado ao [Modelo de Avaliação,](evaluate-model.md) 

### <a name="publish-scores-as-a-web-service"></a>Publicar pontuações como um serviço web

Um uso comum de pontuação é devolver a saída como parte de um serviço web preditivo. Para mais informações, consulte [este tutorial](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) sobre como implementar um ponto final em tempo real baseado num pipeline no designer de Machine Learning Azure.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
