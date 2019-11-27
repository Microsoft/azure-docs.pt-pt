---
title: Alteração de dados-LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como os dados podem ser alterados antes de previsões de indisponibilidade na compreensão de idiomas (LUIS)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222029"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Alterar dados de expressão antes ou durante a predição
LUIS fornece maneiras de manipular a expressão antes ou durante a predição. Isso inclui a [correção ortográfica](luis-tutorial-bing-spellcheck.md)e a correção de problemas de fuso horário para [datetimeV2](luis-reference-prebuilt-datetimev2.md)predefinidos. 

## <a name="correct-spelling-errors-in-utterance"></a>Corrigir erros ortográficos na expressão

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


O LUIS [verificação ortográfica do Bing usa a API v7](../Bing-Spell-Check/overview.md) para corrigir erros de ortografia no expressão. LUIS tem a chave associada esse serviço. Crie a chave e, em seguida, adicione a chave como um parâmetro QueryString no [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

O ponto final requer dois parâmetros para correção ortográfica será trabalhar:

|Param|Valor|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Chave de ponto de extremidade [v7 da API verificação ortográfica do Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Quando [verificação ORTOGRÁFICA do Bing API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) detecta um erro, o expressão original e o expressão corrigido são retornados junto com as previsões do ponto de extremidade.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão v2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão v3](#tab/V3)
 
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
A API de verificação ortográfica do Bing usada em LUIS não dá suporte a uma lista de palavras a serem ignoradas durante as alterações de verificação ortográfica. Se você precisar permitir uma lista de palavras ou acrônimos, processe o expressão no aplicativo cliente antes de enviar o expressão para LUIS para previsão de intenção.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Altere o fuso horário da entidade de datetimeV2 pré-criados
Quando um aplicativo LUIS usa a entidade [datetimeV2](luis-reference-prebuilt-datetimev2.md) predefinida, um valor DateTime pode ser retornado na resposta de previsão. O fuso horário do pedido é utilizado para determinar a datetime correta para retornar. Se a solicitação é proveniente de um bot ou outra aplicação centralizada antes de aceder à LUIS, corrija o fuso horário que utiliza o LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parâmetro de cadeia de consulta de ponto final
O fuso horário é corrigido adicionando o fuso horário do usuário ao [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) usando o parâmetro `timezoneOffset`. O valor de `timezoneOffset` deve ser o número positivo ou negativo, em minutos, para alterar a hora.  

|Param|Valor|
|--|--|
|`timezoneOffset`|número positivo ou negativo, em minutos|

### <a name="daylight-savings-example"></a>Exemplo de economia de hora de Verão
Se você precisar que os datetimeV2 predefinidos retornados sejam ajustados para o horário de verão, use o parâmetro `timezoneOffset` QueryString com um valor +/-em minutos para a consulta de [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) .

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 solicitação de ponto de extremidade de previsão](#tab/V2)

Adicione a 60 minutos: 

https://{Region}. API. cognitiva. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = ativar as luzes? **timezoneOffset = 60**& Verbose = {boolean} & verificação ortográfica = {booliano} & preparo = {booliano} & Bing-soletrar-check-Subscription-Key = {string} & log = {Boolean}

Remova 60 minutos: 

https://{Region}. API. cognitiva. Microsoft. com/Luis/v 2.0/Apps/{appId}? q = ativar as luzes? **timezoneOffset =-60**& Verbose = {boolean} & verificação ortográfica = {booliano} & preparo = {booliano} & Bing-soletrar-check-Subscription-Key = {string} & log = {Boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Solicitação de ponto de extremidade de previsão v3](#tab/V3)

Adicione a 60 minutos:

https://{região}. API. cognitiva. Microsoft. com/Luis/v 3.0-Preview/apps/{appId}/Slots/produção/previsão? consulta = ativar as luzes? **timezoneOffset = 60**& verificação ortográfica = {booliano} & Bing-grafia-check-Subscription-Key = {string} & log = {Boolean}

Remova 60 minutos: 

https://{região}. API. cognitiva. Microsoft. com/Luis/v 3.0-Preview/apps/{appId}/Slots/produção/previsão? consulta = ativar as luzes? **timezoneOffset =-60**& verificação ortográfica = {booliano} & Bing-grafia-check-Subscription-Key = {string} & log = {Boolean}

Saiba mais sobre o [ponto de extremidade de previsão v3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>O código c# determina o valor correto de timezoneOffset
O código C# a seguir usa o método [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) da classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) para determinar o `timezoneOffset` correto com base na hora do sistema:

```CSharp
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
> [Corrigir erros de ortografia neste tutorial](luis-tutorial-bing-spellcheck.md)
