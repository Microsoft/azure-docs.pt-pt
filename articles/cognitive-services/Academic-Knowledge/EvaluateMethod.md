---
title: Avaliar método – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o método Evaluate para retornar um conjunto de entidades acadêmicas com base em uma expressão de consulta.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 69e701d6727e5410b71e6cf8fbe20a1cd038ddb0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705011"
---
# <a name="evaluate-method"></a>Método Evaluate

A API de **avaliação** REST é usada para retornar um conjunto de entidades acadêmicas com base em uma expressão de consulta.
<br>

**Ponto de extremidade REST:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Parâmetros do Pedido  

Nome     | Value | Obrigatório?  | Descrição
-----------|-----------|---------|--------
**expr**       | Cadeia de texto | Sim | Uma expressão de consulta que especifica quais entidades devem ser retornadas.
**deprecia**      | Cadeia de texto | Não  | Nome do modelo que você deseja consultar.  Atualmente, o valor padrão é o *mais recente*.        
**atributos** | Cadeia de texto | Não<br>os ID | Uma lista delimitada por vírgula que especifica os valores de atributo que são incluídos na resposta. Os nomes de atributo diferenciam maiúsculas de minúsculas.
**count**        | Número | Não<br>Predefinição: 10 | Número de resultados a serem retornados.
**offset**     | Número |   Não<br>Predefinição: 0    | Índice do primeiro resultado a ser retornado.
**OrderBy** |   Cadeia de texto | Não<br>Padrão: ao diminuir o prob | Nome de um atributo que é usado para classificar as entidades. Opcionalmente, crescente/decrescente pode ser especificado. O formato é: *Name: ASC* ou *Name: desc*.
  
 <br>

## <a name="response-json"></a>Resposta (JSON)

Nome | Descrição
-------|-----   
**expr** |  O parâmetro *expr* da solicitação.
**contabilidade** |  Uma matriz de 0 ou mais entidades que corresponderam à expressão de consulta. Cada entidade contém um valor de probabilidade de log natural e os valores de outros atributos solicitados.
**anulada** | True se a solicitação atingir o tempo limite.

<br>

#### <a name="example"></a>Exemplo:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Normalmente, uma expressão será Obtida de uma resposta para o método **interpret** .  Mas você pode também compor expressões de consulta por conta própria (consulte [sintaxe de expressão de consulta](QueryExpressionSyntax.md)).  
  
Usando os parâmetros *Count* e *offset* , um grande número de resultados pode ser obtido sem o envio de uma única solicitação que resulta em uma resposta enorme (e potencialmente lenta).  Neste exemplo, a solicitação usou a expressão para a primeira interpretação da resposta da API de **interpretação** como o valor de *expr* . O parâmetro *Count = 2* especifica que dois resultados da entidade estão sendo solicitados. E os *atributos = ti, Y, CC, AA. AuN, AA.* O parâmetro AuId indica que o título, ano, contagem de citação, nome do autor e ID do autor são solicitados para cada resultado.  Consulte [atributos de entidade](EntityAttributes.md) para obter uma lista de atributos.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
