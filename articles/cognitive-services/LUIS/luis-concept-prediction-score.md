---
title: Pontuações de predição
titleSuffix: Language Understanding - Azure Cognitive Services
description: Uma pontuação de predição indica o grau de confiança que o serviço de API de LUIS tem para resultados de predição, com base numa expressão de utilizador.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121762"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Pontuações de previsão indicam a exatidão da previsão para intenções e entidades

Uma pontuação de predição indica o grau de confiança LUIS tem para resultados de predição, com base numa expressão de utilizador.

Uma pontuação de predição é entre zero (0) e um (1). Um exemplo de uma pontuação de LUIS elevada confiança será 0,99. Um exemplo de uma pontuação de confiança baixa é a 0,01. 

|Valor de pontuação|Confiança|
|--|--|
|1|correspondência definida|
|0,99|confiança elevada|
|0.01|confiança baixa|
|0|falha definida para corresponder|

Quando uma expressão resulta numa pontuação de confiança de baixa, LUIS destaca que, na [LUIS](luis-reference-regions.md) site **intenção** página, com o identificados **intenção rotulado** descritos com red.

![Discrepância de pontuação](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Intenção da parte superior de classificação

Predição de cada expressão retorna uma intenção da parte superior de classificação. Esta predição é uma comparação numérica de pontuações de predição. As principais 2 classificações podem ter uma muito pequena diferença entre eles. LUIS não indicar este proximidade além de retornar a pontuação superior.  

## <a name="return-prediction-score-for-all-intents"></a>Devolver a pontuação de previsão para todos os objetivos

Um resultado de teste ou ponto de extremidade pode incluir todas as intenções. Esta configuração é definida no [ponto final](https://aka.ms/v1-endpoint-api-docs) com o `verbose=true` par nome/valor de cadeia de caracteres de consulta.

## <a name="review-intents-with-similar-scores"></a>Reveja os objetivos com pontuações semelhantes

Rever a classificação para todas as intenções é uma excelente forma de verificar que não só é identificada a intenção correta, mas que a próxima identificadas pontuação da intenção é significativamente menor consistentemente para expressões com.

Se tem de objetivos várias pontuações de predição de fechar, com base no contexto de uma expressão, LUIS pode alternar entre os objetivos. Para corrigir esta situação, continuar a adicionar expressões em cada intenção com uma maior variedade de diferenças contextuais ou pode ter o aplicativo de cliente, como um chatbot, fazer escolhas programáticas sobre como lidar com os 2 objetivos principais.

As 2 intenções, que são demasiado-estreitamente classificadas, podem inverter devido a não determinística treinamento. A classificação superior poderia se tornar a segunda parte superior e a segunda pontuação superior poderia se tornar a classificação superior primeiro. Para evitar esta situação, adicione expressões de exemplo para cada um dos principais duas objetivos para essa expressão com a escolha do word e o contexto que diferencia as 2 intenções. Os dois objetivos devem ter sobre o mesmo número de expressões de exemplo. Um princípio de separação para impedir a inversão devido a formação, é uma diferença de 15% em pontuações.

Pode desativar o treinamento determinística pela [com todos os dados de treinamento](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferenças com predições entre as sessões de treinamento diferentes

Quando treinar o mesmo modelo num aplicativo diferente, e as pontuações não são iguais, essa diferença é porque não existe treinamento determinística (um elemento um pouco da aleatoriedade). Em segundo lugar, a qualquer sobrepõem-se de uma expressão em mais do que uma intenção significa que a intenção de superior para a mesma expressão pode mudar com base na formação.

Se o seu bot de bate-papo exigir uma pontuação de LUIS específica para indicar a confiança numa intenção, deve usar a diferença de pontuação entre os objetivos de duas principais. Esta situação disponibiliza flexibilidade para variações no treinamento.

## <a name="e-exponent-notation"></a>Notação de I (expoente)

Pontuações de predição podem usar a notação expoente, *que aparece* acima 0-1 intervalo, tais como `9.910309E-07`. Esta pontuação é uma indicação de um muito **pequeno** número.

|Pontuação de notação E |Pontuação real|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Pontuação

Pontuação é um token separado no LUIS. Uma expressão que contém um ponto no final em comparação com uma expressão que não contenha um ponto no final são duas expressões separados e poderá receber duas previsões diferentes. Certificar-se de que o modelo processa pontuação ou no [expressões de exemplo](luis-concept-utterance.md) (com e sem pontuação) ou no [padrões](luis-concept-patterns.md) onde é mais fácil Ignorar pontuação com a sintaxe especial: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Passos Seguintes

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.
