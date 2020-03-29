---
title: Alteração de dados - LUIS
description: Saiba como os dados podem ser alterados antes das previsões na Compreensão da Linguagem (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b3b36351a64a4e1a0bd13d5785a4e0609a80901d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292064"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Alterar os dados de expressão antes ou durante a previsão
Luis fornece formas de manipular a expressão antes ou durante a previsão. Estes incluem [a fixação](luis-tutorial-bing-spellcheck.md)de ortografias e a fixação de problemas de fuso horário para data pré-construídaV2 . [datetimeV2](luis-reference-prebuilt-datetimev2.md)

## <a name="correct-spelling-errors-in-utterance"></a>Corrigir erros ortográficos na expressão


### <a name="v3-runtime"></a>Tempo de execução V3

Pré-processe o texto para correções ortográficas antes de enviar a expressão para o LUIS. Utilize as palavras de exemplo com a ortografia correta para garantir que obtém as previsões corretas.

Utilize [bing spell Check](../bing-spell-check/overview.md) para corrigir texto antes de enviá-lo para LUIS.

### <a name="prior-to-v3-runtime"></a>Antes do tempo de execução v3

Luis usa [Bing Spell Check API V7](../Bing-Spell-Check/overview.md) para corrigir erros ortográficos na expressão. Luis precisa da chave associada a esse serviço. Crie a tecla e adicione a chave como parâmetro de corda de consulta no [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356).

O ponto final requer dois parâmetros para correções ortográficas para funcionar:

|Param|Valor|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Tecla final do [Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Quando [bing spell check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) deteta um erro, a expressão original, e a expressão corrigida são devolvidas juntamente com previsões do ponto final.

#### <a name="v2-prediction-endpoint-response"></a>[Resposta final de previsão V2](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta final de previsão V3](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Lista de palavras permitidas
A verificação de feitiços Bing API utilizada no LUIS não suporta uma lista de palavras a ignorar durante as alterações de verificação ortográfica. Se precisar de permitir uma lista de palavras ou siglas, processe a expressão na aplicação do cliente antes de enviar a expressão para a LUIS para previsão de intenções.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Alterar o fuso horário da entidade datav2 pré-construída
Quando uma aplicação LUIS utiliza a entidade [datav2](luis-reference-prebuilt-datetimev2.md) pré-construída, um valor de data pode ser devolvido na resposta à previsão. O fuso horário do pedido é utilizado para determinar a hora de regresso correta. Se o pedido vier de um bot ou de outra aplicação centralizada antes de chegar ao LUIS, corrija o fuso horário que o LUIS utiliza.

### <a name="endpoint-querystring-parameter"></a>Parâmetro de corda de consulta de ponto final
O fuso horário é corrigido adicionando o fuso horário `timezoneOffset` do utilizador ao ponto [final](https://go.microsoft.com/fwlink/?linkid=2092356) utilizando o param. O valor `timezoneOffset` deve ser o número positivo ou negativo, em minutos, para alterar o tempo.

|Param|Valor|
|--|--|
|`timezoneOffset`|número positivo ou negativo, em minutos|

### <a name="daylight-savings-example"></a>Exemplo de poupança de luz do dia
Se necessitar do data-data pré-construído devolvidoV2 para se `timezoneOffset` ajustar ao horário de verão, deve utilizar o parâmetro de corda de consulta com um valor de +/- em minutos para a consulta do [ponto final.](https://go.microsoft.com/fwlink/?linkid=2092356)

#### <a name="v2-prediction-endpoint-request"></a>[Pedido de ponto final de previsão V2](#tab/V2)

Adicione 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Retire 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v3-prediction-endpoint-request"></a>[Pedido de ponto final de previsão V3](#tab/V3)

Adicione 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Retire 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Código C# determina o valor correto do timezoneOffset
O seguinte código C# utiliza o método [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) da `timezoneOffset` classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) para determinar o correto com base no tempo do sistema:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Corrigir erros ortográficos com este tutorial](luis-tutorial-bing-spellcheck.md)
