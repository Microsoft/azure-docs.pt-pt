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
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280813"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Pontuações de previsão indicam precisão de previsão para intenção e entidades

Uma pontuação de previsão indica o grau de confiança que o LUIS tem para os resultados de previsão de um usuário expressão.

Uma pontuação de predição é entre zero (0) e um (1). Um exemplo de uma pontuação de LUIS elevada confiança será 0,99. Um exemplo de uma pontuação de confiança baixa é a 0,01. 

|Valor de pontuação|Confidence|
|--|--|
|1|correspondência definida|
|0,99|confiança elevada|
|0.01|confiança baixa|
|0|falha definida para corresponder|

## <a name="top-scoring-intent"></a>Intenção da parte superior de classificação

Predição de cada expressão retorna uma intenção da parte superior de classificação. Essa previsão é uma comparação numérica de pontuações de previsão. 

## <a name="proximity-of-scores-to-each-other"></a>Proximidade de pontuações entre si

As duas pontuações principais podem ter uma diferença muito pequena entre elas. LUIS não indica essa proximidade além de retornar a pontuação superior.  

## <a name="return-prediction-score-for-all-intents"></a>Devolver a pontuação de previsão para todos os objetivos

Um resultado de teste ou ponto de extremidade pode incluir todas as intenções. Essa configuração é definida no ponto de extremidade usando o par nome/valor da QueryString correto.

|API de predição|Nome da QueryString|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Reveja os objetivos com pontuações semelhantes

Examinar a pontuação de todas as intenções é uma boa maneira de verificar se não só a intenção correta foi identificada, mas a pontuação da próxima intenção identificada é significativamente e consistentemente menor para declarações.

Se tem de objetivos várias pontuações de predição de fechar, com base no contexto de uma expressão, LUIS pode alternar entre os objetivos. Para corrigir essa situação, continue a adicionar o declarações a cada tentativa com uma variedade maior de diferenças contextuais ou você pode ter o aplicativo cliente, como um bot de bate-papo, fazer escolhas programáticas sobre como lidar com as duas principais intenções.

As duas tentativas, que são pontuadas com muita pontuação, podem ser invertidas devido a **treinamento não determinístico**. A classificação superior poderia se tornar a segunda parte superior e a segunda pontuação superior poderia se tornar a classificação superior primeiro. Para evitar essa situação, adicione um exemplo de declarações a cada uma das duas primeiras intenções para esse expressão com a escolha do Word e o contexto que diferencia as duas intenções. Os dois objetivos devem ter sobre o mesmo número de expressões de exemplo. Um princípio de separação para impedir a inversão devido a formação, é uma diferença de 15% em pontuações.

Você pode desativar o **treinamento não determinístico** ao [treinar todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferenças com previsões entre diferentes sessões de treinamento

Quando você treina o mesmo modelo em um aplicativo diferente e as pontuações não são as mesmas, essa diferença ocorre porque há um **treinamento não determinístico** (um elemento de aleatoriedade). Em segundo lugar, a qualquer sobrepõem-se de uma expressão em mais do que uma intenção significa que a intenção de superior para a mesma expressão pode mudar com base na formação.

Se o seu bot de chat exigir uma pontuação LUIS específica para indicar confiança em uma intenção, você deverá usar a diferença de pontuação entre as duas primeiras intenções. Essa situação fornece flexibilidade para variações no treinamento.

Você pode desativar o **treinamento não determinístico** ao [treinar todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>Notação de I (expoente)

Pontuações de predição podem usar a notação expoente, _que aparece_ acima 0-1 intervalo, tais como `9.910309E-07`. Esta pontuação é uma indicação de um muito **pequeno** número.

|Pontuação de notação E |Pontuação real|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Pontuação

[Saiba mais](luis-concept-utterance.md#punctuation-marks) sobre como usar ou ignorar a pontuação. 

## <a name="next-steps"></a>Passos seguintes

Ver [adicionar entidades](luis-how-to-add-entities.md) para saber mais sobre como adicionar entidades à sua aplicação LUIS.
