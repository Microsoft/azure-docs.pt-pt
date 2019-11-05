---
title: Pontuações de previsão-LUIS
titleSuffix: Azure Cognitive Services
description: Uma pontuação de previsão indica o grau de confiança que o serviço de API LUIS tem para os resultados de previsão, com base em um usuário expressão.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 5b8d97005d8f404a296ddb45e92b65e4aa811aa3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486774"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Pontuações de previsão indicam precisão de previsão para intenção e entidades

Uma pontuação de previsão indica o grau de confiança que o LUIS tem para os resultados de previsão de um usuário expressão.

Uma pontuação de previsão está entre zero (0) e uma (1). Um exemplo de uma pontuação de LUIS altamente confiável é 0,99. Um exemplo de uma pontuação de confiança baixa é de 0, 1. 

|Valor da Pontuação|confidencial|
|--|--|
|1|correspondência definitiva|
|0,99|alta confiança|
|0, 1|baixa confiança|
|0|falha definitiva para correspondência|

## <a name="top-scoring-intent"></a>Intenção de pontuação superior

Toda previsão de expressão retorna uma intenção de pontuação superior. Essa previsão é uma comparação numérica de pontuações de previsão. 

## <a name="proximity-of-scores-to-each-other"></a>Proximidade de pontuações entre si

As duas pontuações principais podem ter uma diferença muito pequena entre elas. LUIS não indica essa proximidade além de retornar a pontuação superior.  

## <a name="return-prediction-score-for-all-intents"></a>Retornar Pontuação de previsão para todas as intenções

Um teste ou resultado de ponto de extremidade pode incluir todas as intenções. Essa configuração é definida no ponto de extremidade usando o par nome/valor da QueryString correto.

|API de predição|Nome da QueryString|
|--|--|
|V2|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Revisar tentativas com pontuações semelhantes

Examinar a pontuação de todas as intenções é uma boa maneira de verificar se não só a intenção correta foi identificada, mas a pontuação da próxima intenção identificada é significativamente e consistentemente menor para declarações.

Se várias tentativas tiverem pontuações de previsão fechadas, com base no contexto de um expressão, LUIS poderá alternar entre as intenções. Para corrigir essa situação, continue a adicionar o declarações a cada tentativa com uma variedade maior de diferenças contextuais ou você pode ter o aplicativo cliente, como um bot de bate-papo, fazer escolhas programáticas sobre como lidar com as duas principais intenções.

As duas tentativas, que são pontuadas com muita pontuação, podem ser invertidas devido a **treinamento não determinístico**. A pontuação superior poderia se tornar a segunda superior e a segunda pontuação superior poderia se tornar a primeira pontuação superior. Para evitar essa situação, adicione um exemplo de declarações a cada uma das duas primeiras intenções para esse expressão com a escolha do Word e o contexto que diferencia as duas intenções. As duas intenções devem ter aproximadamente o mesmo número de declarações de exemplo. Uma regra geral para separação para evitar inversão devido ao treinamento é uma diferença de 15% nas pontuações.

Você pode desativar o **treinamento não determinístico** ao [treinar todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferenças com previsões entre diferentes sessões de treinamento

Quando você treina o mesmo modelo em um aplicativo diferente e as pontuações não são as mesmas, essa diferença ocorre porque há um **treinamento não determinístico** (um elemento de aleatoriedade). Em segundo lugar, qualquer sobreposição de um expressão para mais de uma intenção significa que a principal intenção para o mesmo expressão pode mudar com base no treinamento.

Se o seu bot de chat exigir uma pontuação LUIS específica para indicar confiança em uma intenção, você deverá usar a diferença de pontuação entre as duas primeiras intenções. Essa situação fornece flexibilidade para variações no treinamento.

Você pode desativar o **treinamento não determinístico** ao [treinar todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>Notação E (expoente)

As pontuações de previsão podem usar a notação exponencial, que *aparece* acima do intervalo de 0-1, como `9.910309E-07`. Essa pontuação é uma indicação de um número muito **pequeno** .

|Pontuação de notação E |Pontuação real|
|--|--|
|9.910309 e-07|.0000009910309|

## <a name="punctuation"></a>Pontuação

[Saiba mais](luis-concept-utterance.md#punctuation-marks) sobre como usar ou ignorar a pontuação. 

## <a name="next-steps"></a>Passos seguintes

Consulte [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades ao seu aplicativo Luis.
