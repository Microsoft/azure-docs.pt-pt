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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 198ce98808c8a62a839d154c365518c9e8263056
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619903"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Alterar dados de expressão antes ou durante a predição
LUIS fornece maneiras de manipular a expressão antes ou durante a predição. Isso inclui a [correção ortográfica](luis-tutorial-bing-spellcheck.md)e a correção de problemas de fuso horário para [datetimeV2](luis-reference-prebuilt-datetimev2.md)predefinidos. 

## <a name="correct-spelling-errors-in-utterance"></a>Corrigir erros ortográficos na expressão
Utiliza o LUIS [Bing ortográfica verificar a API V7](../Bing-Spell-Check/overview.md) para corrigir erros ortográficos na expressão. LUIS tem a chave associada esse serviço. Criar a chave, em seguida, adicione a chave como um parâmetro de cadeia de consulta no [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356). 

Também pode corrigir erros ortográficos na **teste** painel por [inserir a chave](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). A chave é mantida como uma variável de sessão no browser para o painel de teste. Adicione a chave para o painel de teste em cada sessão de browser que pretende que a ortografia corrigida. 

Utilização da chave no painel de teste e a contagem de ponto final para o [utilização de chave](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implementa a verificação de ortografia do Bing limites para o comprimento do texto. 

O ponto final requer dois parâmetros para correção ortográfica será trabalhar:

|Param|Valor|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing ortográfica verificar a API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) chave de ponto final|

Quando [Bing ortográfica verificar a API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) Deteta um erro, a expressão original e a expressão corrigido são devolvidos juntamente com predições do ponto final.

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
 
### <a name="list-of-allowed-words"></a>Lista de palavras permitidas
A API de verificação ortográfica do Bing usada em LUIS não dá suporte a uma lista (também chamada de lista de permissões) de palavras a serem ignoradas durante as alterações de verificação ortográfica. Se você precisar permitir uma lista de palavras ou acrônimos, processe o expressão no aplicativo cliente antes de enviar o expressão para LUIS para previsão de intenção.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Altere o fuso horário da entidade de datetimeV2 pré-criados
Quando um aplicativo LUIS usa a entidade [datetimeV2](luis-reference-prebuilt-datetimev2.md) predefinida, um valor DateTime pode ser retornado na resposta de previsão. O fuso horário do pedido é utilizado para determinar a datetime correta para retornar. Se a solicitação é proveniente de um bot ou outra aplicação centralizada antes de aceder à LUIS, corrija o fuso horário que utiliza o LUIS. 

### <a name="endpoint-querystring-parameter"></a>Parâmetro de cadeia de consulta de ponto final
O fuso horário é corrigido adicionando fuso de horário do utilizador para o [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) usando o `timezoneOffset` param. O valor de `timezoneOffset` deve ser o número positivo ou negativo, em minutos, para alterar a hora.  

|Param|Valor|
|--|--|
|`timezoneOffset`|número positivo ou negativo, em minutos|

### <a name="daylight-savings-example"></a>Exemplo de economia de hora de Verão
Se precisar do datetimeV2 pré-criados retornado para ajustar para o horário de Verão, deve usar o `timezoneOffset` parâmetro de cadeia de consulta com um + /-valor em minutos para o [ponto final](https://go.microsoft.com/fwlink/?linkid=2092356) consulta.

Adicione a 60 minutos: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn as luzes acesas? **timezoneOffset = 60**& verboso = {booleano} & verificação ortográfica = {booleano} & de teste = {booleano} & como bing-ortográfica-check-subscription-key = {string} de e & xecução = {booleano}

Remova 60 minutos: 

https://{Region}.API.cognitive.microsoft.com/Luis/v2.0/Apps/{appId}?q=Turn as luzes acesas? **timezoneOffset = a 60**& verboso = {booleano} & verificação ortográfica = {booleano} & de teste = {booleano} & como bing-ortográfica-check-subscription-key = {string} de e & xecução = {booleano}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>O código c# determina o valor correto de timezoneOffset
O código do c# seguinte utiliza a [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) da classe [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) método para determinar o correto `timezoneOffset` com base na hora do sistema:

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Erros de ortografia correta com este tutorial](luis-tutorial-bing-spellcheck.md)
