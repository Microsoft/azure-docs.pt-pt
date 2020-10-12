---
title: Compreender filtros de recolha OData
titleSuffix: Azure Cognitive Search
description: Conheça a mecânica de como os filtros de recolha OData funcionam em consultas de Pesquisa Cognitiva Azure, incluindo limitações e comportamentos exclusivos das coleções.
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
ms.openlocfilehash: 6af0f2b5221a737687578e939c14cecf3be14509
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932921"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Compreender filtros de recolha OData em Pesquisa Cognitiva Azure

Para [filtrar](query-odata-filter-orderby-syntax.md) em campos de recolha em Azure Cognitive Search, pode utilizar os [ `any` operadores e `all` operadores](search-query-odata-collection-operators.md) juntamente com **expressões lambda.** Expressões lambda são expressões booleanas que se referem a uma **variável de gama.** Os `any` `all` operadores são análogos a um `for` loop na maioria das linguagens de programação, com a variável de gama assumindo o papel de variável loop, e a expressão lambda como o corpo do loop. A variável de gama assume o valor "atual" da coleção durante a iteração do laço.

Pelo menos é assim que funciona conceptualmente. Na realidade, a Azure Cognitive Search implementa filtros de uma forma muito diferente de como `for` os loops funcionam. Idealmente, esta diferença seria invisível para ti, mas em certas situações não é. O resultado final é que há regras que tens de seguir ao escrever expressões de lambda.

Este artigo explica porque existem as regras para filtros de recolha explorando como a Azure Cognitive Search executa estes filtros. Se estiver a escrever filtros avançados com expressões de lambda complexas, poderá achar este artigo útil para construir a sua compreensão do que é possível nos filtros e porquê.

Para obter informações sobre quais são as regras dos filtros de recolha, incluindo exemplos, consulte [os filtros de recolha OData de resolução de problemas em Azure Cognitive Search](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Por que os filtros de coleção são limitados

Existem três razões subjacentes para que nem todas as funcionalidades de filtro sejam suportadas para todos os tipos de coleções:

1. Apenas certos operadores são suportados para determinados tipos de dados. Por exemplo, não faz sentido comparar os valores booleano `true` e usar , e assim por `false` `lt` `gt` diante.
1. A Azure Cognitive Search não suporta **pesquisas correlacionadas** em campos do tipo `Collection(Edm.ComplexType)` .
1. A Azure Cognitive Search utiliza índices invertidos para executar filtros em todos os tipos de dados, incluindo recolhas.

A primeira razão é apenas uma consequência de como a linguagem OData e o sistema de tipo EDM são definidos. Os dois últimos são explicados mais detalhadamente no resto deste artigo.

## <a name="correlated-versus-uncorrelated-search"></a>Pesquisa correlacionada versus não correlacionada

Ao aplicar vários critérios de filtro sobre uma coleção de objetos complexos, os critérios são **correlacionados** uma vez que se aplicam a *cada objeto na coleção*. Por exemplo, o seguinte filtro irá devolver hotéis que tenham pelo menos um quarto de luxo com uma taxa inferior a 100:

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

Se a filtragem não foi *correlacionada,* o filtro acima pode devolver hotéis onde um quarto é de luxo e um quarto diferente tem uma taxa base inferior a 100. Isso não faria sentido, uma vez que ambas as cláusulas da expressão lambda se aplicam à mesma variável de gama, ou `room` seja. É por isso que estes filtros estão correlacionados.

No entanto, para uma pesquisa de texto completo, não há como se referir a uma variável de alcance específica. Se utilizar uma pesquisa de campo para emitir uma [consulta lucene completa](query-lucene-syntax.md) como esta:

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

você pode obter hotéis de volta onde um quarto é de luxo, e um quarto diferente menciona "vista da cidade" na descrição. Por exemplo, o documento abaixo `Id` com o de `1` corresponderia à consulta:

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

A razão é que `Rooms/Type` se refere a todos os termos analisados do campo em todo o `Rooms/Type` documento, e similarmente para `Rooms/Description` , como mostrado nas tabelas abaixo.

Como `Rooms/Type` é armazenado para obter uma pesquisa completa:

| Prazo em `Rooms/Type` | IDs de documento |
| --- | --- |
| de luxo | 1, 2 |
| padrão | 1 |

Como `Rooms/Description` é armazenado para obter uma pesquisa completa:

| Prazo em `Rooms/Description` | IDs de documento |
| --- | --- |
| pátio | 2 |
| city | 1 |
| jardim | 1 |
| grande | 1 |
| motel | 2 |
| sala | 1, 2 |
| padrão | 1 |
| suite | 1 |
| ver | 1 |

Assim, ao contrário do filtro acima, que basicamente diz "documentos de correspondência onde um quarto tem `Type` igual a 'Deluxe Room' e **esse mesmo quarto** tem menos de `BaseRate` 100", a consulta de pesquisa diz "documentos de correspondência onde `Rooms/Type` tem o termo "deluxe" e tem a frase "vista da `Rooms/Description` cidade". Não há noção de quartos individuais cujos campos podem ser correlacionados neste último caso.

> [!NOTE]
> Se quiser ver suporte para pesquisa correlacionada adicionada à Pesquisa Cognitiva Azure, por favor vote [neste item User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Índices e coleções invertidos

Já deve ter reparado que há muito menos restrições nas expressões de lambda sobre coleções complexas do que para coleções simples como `Collection(Edm.Int32)` , e assim por `Collection(Edm.GeographyPoint)` diante. Isto porque a Azure Cognitive Search armazena coleções complexas como coleções reais de sub-documentos, enquanto as coleções simples não são armazenadas como coleções.

Por exemplo, considere um campo de recolha de cordas filtrado como `seasons` num índice para um retalhista online. Alguns documentos enviados para este índice podem ser assim:

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

Os valores do `seasons` campo são armazenados numa estrutura chamada **índice invertido,** que se parece com isto:

| Termo | IDs de documento |
| --- | --- |
| primavera | 1, 2 |
| verão | 1 |
| queda | 1, 2 |
| inverno | 2, 3 |

Esta estrutura de dados destina-se a responder a uma pergunta com grande rapidez: Em que documentos aparece um determinado termo? Responder a esta pergunta funciona mais como um simples cheque da igualdade do que um loop sobre uma coleção. Na verdade, é por isso que para coleções de cordas, a Azure Cognitive Search só permite `eq` como um operador de comparação dentro de uma expressão lambda para `any` .

Construindo a partir da igualdade, em seguida vamos ver como é possível combinar múltiplos controlos de igualdade na mesma variável de gama com `or` . Funciona graças à álgebra e [à propriedade distributiva dos quantificadores.](https://en.wikipedia.org/wiki/Existential_quantification#Negation) Esta expressão:

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

equivale a:

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

e cada uma das `any` duas subexpressões pode ser executada de forma eficiente usando o índice invertido. Além disso, graças [à lei de negação dos quantificadores,](https://en.wikipedia.org/wiki/Existential_quantification#Negation)esta expressão:

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

equivale a:

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

e é por isso que é possível usar `all` com `ne` e `and` .

> [!NOTE]
> Embora os detalhes estejam fora do âmbito deste documento, estes mesmos princípios [estendem-se aos testes de distância e intersecção para recolhas de pontos geo-espaciais](search-query-odata-geo-spatial-functions.md) também. É por isso que, `any` em:
>
> - `geo.intersects` não pode ser negado
> - `geo.distance` deve ser comparado usando `lt` ou `le`
> - expressões devem ser combinadas com `or` , não `and`
>
> As regras inversas aplicam-se `all` a .

Uma maior variedade de expressões são permitidas na filtragem de coleções de tipos de dados que suportam, `lt` `gt` e `le` `ge` operadores, como `Collection(Edm.Int32)` por exemplo. Especificamente, pode utilizar `and` assim como em , desde que as `or` `any` expressões de comparação subjacentes sejam combinadas em **comparações de gama** utilizando , que são depois `and` combinadas com a utilização `or` . Esta estrutura de expressões booleanas é chamada [formulário normal disjuntivo (DNF),](https://en.wikipedia.org/wiki/Disjunctive_normal_form)também conhecido como "ORs of ANDs". Inversamente, as expressões de lambda para `all` estes tipos de dados devem estar na Forma [Normal Conjuntiva (CNF),](https://en.wikipedia.org/wiki/Conjunctive_normal_form)também conhecidas como "ANDs de RAM". A Azure Cognitive Search permite tais comparações de alcance porque pode executá-las usando índices invertidos de forma eficiente, assim como pode fazer pesquisa de fim rápido para cordas.

Em resumo, aqui estão as regras do polegar para o que é permitido numa expressão lambda:

- No `any` interior, são sempre *permitidos controlos positivos,* como igualdade, comparações `geo.intersects` de alcance, ou `geo.distance` comparados com ou `lt` `le` (pense na "proximidade" como sendo igualdade quando se trata de verificar distâncias).
- Por `any` dentro, `or` é sempre permitido. Só pode utilizar `and` para tipos de dados que possam expressar verificações de alcance, e apenas se utilizar ORs de ANDs (DNF).
- No `all` interior, as regras são invertidas -- só são *permitidos controlos negativos,* pode `and` utilizar-se sempre, e só pode utilizar `or` para verificações de alcance expressas em ANDs de ORs (CNF).

Na prática, estes são os tipos de filtros que é mais provável que use de qualquer maneira. Ainda é útil entender os limites do que é possível.

Para exemplos específicos de quais tipos de filtros são permitidos e que não são, consulte [Como escrever filtros de recolha válidos](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Passos seguintes  

- [Resolução de problemas filtros de recolha OData em Pesquisa Cognitiva Azure](search-query-troubleshoot-collection-filters.md)
- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)