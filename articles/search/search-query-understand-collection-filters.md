---
title: Noções básicas sobre filtros de coleção OData
titleSuffix: Azure Cognitive Search
description: Aprenda a mecânica de como os filtros de coleta do OData funcionam no Azure Pesquisa Cognitiva consultas, incluindo limitações e comportamentos exclusivos de coleções.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113066"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Noções básicas sobre filtros de coleção OData no Azure Pesquisa Cognitiva

Para [Filtrar](query-odata-filter-orderby-syntax.md) os campos de coleção no Azure pesquisa cognitiva, você pode usar os [operadores`any` e `all`](search-query-odata-collection-operators.md) juntos com **expressões lambda**. As expressões lambda são expressões booleanas que se referem a uma **variável de intervalo**. Os operadores de `any` e `all` são análogos a um loop de `for` na maioria das linguagens de programação, com a variável de intervalo que assume a função da variável de loop e a expressão lambda como o corpo do loop. A variável de intervalo assume o valor "Current" da coleção durante a iteração do loop.

Pelo menos é assim que funciona conceitualmente. Na realidade, o Azure Pesquisa Cognitiva implementa filtros de uma maneira muito diferente de como `for` loops funcionam. O ideal é que essa diferença seja invisível para você, mas em determinadas situações ela não é. O resultado final é que há regras que você precisa seguir ao escrever expressões lambda.

Este artigo explica por que as regras para filtros de coleta existem explorando como o Azure Pesquisa Cognitiva executa esses filtros. Se você estiver escrevendo filtros avançados com expressões lambda complexas, poderá encontrar este artigo útil para criar sua compreensão do que é possível fazer em filtros e por quê.

Para obter informações sobre quais são as regras para filtros de coleta, incluindo exemplos, consulte [solução de problemas de filtros de coleção OData no Azure pesquisa cognitiva](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Por que os filtros de coleção são limitados

Há três motivos subjacentes pelos quais nem todos os recursos de filtro têm suporte para todos os tipos de coleções:

1. Somente determinados operadores têm suporte para determinados tipos de dados. Por exemplo, não faz sentido comparar os valores Boolianos `true` e `false` usando `lt`, `gt`e assim por diante.
1. O Azure Pesquisa Cognitiva não dá suporte à **pesquisa correlacionada** em campos do tipo `Collection(Edm.ComplexType)`.
1. O Azure Pesquisa Cognitiva usa índices invertidos para executar filtros em todos os tipos de dados, incluindo coleções.

A primeira razão é apenas uma conseqüência de como o sistema de tipos de linguagem OData e EDM é definido. Os dois últimos são explicados com mais detalhes no restante deste artigo.

## <a name="correlated-versus-uncorrelated-search"></a>Pesquisa correlacionada versus não correlacionada

Ao aplicar vários critérios de filtro em uma coleção de objetos complexos, os critérios são **correlacionados** , pois se aplicam a *cada objeto na coleção*. Por exemplo, o filtro a seguir retornará hotéis que têm pelo menos uma sala de luxo com uma taxa menor que 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se a filtragem não estiver *correlacionada*, o filtro acima poderá retornar Hotéis em que uma sala é Deluxe e uma sala diferente tem uma taxa base menor que 100. Isso não faria sentido, pois ambas as cláusulas da expressão lambda se aplicam à mesma variável de intervalo, ou seja `room`. É por isso que esses filtros estão correlacionados.

No entanto, para pesquisa de texto completo, não há como fazer referência a uma variável de intervalo específica. Se você usar a pesquisa de campo para emitir uma [consulta Lucene completa](query-lucene-syntax.md) como esta:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

Você pode obter Hotéis de volta onde uma sala é Deluxe e uma sala diferente menciona "exibição de cidade" na descrição. Por exemplo, o documento abaixo com `Id` de `1` corresponderia à consulta:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

O motivo é que `Rooms/Type` se refere a todos os termos analisados do campo `Rooms/Type` no documento inteiro e, da mesma forma, para `Rooms/Description`, conforme mostrado nas tabelas abaixo.

Como `Rooms/Type` é armazenado para a pesquisa de texto completo:

| Termo em `Rooms/Type` | IDs de documento |
| --- | --- |
| Deluxe | 1, 2 |
| standard | 1 |

Como `Rooms/Description` é armazenado para a pesquisa de texto completo:

| Termo em `Rooms/Description` | IDs de documento |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| jardim | 1 |
| vários | 1 |
| Motel | 2 |
| espaço | 1, 2 |
| standard | 1 |
| aplicações | 1 |
| ver | 1 |

Ao contrário do filtro acima, que basicamente diz "corresponder documentos onde um quarto tem `Type` igual a" sala de luxo "e **que o mesmo espaço** tem `BaseRate` menos de 100", a consulta de pesquisa indica "corresponder documentos onde `Rooms/Type` tem o termo" Deluxe "e `Rooms/Description` tem a frase" cidade de exibição ". Não há nenhum conceito de salas individuais cujos campos podem ser correlacionados no último caso.

> [!NOTE]
> Se você quiser ver o suporte para a pesquisa correlacionada adicionada ao Pesquisa Cognitiva do Azure, vote [neste item de voz do usuário](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Índices e coleções invertidos

Você pode ter notado que há muito menos restrições em expressões lambda em coleções complexas do que para coleções simples, como `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`e assim por diante. Isso ocorre porque o Azure Pesquisa Cognitiva armazena coleções complexas como coleções reais de subdocumentos, enquanto as coleções simples não são armazenadas como coleções.

Por exemplo, considere um campo de coleção de cadeia de caracteres filtrável como `seasons` em um índice para um varejista online. Alguns documentos carregados nesse índice podem ter a seguinte aparência:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Os valores do campo `seasons` são armazenados em uma estrutura chamada de **índice invertido**, que se parece com isto:

| Termo | IDs de documento |
| --- | --- |
| surge | 1, 2 |
| Verão | 1 |
| encontram | 1, 2 |
| fim | 2, 3 |

Essa estrutura de dados foi projetada para responder a uma pergunta com grande velocidade: em quais documentos um determinado termo aparece? Responder a essa pergunta funciona mais como uma verificação de igualdade simples do que um loop em uma coleção. Na verdade, é por isso que, para coleções de cadeias de caracteres, o Azure Pesquisa Cognitiva permite apenas `eq` como um operador de comparação dentro de uma expressão lambda para `any`.

Criando a partir de igualdade, veremos como é possível combinar várias verificações de igualdade na mesma variável de intervalo com `or`. Ele funciona graças a Algebra e [à propriedade distributivo de quantificadores](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Esta expressão:

    seasons/any(s: s eq 'winter' or s eq 'fall')

é equivalente a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

e cada uma das duas subexpressãos `any` pode ser executada com eficiência usando o índice invertido. Além disso, graças à [lei de negação de quantificadores](https://en.wikipedia.org/wiki/Existential_quantification#Negation), esta expressão:

    seasons/all(s: s ne 'winter' and s ne 'fall')

é equivalente a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

é por isso que é possível usar `all` com `ne` e `and`.

> [!NOTE]
> Embora os detalhes estejam além do escopo deste documento, esses mesmos princípios se estendem a [testes de distância e interseção para coleções de pontos geoespaciais](search-query-odata-geo-spatial-functions.md) também. É por isso que, no `any`:
>
> - `geo.intersects` não pode ser negado
> - `geo.distance` deve ser comparado usando `lt` ou `le`
> - as expressões devem ser combinadas com `or`, não `and`
>
> As regras de inverso se aplicam para `all`.

Uma variedade maior de expressões é permitida ao filtrar em coleções de tipos de dados que dão suporte aos operadores `lt`, `gt`, `le`e `ge`, como `Collection(Edm.Int32)`, por exemplo. Especificamente, você pode usar `and`, bem como `or` no `any`, desde que as expressões de comparação subjacente sejam combinadas em **comparações de intervalo** usando `and`, que são posteriormente combinadas usando `or`. Essa estrutura de expressões booleanas é chamada de [DNF (Disjunctive Normal Form)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), também conhecido como "ORS de ands". Por outro lado, as expressões lambda para `all` para esses tipos de dados devem estar no [conjuntiva normal Form (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), também conhecido como "ands do ORS". O Azure Pesquisa Cognitiva permite comparações de intervalo, pois pode executá-las usando índices invertidos com eficiência, assim como pode fazer uma pesquisa rápida de termo para cadeias de caracteres.

Em resumo, aqui estão as regras gerais para o que é permitido em uma expressão lambda:

- Dentro de `any`, as *verificações positivas* sempre são permitidas, como igualdade, comparações de intervalo, `geo.intersects`ou `geo.distance` comparadas com `lt` ou `le` (considere a "proximidade" como igualdade quando se trata de verificar a distância).
- Dentro de `any`, `or` sempre é permitido. Você pode usar `and` apenas para tipos de dados que podem expressar verificações de intervalo e apenas se usar o ORs de ANDs (DNF).
- Dentro de `all`, as regras são revertidas-somente *verificações negativas* são permitidas, você pode usar `and` sempre, e você pode usar `or` somente para verificações de intervalo expressas como ands do ORS (CNF).

Na prática, esses são os tipos de filtros que você provavelmente usará de qualquer forma. Ainda é útil entender os limites do que é possível no entanto.

Para obter exemplos específicos de quais tipos de filtros são permitidos e quais não são, consulte [como escrever filtros de coleção válidos](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Passos seguintes  

- [Solução de problemas de filtros de coleção OData no Azure Pesquisa Cognitiva](search-query-troubleshoot-collection-filters.md)
- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
