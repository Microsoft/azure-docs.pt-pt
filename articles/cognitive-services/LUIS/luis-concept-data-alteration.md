---
title: Alteração de dados - LUIS
description: Saiba como os dados podem ser alterados antes das previsões em Compreensão linguística (LUIS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026015"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Alterar os dados de expressão antes ou durante a previsão
LUIS fornece formas de manipular a expressão antes ou durante a previsão. Estes incluem [a fixação de ortografia](luis-tutorial-bing-spellcheck.md)e a fixação de problemas de aso desaporo para datas pré-construídasV2 . [](luis-reference-prebuilt-datetimev2.md)

## <a name="correct-spelling-errors-in-utterance"></a>Erros ortográficos corretos na expressão


### <a name="v3-runtime"></a>Tempo de execução V3

Pré-processo texto para correções ortográficas antes de enviar a expressão para LUIS. Use palavras de exemplo com a ortografia correta para garantir que obtém as previsões corretas.

Utilize [bing Spell Check](../bing-spell-check/overview.md) para corrigir texto antes de enviá-lo para LUIS.

### <a name="prior-to-v3-runtime"></a>Antes do tempo de funcionação da V3

LUIS utiliza [Bing Spell Check API V7](../Bing-Spell-Check/overview.md) para corrigir erros ortográficos na expressão. LUIS precisa da chave associada a este serviço. Crie a chave e, em seguida, adicione a chave como parâmetro de consulta no [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356).

O ponto final requer dois params para correções ortográficas funcionarem:

|Param|Valor|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) chave final|

Quando [bing Spell Check API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) deteta um erro, a expressão original e a expressão corrigida são devolvidas juntamente com as previsões do ponto final.

#### <a name="v2-prediction-endpoint-response"></a>[Resposta do ponto final da previsão V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Resposta do ponto final de previsão V3](#tab/V3)

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
O Bing spell check API usado no LUIS não suporta uma lista de palavras a ignorar durante as alterações de verificação ortográfica. Se precisar de permitir uma lista de palavras ou siglas, processe a expressão na aplicação do cliente antes de enviar a expressão para LUIS para previsão de intenção.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Alterar fuso horário da entidade datatimeV2 pré-construída
Quando uma aplicação LUIS utiliza a entidade [dataV2](luis-reference-prebuilt-datetimev2.md) pré-construída, um valor de data pode ser devolvido na resposta de previsão. O intervalo de tempo do pedido é utilizado para determinar a data correta para a sua devolução. Se o pedido vem de um bot ou de outra aplicação centralizada antes de chegar ao LUIS, corrija o tempo que o LUIS utiliza.

### <a name="v3-prediction-api-to-alter-timezone"></a>API de previsão V3 para alterar o timezone

Em V3, determina `datetimeReference` o intervalo de tempo. Saiba mais sobre [as previsões V3.](luis-migration-api-v3.md#v3-post-body)

### <a name="v2-prediction-api-to-alter-timezone"></a>API de previsão V2 para alterar o timezone
O timezone é corrigido adicionando o timezone do utilizador ao ponto final utilizando o `timezoneOffset` parâmetro baseado na versão API. O valor do parâmetro deve ser o número positivo ou negativo, em minutos, para alterar o tempo.

#### <a name="v2-prediction-daylight-savings-example"></a>V2 previsão luz do dia exemplo
Se necessitar da data pré-construída devolvidaV2 para se ajustar para o horário de verão, deve utilizar o parâmetro de consulta com um valor de +/- em minutos para a consulta do [ponto final.](https://go.microsoft.com/fwlink/?linkid=2092356)

Adicione 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Remover 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>Código de previsão V2 C# determina o valor correto do parâmetro

O seguinte código C# utiliza o método [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) da classe [TimeZone](/dotnet/api/system.timezoneinfo) Para determinar o valor correto de compensação com base no tempo do sistema:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Erros ortográficos corretos com este tutorial](luis-tutorial-bing-spellcheck.md)