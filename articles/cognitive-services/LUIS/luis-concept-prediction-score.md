---
title: Pontuações de previsão - LUIS
description: Uma pontuação de previsão indica o grau de confiança que o serviço luis API tem para os resultados de previsão, com base numa expressão de utilizador.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316465"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Os resultados da previsão indicam precisão de previsão para intenção e entidades

Uma pontuação de previsão indica o grau de confiança que o LUIS tem para os resultados da previsão de uma expressão de utilizador.

Uma pontuação de previsão é entre zero (0) e um (1). Um exemplo de uma pontuação luis altamente confiante é de 0,99. Um exemplo de uma pontuação de baixa confiança é 0,01.

|Valor de pontuação|Confiança|
|--|--|
|1|jogo definitivo|
|0.99|alta confiança|
|0.01|baixa confiança|
|0|falha definitiva para corresponder|

## <a name="top-scoring-intent"></a>Intenção de melhor pontuação

Cada previsão de expressão devolve uma intenção de pontuação máxima. Esta previsão é uma comparação numérica das pontuações de previsão.

## <a name="proximity-of-scores-to-each-other"></a>Proximidade de pontuações entre si

As pontuações do top 2 podem ter uma diferença muito pequena entre eles. Luis não indica esta proximidade além de devolver a pontuação máxima.

## <a name="return-prediction-score-for-all-intents"></a>Pontuação de previsão de retorno para todos os efeitos

Um resultado de teste ou ponto final pode incluir todas as intenções. Esta configuração é definida no ponto final utilizando o par de nome/valor de consulta correto.

|API de predição|Nome de consulta|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Rever intenções com pontuações semelhantes

Rever a pontuação para todos os efeitos é uma boa maneira de verificar que não só a intenção correta é identificada, mas que a próxima pontuação identificada é significativa e consistentemente menor para as expressões.

Se várias intenções tiverem pontuações de previsão próximas, com base no contexto de uma expressão, LUIS pode alternar entre as intenções. Para corrigir esta situação, continue a adicionar expressões a cada intenção com uma maior variedade de diferenças contextuais ou pode ter a aplicação do cliente, como um chat bot, fazer escolhas programáticas sobre como lidar com as 2 principais intenções.

As duas intenções, que têm uma pontuação muito próxima, podem ser invertidas devido a uma **preparação não determinista**. A pontuação máxima pode tornar-se a segunda melhor e a segunda melhor pontuação pode tornar-se a primeira melhor pontuação. Para evitar esta situação, adicione palavras de exemplo a cada uma das duas principais intenções para que a expressão com escolha de palavras e contexto que diferencia as 2 intenções. As duas intenções devem ter o mesmo número de palavras de exemplo. Uma regra do polegar para a separação para evitar a inversão devido ao treino, é uma diferença de 15% nas pontuações.

Pode desligar o **treino não determinístico** [treinando com todos os dados.](luis-how-to-train.md#train-with-all-data)

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferenças com previsões entre diferentes sessões de treino

Quando treina o mesmo modelo numa aplicação diferente, e as pontuações não são as mesmas, esta diferença é porque há **formação não determinística** (um elemento de aleatoriedade). Em segundo lugar, qualquer sobreposição de uma expressão a mais do que uma intenção significa que a intenção máxima para a mesma expressão pode mudar com base no treino.

Se o seu chat bot necessitar de uma pontuação LUIS específica para indicar confiança numa intenção, deve usar a diferença de pontuação entre as duas principais intenções. Esta situação proporciona flexibilidade para as variações na formação.

Pode desligar o **treino não determinístico** [treinando com todos os dados.](luis-how-to-train.md#train-with-all-data)

## <a name="e-exponent-notation"></a>E (exponente) notação

As pontuações de previsão podem usar notação expoente, _aparecendo_ acima do intervalo 0-1, tais como `9.910309E-07` . Esta partitura é uma indicação de um número muito **pequeno.**

|Pontuação de notação E |Pontuação real|
|--|--|
|9.910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>Definições da aplicação

Utilize [as definições de aplicação](luis-reference-application-settings.md) para controlar como os diacríticos e pontuações de previsão de impacto de pontuação.

## <a name="next-steps"></a>Passos seguintes

Consulte [entidades Add](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua app LUIS.
