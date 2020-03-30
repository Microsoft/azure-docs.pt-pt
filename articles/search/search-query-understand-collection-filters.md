---
title: Compreender filtros de recolha oData
titleSuffix: Azure Cognitive Search
description: Saiba como funcionam os filtros de recolha oData em consultas de Pesquisa Cognitiva Azure, incluindo limitações e comportamentos exclusivos das coleções.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113066"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Compreender filtros de recolha oData em Pesquisa Cognitiva Azure

Para [filtrar](query-odata-filter-orderby-syntax.md) em campos de recolha em Azure Cognitive Search, pode utilizar os [ `any` e `all` operadores](search-query-odata-collection-operators.md) juntamente com **expressões de lambda.** As expressões de Lambda são expressões booleanas que se referem a uma **variável de gama.** Os `any` `all` operadores e operadores `for` são análogos a um ciclo na maioria das linguagens de programação, com a variável gama assumindo o papel de variável loop, e a expressão lambda como o corpo do loop. A variável gama assume o valor "atual" da coleção durante a iteração do loop.

Pelo menos é assim que funciona conceptualmente. Na realidade, a Azure Cognitive Search implementa filtros de uma forma muito diferente de como `for` os loops funcionam. Idealmente, esta diferença seria invisível para ti, mas em certas situações não é. O resultado final é que há regras que tem de seguir ao escrever expressões de lambda.

Este artigo explica porque é que as regras para filtros de recolha existem explorando como a Pesquisa Cognitiva Azure executa estes filtros. Se estiver a escrever filtros avançados com expressões complexas de lambda, poderá achar este artigo útil para construir a sua compreensão do que é possível nos filtros e porquê.

Para obter informações sobre quais são as regras para filtros de recolha, incluindo exemplos, consulte filtros de [recolha de OData de resolução de problemas em Pesquisa Cognitiva Azure](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Por que os filtros de recolha são limitados

Existem três razões subjacentes para que nem todas as funcionalidades do filtro sejam suportadas para todos os tipos de coleções:

1. Apenas certos operadores são apoiados para determinados tipos de dados. Por exemplo, não faz sentido comparar os `true` valores `lt` `gt`booleanos e `false` usar, e assim por diante.
1. A Pesquisa Cognitiva Azure não suporta pesquisas `Collection(Edm.ComplexType)` **correlacionadas** em campos de tipo .
1. A Azure Cognitive Search utiliza índices invertidos para executar filtros sobre todos os tipos de dados, incluindo coleções.

A primeira razão é apenas uma consequência de como o sistema de idioma OData e edm são definidos. Os dois últimos são explicados mais detalhadamente no resto deste artigo.

## <a name="correlated-versus-uncorrelated-search"></a>Pesquisa correlacionada versus não relacionada

Ao aplicar múltiplos critérios de filtro sobre uma coleção de objetos complexos, os critérios são **correlacionados** uma vez que se aplicam a *cada objeto na recolha*. Por exemplo, o filtro seguinte devolverá hotéis que tenham pelo menos um quarto de luxo com uma taxa inferior a 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Se a filtragem não foi *correlacionada,* o filtro acima pode devolver hotéis onde um quarto é de luxo e um quarto diferente tem uma taxa base inferior a 100. Isso não faria sentido, uma vez que ambas as cláusulas da expressão `room`lambda se aplicam à mesma variável de gama, nomeadamente. É por isso que estes filtros estão correlacionados.

No entanto, para pesquisa de texto completo, não há como se referir a uma variável de gama específica. Se usar a pesquisa no terreno para emitir uma [consulta lucene completa](query-lucene-syntax.md) como esta:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

você pode obter hotéis de volta onde um quarto é de luxo, e um quarto diferente menciona "vista da cidade" na descrição. Por exemplo, o `Id` documento `1` abaixo com de corresponder à consulta:

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

A razão `Rooms/Type` é que se refere `Rooms/Type` a todos os termos analisados `Rooms/Description`do campo em todo o documento, e da mesma forma para, como mostram as tabelas abaixo.

Como `Rooms/Type` é armazenado para pesquisa de texto completo:

| Termo em`Rooms/Type` | IDs de documento |
| --- | --- |
| deluxo | 1, 2 |
| padrão | 1 |

Como `Rooms/Description` é armazenado para pesquisa de texto completo:

| Termo em`Rooms/Description` | IDs de documento |
| --- | --- |
| pátio | 2 |
| city | 1 |
| jardim | 1 |
| grande | 1 |
| motel | 2 |
| sala | 1, 2 |
| padrão | 1 |
| suíte | 1 |
| ver | 1 |

Assim, ao contrário do filtro acima, que basicamente diz "combinar documentos onde uma sala `Type` tem igual a 'Deluxe Room' e **essa mesma sala** tem `BaseRate` menos de 100", a consulta de pesquisa diz "documentos de correspondência onde `Rooms/Type` tem o termo "deluxe" e `Rooms/Description` tem a frase "vista da cidade". Não há nenhum conceito de salas individuais cujos campos podem ser correlacionados neste último caso.

> [!NOTE]
> Se quiser ver suporte para pesquisa correlacionada adicionada à Pesquisa Cognitiva Azure, por favor vote [neste item de Voz do Utilizador](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Índices e coleções invertidos

Você deve ter notado que há muito menos restrições em expressões de lambda `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)`sobre coleções complexas do que para coleções simples como , e assim por diante. Isto porque a Azure Cognitive Search armazena coleções complexas como coleções reais de sub-documentos, enquanto coleções simples não são armazenadas como coleções.

Por exemplo, considere um campo `seasons` de recolha de cordas filtravel como num índice para um retalhista online. Alguns documentos enviados para este índice podem parecer assim:

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

Os valores `seasons` do campo são armazenados numa estrutura chamada **índice invertido,** que se parece com isto:

| Termo | IDs de documento |
| --- | --- |
| primavera | 1, 2 |
| verão | 1 |
| queda | 1, 2 |
| inverno | 2, 3 |

Esta estrutura de dados destina-se a responder a uma pergunta com grande rapidez: Em que documentos aparece um determinado termo? Responder a esta pergunta funciona mais como um simples cheque de igualdade do que um loop sobre uma coleção. Na verdade, é por isso que para coleções `eq` de cordas, a Pesquisa `any`Cognitiva Azure só permite como um operador de comparação dentro de uma expressão lambda para .

A partir da igualdade, em seguida vamos ver como é possível combinar múltiplos controlos de igualdade na mesma variável gama com `or`. Funciona graças à álgebra e [à propriedade distributiva de quantificantes.](https://en.wikipedia.org/wiki/Existential_quantification#Negation) Esta expressão:

    seasons/any(s: s eq 'winter' or s eq 'fall')

é equivalente a:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

e cada uma `any` das duas subexpressões pode ser executada de forma eficiente usando o índice invertido. Além disso, graças à [lei de negação dos quantifiers,](https://en.wikipedia.org/wiki/Existential_quantification#Negation)esta expressão:

    seasons/all(s: s ne 'winter' and s ne 'fall')

é equivalente a:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

é por isso que é `all` `ne` possível usar com e `and`.

> [!NOTE]
> Embora os detalhes estejam fora do âmbito deste documento, estes mesmos princípios estendem-se também a testes de [distância e intersecção para recolhas de pontos geoespaciais.](search-query-odata-geo-spatial-functions.md) É por isso `any`que, em:
>
> - `geo.intersects`não pode ser negado
> - `geo.distance`deve ser comparado `lt` usando ou`le`
> - expressões devem ser `or`combinadas com, não`and`
>
> As regras de `all`invasões aplicam-se .

Uma maior variedade de expressões é permitida ao filtrar `lt`em `gt` `le`coleções `ge` de tipos `Collection(Edm.Int32)` de dados que suportam os, e operadores, como por exemplo. Especificamente, pode `and` utilizar `or` tão `any`bem como em , desde que as expressões de comparação `or`subjacentes sejam combinadas em **comparações** de alcance utilizando `and`, que são combinadas mais a utilizar. Esta estrutura de expressões booleanas chama-se [Disjunctive Normal Form (DNF),](https://en.wikipedia.org/wiki/Disjunctive_normal_form)também conhecida como "ORs of ANDs". Inversamente, as expressões de lambda para `all` estes tipos de dados devem estar na Forma Normal [Conjuntiva (CNF),](https://en.wikipedia.org/wiki/Conjunctive_normal_form)também conhecida como "ANDs of ORs". A Pesquisa Cognitiva Azure permite tais comparações de gama porque pode executá-las usando índices invertidos de forma eficiente, assim como pode fazer pesquisa rápida de cordas.

Em resumo, aqui estão as regras do polegar para o que é permitido numa expressão lambda:

- No `any`interior, são sempre *permitidos controlos positivos,* como igualdade, comparações `geo.intersects`de gama, ou `geo.distance` comparadas com `lt` ou `le` (pense em "proximidade" como sendo como a igualdade quando se trata de verificar a distância).
- `any`Dentro, `or` é sempre permitido. Só pode `and` utilizar para tipos de dados que possam expressar verificações de alcance, e apenas se utilizar ORs de ANDs (DNF).
- No `all`interior , as regras são invertidas -- `and` só são *permitidos controlos negativos,* pode utilizar-se sempre, e só pode utilizar `or` para verificações de alcance expressas como ANDs de ORs (CNF).

Na prática, estes são os tipos de filtros que é mais provável usar de qualquer maneira. Mas ainda é útil compreender os limites do que é possível.

Para exemplos específicos dos tipos de filtros permitidos e que não são, consulte [Como escrever filtros de recolha válidos](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Passos seguintes  

- [Filtros de recolha de OData de resolução de problemas em Pesquisa Cognitiva Azure](search-query-troubleshoot-collection-filters.md)
- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
