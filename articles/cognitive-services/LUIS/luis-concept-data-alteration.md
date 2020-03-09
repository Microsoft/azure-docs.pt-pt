---
title: Alteração de dados - LUIS
description: Saiba como os dados podem ser alterados antes de previsões de indisponibilidade na compreensão de idiomas (LUIS)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 5547724a6333d248a7ba4e9aeecaaa8f331feb7d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361188"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Alterar dados de expressão antes ou durante a predição
LUIS fornece maneiras de manipular a expressão antes ou durante a predição. Estes incluem [a fixação](luis-tutorial-bing-spellcheck.md)de ortografias e a fixação de problemas de fuso horário para data pré-construídaV2 . [](luis-reference-prebuilt-datetimev2.md)

## <a name="correct-spelling-errors-in-utterance"></a>Corrigir erros ortográficos na expressão


### <a name="v3-runtime"></a>Tempo de execução V3

Pré-processe o texto para correções ortográficas antes de enviar a expressão para o LUIS. Utilize as palavras de exemplo com a ortografia correta para garantir que obtém as previsões corretas.

Utilize [bing spell Check](../bing-spell-check/overview.md) para corrigir texto antes de enviá-lo para LUIS.

### <a name="prior-to-v3-runtime"></a>Antes do tempo de execução v3

Luis usa [Bing Spell Check API V7](../Bing-Spell-Check/overview.md) para corrigir erros ortográficos na expressão. LUIS tem a chave associada esse serviço. Crie a tecla e adicione a chave como parâmetro de corda de consulta no [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356).

O ponto final requer dois parâmetros para correção ortográfica será trabalhar:

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

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Altere o fuso horário da entidade de datetimeV2 pré-criados
Quando uma aplicação LUIS utiliza a entidade [datav2](luis-reference-prebuilt-datetimev2.md) pré-construída, um valor de data pode ser devolvido na resposta à previsão. O fuso horário do pedido é utilizado para determinar a datetime correta para retornar. Se a solicitação é proveniente de um bot ou outra aplicação centralizada antes de aceder à LUIS, corrija o fuso horário que utiliza o LUIS.

### <a name="endpoint-querystring-parameter"></a>Parâmetro de cadeia de consulta de ponto final
O fuso horário é corrigido adicionando o fuso horário do utilizador ao [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) utilizando o `timezoneOffset` param. O valor da `timezoneOffset` deve ser o número positivo ou negativo, em minutos, para alterar o tempo.

|Param|Valor|
|--|--|
|`timezoneOffset`|número positivo ou negativo, em minutos|

### <a name="daylight-savings-example"></a>Exemplo de economia de hora de Verão
Se necessitar do data-data pré-construído devolvidoV2 para se ajustar ao horário de verão, deve utilizar o parâmetro de `timezoneOffset` consulta string com um valor de +/- em minutos para a consulta do [ponto final.](https://go.microsoft.com/fwlink/?linkid=2092356)

#### <a name="v2-prediction-endpoint-request"></a>[Pedido de ponto final de previsão V2](#tab/V2)

Adicione a 60 minutos:

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=acender as luzes? **timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscrição-key={string}&log={boolean}

Remova 60 minutos:

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=acender as luzes? **timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscrição-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-request"></a>[Pedido de ponto final de previsão V3](#tab/V3)

Adicione a 60 minutos:

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?question?question=acende as luzes? **timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Remova 60 minutos:

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?question?question=acende as luzes? **timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Saiba mais sobre o ponto final da [previsão V3](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>O código c# determina o valor correto de timezoneOffset
O C# seguinte código utiliza o método [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) da classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) para determinar o `timezoneOffset` correto com base no tempo do sistema:

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
