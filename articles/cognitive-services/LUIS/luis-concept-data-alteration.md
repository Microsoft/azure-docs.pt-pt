---
title: Alteração de dados - LUIS
description: Saiba como os dados podem ser alterados antes das previsões na Compreensão da Linguagem (LUIS)
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 3a88739caa9b35679f10b0cb63a804e9464c871c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872245"
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

### <a name="v3-prediction-api-to-alter-timezone"></a>Previsão V3 API para alterar fuso horário

Em V3, `datetimeReference` o fuso horário compensa. Saiba mais sobre [as previsões v3.](luis-migration-api-v3.md#v3-post-body)

### <a name="v2-prediction-api-to-alter-timezone"></a>Previsão V2 API para alterar fuso horário
O fuso horário é corrigido adicionando o fuso horário `timezoneOffset` do utilizador ao ponto final utilizando o parâmetro com base na versão API. O valor do parâmetro deve ser o número positivo ou negativo, em minutos, para alterar o tempo.

#### <a name="v2-prediction-daylight-savings-example"></a>Exemplo de poupança de verão de previsão V2
Se necessitar do data-data pré-construído devolvidoV2 para se ajustar ao horário de verão, deve utilizar o parâmetro de corda de consulta com um valor de +/- em minutos para a consulta do [ponto final.](https://go.microsoft.com/fwlink/?linkid=2092356)

Adicione 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Retire 60 minutos:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2 previsão C# código determina o valor correto do parâmetro

O seguinte código C# utiliza o método [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) da classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) para determinar o valor correto de compensação com base no tempo do sistema:

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
> [Corrigir erros ortográficos com este tutorial](luis-tutorial-bing-spellcheck.md)
