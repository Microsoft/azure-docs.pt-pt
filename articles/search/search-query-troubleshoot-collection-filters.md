---
title: Solução de problemas de filtros de coleção OData-Azure Search
description: Solucionando problemas de erros de filtro de coleção OData em consultas Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: fbd43cc13d3b7377668aad2fadc874ae47422ee1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647959"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Solução de problemas de filtros de coleção OData no Azure Search

Para [Filtrar](query-odata-filter-orderby-syntax.md) os campos de coleção no Azure Search, você pode usar [ `any` os `all` operadores e](search-query-odata-collection-operators.md) junto com **expressões lambda**. Uma expressão lambda é um subfiltro que é aplicado a cada elemento de uma coleção.

Nem todo recurso de expressões de filtro está disponível dentro de uma expressão lambda. Quais recursos estão disponíveis diferem dependendo do tipo de dados do campo de coleção que você deseja filtrar. Isso pode resultar em um erro se você tentar usar um recurso em uma expressão lambda que não tenha suporte nesse contexto. Se você estiver encontrando esses erros ao tentar escrever um filtro complexo nos campos de coleção, este artigo o ajudará a solucionar o problema.

## <a name="common-collection-filter-errors"></a>Erros de filtro de coleção comum

A tabela a seguir lista os erros que você pode encontrar ao tentar executar um filtro de coleção. Esses erros ocorrem quando você usa um recurso de expressões de filtro que não tem suporte dentro de uma expressão lambda. Cada erro fornece algumas diretrizes sobre como você pode reescrever seu filtro para evitar o erro. A tabela também inclui um link para a seção relevante deste artigo que fornece mais informações sobre como evitar esse erro.

| Mensagem de erro | Ocorrer | Para obter mais informações, veja |
| --- | --- | --- |
| A função ' IsMatch ' não tem parâmetros associados ao ' s da variável de intervalo '. Só há suporte para referências de campo associadas dentro de expressões lambda (' any ' ou ' all'). Altere seu filtro para que a função ' IsMatch ' esteja fora da expressão lambda e tente novamente. | Usando `search.ismatch` ou`search.ismatchscoring` dentro de uma expressão lambda | [Regras para filtrar coleções complexas](#bkmk_complex) |
| Expressão lambda inválida. Foi encontrado um teste para igualdade ou desigualdade em que o oposto era esperado em uma expressão lambda que itera em um campo da coleção de tipos (EDM. String). Para ' any ', use expressões do formato ' x EQ y ' ou ' search.in (...) '. Para ' all', use expressões do formato ' x ne y ', ' not (x EQ y) ' ou ' not search.in (...) '. | Filtragem em um campo do tipo`Collection(Edm.String)` | [Regras para filtrar coleções de cadeias de caracteres](#bkmk_strings) |
| Expressão lambda inválida. Encontrada uma forma sem suporte de expressão booliana complexa. Para ' any ', use expressões que sejam ' ORs de ANDs ', também conhecida como forma normal disjunctive. Por exemplo: ' (a e b) ou (c e d) ', em que a, b, c e d são subexpressãos de comparação ou de igualdade. Para ' all', use expressões que sejam ' ANDs of ORs ', também conhecidas como forma normal conjuntiva. Por exemplo: ' (a ou b) e (c ou d) ', em que a, b, c e d são subexpressãos de comparação ou desigualdade. Exemplos de expressões de comparação: ' x gt 5 ', ' x Le 2 '. Exemplo de uma expressão de igualdade: ' x EQ 5 '. Exemplo de uma expressão de desigualdade: ' x ne 5 '. | Filtrando em campos do `Collection(Edm.DateTimeOffset)`tipo `Collection(Edm.Double)` `Collection(Edm.Int32)`,, ou`Collection(Edm.Int64)` | [Regras para filtragem de coleções comparáveis](#bkmk_comparables) |
| Expressão lambda inválida. Foi encontrado um uso sem suporte de geograficamente. distance () ou Geo. interseccionations () em uma expressão lambda que itera sobre um campo da coleção de tipos (EDM. GeographyPoint). Para ' any ', certifique-se de comparar Geo. distance () usando os operadores ' lt ' ou ' Le ' e certifique-se de que qualquer uso de geográfico. Intersects () não seja negado. Para ' todos ', certifique-se de comparar a área geográfica. distance () usando os operadores ' gt ' ou ' ge ' e certifique-se de que qualquer uso de Geo. interseção () seja negado. | Filtragem em um campo do tipo`Collection(Edm.GeographyPoint)` | [Regras para filtragem de coleções GeographyPoint](#bkmk_geopoints) |
| Expressão lambda inválida. Não há suporte para expressões booleanas complexas em expressões lambda que iteram em campos da coleção de tipos (EDM. GeographyPoint). Para ' any ', ingresse em subexpressãos com ' or '; Não há suporte para ' and '. Para ' all', junte-se a subexpressãos com ' and '; Não há suporte para ' or '. | Filtrando em campos do `Collection(Edm.String)` tipo ou`Collection(Edm.GeographyPoint)` | [Regras para filtrar coleções de cadeias de caracteres](#bkmk_strings) <br/><br/> [Regras para filtragem de coleções GeographyPoint](#bkmk_geopoints) |
| Expressão lambda inválida. Encontrado um operador de comparação (um de ' lt ', ' Le ', ' gt ' ou ' ge '). Somente operadores de igualdade são permitidos em expressões lambda que iteram sobre campos da coleção de tipos (EDM. String). Para ' any ', use expressões do formato ' x EQ y '. Para ' all', use expressões do formato ' x ne y ' ou ' not (x EQ y) '. | Filtragem em um campo do tipo`Collection(Edm.String)` | [Regras para filtrar coleções de cadeias de caracteres](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Como escrever filtros de coleção válidos

As regras para gravar filtros de coleção válidos são diferentes para cada tipo de dados. As seções a seguir descrevem as regras mostrando exemplos de quais recursos de filtro têm suporte e quais não são:

- [Regras para filtrar coleções de cadeias de caracteres](#bkmk_strings)
- [Regras para filtragem de coleções booleanas](#bkmk_bools)
- [Regras para filtragem de coleções GeographyPoint](#bkmk_geopoints)
- [Regras para filtragem de coleções comparáveis](#bkmk_comparables)
- [Regras para filtrar coleções complexas](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regras para filtrar coleções de cadeias de caracteres

Dentro de expressões lambda para coleções de cadeias de caracteres, os únicos operadores de `eq` comparação `ne`que podem ser usados são e.

> [!NOTE]
> `lt` Azure Search não oferece suporte / `le` aosoperadores`ge` para cadeias de caracteres, dentro ou fora de uma expressão lambda. / `gt` /

O corpo de um `any` só pode testar a igualdade enquanto o corpo de um `all` só pode testar desigualdade.

Também é possível combinar várias expressões por `or` meio do corpo de um `any`, e por meio `and` do corpo de um `all`. Como a `search.in` função é equivalente à combinação de verificações de `or`igualdade com, ela também é permitida no corpo de `any`um. Por outro lado, `not search.in` é permitido no corpo de um. `all`

Por exemplo, essas expressões são permitidas:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

Embora essas expressões não sejam permitidas:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regras para filtragem de coleções booleanas

O tipo `Edm.Boolean` oferece suporte apenas `eq` aos `ne` operadores e. Assim, não faz muito sentido permitir a combinação dessas cláusulas que verificam a mesma variável `and` / `or` de intervalo, pois isso sempre levaria a tautologies ou contraditórias.

Aqui estão alguns exemplos de filtros em coleções booleanas que são permitidos:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Ao contrário das coleções de cadeias de caracteres, as coleções booleanas não têm limites em qual operador pode ser usado em qual tipo de expressão lambda. Ambos `eq` `any` `all`e `ne` podem ser usados no corpo de ou.

Expressões como as seguintes não são permitidas para coleções boolianas:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regras para filtragem de coleções GeographyPoint

Os valores do `Edm.GeographyPoint` tipo em uma coleção não podem ser comparados diretamente entre si. Em vez disso, eles devem ser usados como parâmetros `geo.distance` para `geo.intersects` as funções e. A `geo.distance` função, por sua vez, deve ser comparada a um valor de distância `lt`usando um `gt`dos operadores `ge`de comparação, `le`, ou. Essas regras também se aplicam aos campos de não coleção EDM. GeographyPoint.

Como as coleções de `Edm.GeographyPoint` cadeias de caracteres, as coleções têm algumas regras para como as funções geoespaciais podem ser usadas e combinadas nos diferentes tipos de expressões lambda:

- Quais operadores de comparação que você pode usar `geo.distance` com a função dependem do tipo de expressão lambda. Para `any`o, você pode usar `lt` apenas `le`o ou o. Para `all`o, você pode usar `gt` apenas `ge`o ou o. Você pode negar as expressões que `geo.distance`envolvem, mas precisará alterar o operador de comparação`geo.distance(...) lt x` ( `not (geo.distance(...) ge x)` torna `geo.distance(...) le x` - `not (geo.distance(...) gt x)`se e se torna).
- No corpo de um `all`, a `geo.intersects` função deve ser negada. Por outro lado, no corpo de um `any`, a `geo.intersects` função não deve ser negada.
- No corpo de uma `any`, as expressões geoespaciais geográficas podem ser combinadas usando. `or` No corpo de um `all`, essas expressões podem ser combinadas usando. `and`

As limitações acima existem por motivos semelhantes como a limitação de igualdade/desigualdade em coleções de cadeias de caracteres. Consulte [noções básicas sobre filtros de coleção OData no Azure Search](search-query-understand-collection-filters.md) para uma análise mais profunda desses motivos.

Aqui estão alguns exemplos de filtros em `Edm.GeographyPoint` coleções que são permitidas:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Expressões como as seguintes não são permitidas para `Edm.GeographyPoint` coleções:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regras para filtragem de coleções comparáveis

Esta seção se aplica a todos os seguintes tipos de dados:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

`Edm.Int32` Tipos como e `eq` `ne` `ge` `lt` `le` `gt`dão suporte a todos os seis operadores de comparação:,,,, e. `Edm.DateTimeOffset` Expressões lambda sobre coleções desses tipos podem conter expressões simples usando qualquer um desses operadores. Isso se aplica ao `any` e `all`ao. Por exemplo, esses filtros são permitidos:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

No entanto, há limitações sobre como essas expressões de comparação podem ser combinadas em expressões mais complexas dentro de uma expressão lambda:

- Regras para `any`:
  - Expressões de desigualdade simples não podem ser combinadas de forma útil com nenhuma outra expressão. Por exemplo, essa expressão é permitida:
    - `ratings/any(r: r ne 5)`

    Mas essa expressão não é:
    - `ratings/any(r: r ne 5 and r gt 2)`

    e embora essa expressão seja permitida, ela não é útil porque as condições se sobrepõem:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Expressões de comparação simples `eq`envolvendo `lt`, `le`, `gt`, ou `ge` podem ser combinadas `and`com / `or`. Por exemplo:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Expressões de comparação combinadas com (conconjuntos) podem `and` ser `or`combinadas com o. Esse formulário é conhecido em lógica booleana como "[disjunctive normal](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Por exemplo:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regras para `all`:
  - Expressões de igualdade simples não podem ser combinadas de forma útil com outras expressões. Por exemplo, essa expressão é permitida:
    - `ratings/all(r: r eq 5)`

    Mas essa expressão não é:
    - `ratings/all(r: r eq 5 or r le 2)`

    e embora essa expressão seja permitida, ela não é útil porque as condições se sobrepõem:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Expressões de comparação simples `ne`envolvendo `lt`, `le`, `gt`, ou `ge` podem ser combinadas `and`com / `or`. Por exemplo:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Expressões de comparação combinadas com `or` (disjunções) podem ser mais combinadas usando. `and` Esse formulário é conhecido em lógica booleana como "[conjuntiva normal](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Por exemplo:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regras para filtrar coleções complexas

Expressões lambda sobre coleções complexas dão suporte a uma sintaxe muito mais flexível do que expressões lambda em coleções de tipos primitivos. Você pode usar qualquer constructo de filtro dentro de uma expressão lambda que você pode usar fora de uma, com apenas duas exceções.

Primeiro, as funções `search.ismatch` e `search.ismatchscoring` não têm suporte dentro de expressões lambda. Para obter mais informações, consulte Understanding [OData Collection Filters in Azure Search](search-query-understand-collection-filters.md).

Segundo, a referência de campos que não estão *associados* à variável de intervalo (portanto, chamadas de *variáveis livres*) não é permitida. Por exemplo, considere as duas expressões de filtro OData equivalentes a seguir:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

A primeira expressão será permitida, enquanto o segundo formulário será rejeitado porque `details/margin` não está associado à variável `s`de intervalo.

Essa regra também se estende a expressões que têm variáveis associadas em um escopo externo. Essas variáveis são gratuitas em relação ao escopo no qual aparecem. Por exemplo, a primeira expressão é permitida, enquanto a segunda expressão equivalente não é permitida `s/name` porque é gratuita em relação ao escopo da variável `a`de intervalo:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Essa limitação não deve ser um problema na prática, pois sempre é possível construir filtros, de modo que as expressões lambda contenham apenas variáveis associadas.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Roteiro para regras de filtro de coleção

A tabela a seguir resume as regras para construir filtros válidos para cada tipo de dados de coleção.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter exemplos de como construir filtros válidos para cada caso, consulte [como escrever filtros de coleção válidos](#bkmk_examples).

Se você escrever filtros com frequência e entender as regras dos primeiros princípios ajudaria mais do que apenas memorizar, consulte [noções básicas sobre filtros de coleção OData no Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos seguintes  

- [Noções básicas sobre filtros de coleção OData no Azure Search](search-query-understand-collection-filters.md)
- [Filtros no Azure Search](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
