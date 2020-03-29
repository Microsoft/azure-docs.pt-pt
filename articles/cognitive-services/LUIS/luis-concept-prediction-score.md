---
title: Pontuações de previsão - LUIS
titleSuffix: Azure Cognitive Services
description: Uma pontuação de previsão indica o grau de confiança que o serviço LUIS API tem para os resultados de previsão, com base na expressão de um utilizador.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280813"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Os resultados da previsão indicam precisão de previsão para intenções e entidades

Uma pontuação de previsão indica o grau de confiança que a LUIS tem para os resultados de previsão de uma expressão de utilizador.

Uma pontuação de previsão é entre zero (0) e um (1). Um exemplo de uma pontuação LUIS altamente confiante é 0,99. Um exemplo de uma pontuação de baixa confiança é 0,01. 

|Valor da pontuação|Confiança|
|--|--|
|1|jogo definitivo|
|0.99|alta confiança|
|0.01|baixa confiança|
|0|falha definitiva de corresponder|

## <a name="top-scoring-intent"></a>Intenção de pontuação máxima

Cada previsão de expressão retorna a uma intenção de pontuação máxima. Esta previsão é uma comparação numérica das pontuações de previsão. 

## <a name="proximity-of-scores-to-each-other"></a>Proximidade de pontuações uns com os outros

As 2 melhores pontuações podem ter uma diferença muito pequena entre eles. Luis não indica esta proximidade além de devolver a pontuação máxima.  

## <a name="return-prediction-score-for-all-intents"></a>Pontuação de previsão de retorno para todas as intenções

Um resultado de teste ou ponto final pode incluir todas as intenções. Esta configuração é definida no ponto final utilizando o nome/par de valor de consulta correta.

|API de predição|Nome de corda de consulta|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Rever as intenções com pontuações semelhantes

Rever a pontuação para todas as intenções é uma boa maneira de verificar que não só é identificada a intenção correta, como que a pontuação da intenção identificada seguinte é significativa e consistentemente mais baixa para as declarações.

Se as múltiplas intenções tiverem pontuações de previsão próximas, com base no contexto de uma expressão, o LUIS pode alternar entre as intenções. Para corrigir esta situação, continue a adicionar expressões a cada intenção com uma maior variedade de diferenças contextuais ou pode ter a aplicação do cliente, como um chat bot, fazer escolhas programáticas sobre como lidar com as 2 principais intenções.

As duas intenções, demasiado pontuadas, podem inverter-se devido a **uma formação não determinística.** A pontuação máxima pode tornar-se a segunda melhor e a segunda melhor pontuação pode tornar-se a primeira pontuação máxima. Para prevenir esta situação, adicione declarações exemplo a cada uma das duas principais intenções para que a expressão com escolha de palavras e contexto que diferencia as 2 intenções. As duas intenções devem ter o mesmo número de declarações de exemplo. Uma regra do polegar para a separação para prevenir a inversão devido ao treino, é uma diferença de 15% nas pontuações.

Pode desligar a **formação não determinística** [treinando com todos os dados.](luis-how-to-train.md#train-with-all-data)

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferenças com previsões entre diferentes sessões de treino

Quando treina o mesmo modelo numa aplicação diferente, e as pontuações não são as mesmas, esta diferença é porque há **treino não determinístico** (um elemento de aleatoriedade). Em segundo lugar, qualquer sobreposição de uma expressão a mais do que uma intenção significa que a intenção máxima para a mesma expressão pode mudar com base no treino.

Se o seu chat bot necessitar de uma pontuação LUIS específica para indicar confiança numa intenção, deve utilizar a diferença de pontuação entre as duas principais intenções. Esta situação proporciona flexibilidade para as variações na formação.

Pode desligar a **formação não determinística** [treinando com todos os dados.](luis-how-to-train.md#train-with-all-data)

## <a name="e-exponent-notation"></a>Notação E (expoente)

As pontuações de previsão podem usar notação exponente, `9.910309E-07` _aparecendo_ acima da gama 0-1, como . Esta pontuação é uma indicação de um número muito **pequeno.**

|Pontuação de notação E |Pontuação real|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Pontuação

[Saiba mais](luis-concept-utterance.md#punctuation-marks) sobre como usar ou ignorar a pontuação. 

## <a name="next-steps"></a>Passos seguintes

Consulte [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.
