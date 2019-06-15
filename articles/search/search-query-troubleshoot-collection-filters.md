---
title: Resolução de problemas de filtros de recolha de OData - Azure Search
description: Resolução de problemas de erros de filtro de coleção OData em consultas de pesquisa do Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079629"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Resolução de problemas de filtros de recolha de OData no Azure Search

Para [filtro](query-odata-filter-orderby-syntax.md) em campos de coleção do Azure Search, pode utilizar os [ `any` e `all` operadores](search-query-odata-collection-operators.md) em conjunto com **expressões lambda**. Uma expressão lambda é um filtro secundária que é aplicado a cada elemento de uma coleção.

Não todas as funcionalidades de expressões de filtro estão disponível dentro de uma expressão lambda. Que funcionalidades estão disponíveis é diferente consoante o tipo de dados do campo coleção que pretende filtrar. Isso pode resultar num erro se tentar usar um recurso numa expressão lambda que não é suportada nesse contexto. Se estiver com esses erros ao tentar escrever um filtro complexo em campos de coleção, este artigo irá ajudá-lo a resolver o problema.

## <a name="common-collection-filter-errors"></a>Erros comuns de filtro de coleção

A tabela seguinte indica os erros que poderá encontrar ao tentar executar um filtro de coleção. Estes erros ocorrem quando usar um recurso de expressões de filtro que não é suportado dentro de uma expressão lambda. Cada erro fornece algumas orientações sobre como pode reescrever o filtro para evitar o erro. A tabela também inclui uma ligação para a secção relevante deste artigo, que fornece mais informações sobre como evitar esse erro.

| Mensagem de erro | Situação | Para obter mais informações, veja |
| --- | --- | --- |
| A função 'ismatch' não tem parâmetros vinculados à variável de alcance ". Vinculado apenas o campo referências são suportadas no interior de expressões lambda ("qualquer" ou "todos"). . Altere o filtro, para que a função 'ismatch' está fora da expressão lambda e tente novamente. | Usando `search.ismatch` ou `search.ismatchscoring` dentro de uma expressão lambda | [Regras para filtrar coleções complexas](#bkmk_complex) |
| Expressão lambda inválido. Encontrar um teste de igualdade ou desigualdade onde o oposto era esperado numa expressão lambda que itera através de um campo do tipo Collection(Edm.String). Para "qualquer", utilize expressões do formulário 'x eq y' ou 'search.in(...)'. Para "all", utilize expressões do formulário 'x ne y', 'não (x eq y)' ou 'não search.in(...)'. | Filtrar num campo do tipo `Collection(Edm.String)` | [Regras de filtragem de coleções de cadeia de caracteres](#bkmk_strings) |
| Expressão lambda inválido. Encontrar uma forma não suportada de expressão booleana complexo. Para "qualquer", utilize expressões que são "ORs de ANDs", também conhecido como Disjunctive Normal formulário. Por exemplo: '(a and b) ou (c e d) "em que a, b, c e d são as subexpressões comparação ou de igualdade. Para "all", utilize expressões que são "ANDs de ORs", também conhecido como Conjunctive Normal formulário. Por exemplo: '(a or b) e (c ou d) "em que a, b, c e d são as subexpressões comparação ou desigualdade. Exemplos de expressões de comparação: ' x gt 5', ' x le 2. Exemplo de uma expressão de igualdade: ' x eq 5'. Exemplo de uma expressão de desigualdade: ' x ne 5'. | Filtrar em campos do tipo `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, ou `Collection(Edm.Int64)` | [Regras para filtrar coleções comparáveis](#bkmk_comparables) |
| Expressão lambda inválido. Foi encontrada uma utilização não suportada de geo.distance() ou geo.intersects() numa expressão lambda que itera através de um campo do tipo Collection(Edm.GeographyPoint). Para "qualquer", certifique-se de comparar geo.distance() utilizando os operadores "lt" ou "le" e certifique-se de que qualquer utilização do geo.intersects() não será negada. Para "all", certifique-se de comparar geo.distance() utilizando os operadores "gt" ou "ge" e certifique-se de que qualquer utilização do geo.intersects() será negada. | Filtrar num campo do tipo `Collection(Edm.GeographyPoint)` | [Regras de filtragem GeographyPoint coleções](#bkmk_geopoints) |
| Expressão lambda inválido. As expressões booleanas complexas não são suportadas em expressões lambda que iterar sobre os campos do tipo Collection(Edm.GeographyPoint). Para "qualquer", Junte-se as subexpressões com 'ou'; "e" não é suportada. Para "all", Junte-se as subexpressões com 'e'; 'ou' não é suportada. | Filtrar em campos do tipo `Collection(Edm.String)` ou `Collection(Edm.GeographyPoint)` | [Regras de filtragem de coleções de cadeia de caracteres](#bkmk_strings) <br/><br/> [Regras de filtragem GeographyPoint coleções](#bkmk_geopoints) |
| Expressão lambda inválido. Foi encontrado um operador de comparação (um de "lt", "le", "gt" ou "ge"). Operadores de igualdade apenas são permitidos em expressões lambda que iterar sobre os campos do tipo Collection(Edm.String). Para "qualquer", utilize expressões do formulário 'x eq y'. Para "all", utilize expressões do formulário 'x ne y' ou 'não (x eq y)". | Filtrar num campo do tipo `Collection(Edm.String)` | [Regras de filtragem de coleções de cadeia de caracteres](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Como escrever filtros de recolha válido

As regras para criar filtros de colecção são diferentes para cada tipo de dados. As secções seguintes descrevem as regras ao mostrar exemplos de que filtros funcionalidades são suportadas e que não são:

- [Regras de filtragem de coleções de cadeia de caracteres](#bkmk_strings)
- [Regras para filtrar coleções booleanas](#bkmk_bools)
- [Regras de filtragem GeographyPoint coleções](#bkmk_geopoints)
- [Regras para filtrar coleções comparáveis](#bkmk_comparables)
- [Regras para filtrar coleções complexas](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regras de filtragem de coleções de cadeia de caracteres

No interior de expressões lambda para coleções de cadeia de caracteres, os operadores de comparação única que podem ser utilizados são `eq` e `ne`.

> [!NOTE]
> O Azure Search não suporta o `lt` / `le` / `gt` / `ge` operadores para cadeias de caracteres, se dentro ou fora de uma expressão lambda.

O corpo de uma `any` só pode testar a igualdade enquanto o corpo de um `all` pode apenas testar desigualdade.

Também é possível combinar várias expressões através de `or` no corpo de um `any`e por meio `and` no corpo de um `all`. Uma vez que o `search.in` função é equivalente a combinação de verificações de igualdade com `or`, também é permitida no corpo de um `any`. Por outro lado, `not search.in` é permitida no corpo de um `all`.

Por exemplo, estas expressões são permitidos:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

Embora essas expressões não são permitidos:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regras para filtrar coleções booleanas

O tipo `Edm.Boolean` só suporta o `eq` e `ne` operadores. Como tal, não faz muito sentido para permitir a combinação dessas cláusulas que verificam a mesma variável de alcance com `and` / `or` , uma vez que sempre que levaria a tautologies ou contradições.

Aqui estão alguns exemplos de filtros em booleanos coleções que são permitidos:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Ao contrário das coleções de cadeia, booleanas coleções têm sem limites em que o operador pode ser usado em que tipo de expressão lambda. Ambos `eq` e `ne` pode ser utilizado no corpo do `any` ou `all`.

Não são permitidas expressões, como o seguinte para coleções booleanas:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regras de filtragem GeographyPoint coleções

Valores do tipo `Edm.GeographyPoint` numa coleção não pode ser comparado com diretamente entre si. Em vez disso, eles devem ser usados como parâmetros para o `geo.distance` e `geo.intersects` funções. O `geo.distance` função por sua vez deve ser comparada para um valor de distância através de um dos operadores de comparação `lt`, `le`, `gt`, ou `ge`. Estas regras aplicam-se também aos campos de geographypoint não coleção.

Como coleções de cadeia de caracteres, `Edm.GeographyPoint` coleções têm algumas regras para a forma como as funções geoespaciais podem ser usadas e combinadas dos diferentes tipos de expressões lambda:

- Os operadores de comparação, pode utilizar com o `geo.distance` função depende do tipo da expressão lambda. Para `any`, pode utilizar apenas `lt` ou `le`. Para `all`, pode utilizar apenas `gt` ou `ge`. Pode negar expressões que envolvem `geo.distance`, mas terá que alterar o operador de comparação (`geo.distance(...) lt x` torna-se `not (geo.distance(...) ge x)` e `geo.distance(...) le x` torna-se `not (geo.distance(...) gt x)`).
- No corpo de uma `all`, o `geo.intersects` função deve ser negada. Por outro lado, no corpo de uma `any`, o `geo.intersects` não deve ser negada a função.
- No corpo de uma `any`, geoespaciais expressões podem ser combinadas com `or`. No corpo de uma `all`, tais expressões podem ser combinadas com `and`.

As limitações acima, existem por motivos semelhantes, como a limitação de igualdade/desigualdade em coleções de cadeia de caracteres. Ver [filtros de recolha de noções básicas sobre OData na Azure Search](search-query-understand-collection-filters.md) para uma análise mais profunda essas razões.

Aqui estão alguns exemplos de filtros no `Edm.GeographyPoint` coleções que são permitidas:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

As expressões como os seguintes não são permitidas para `Edm.GeographyPoint` coleções:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regras para filtrar coleções comparáveis

Esta secção aplica-se a todos os tipos de dados seguintes:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Como tipos `Edm.Int32` e `Edm.DateTimeOffset` suportar todos os seis dos operadores de comparação: `eq`, `ne`, `lt`, `le`, `gt`, e `ge`. As expressões lambda em coleções desses tipos podem conter expressões simples, utilizando qualquer um destes operadores. Isto aplica-se a ambos `any` e `all`. Por exemplo, estes filtros são permitidos:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

No entanto, há limitações quanto à forma como as expressões de comparação podem ser combinadas num expressões mais complexas dentro de uma expressão lambda:

- Regras para `any`:
  - Expressões de desigualdade simples não podem ser útil combinadas com qualquer outras expressões. Por exemplo, é permitida esta expressão:
    - `ratings/any(r: r ne 5)`

    mas não está esta expressão:
    - `ratings/any(r: r ne 5 and r gt 2)`

    e embora esta expressão é permitida, não é útil porque as condições sobrepor-se:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Expressões de comparação simples envolvendo `eq`, `lt`, `le`, `gt`, ou `ge` pode ser combinado com `and` / `or`. Por exemplo:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Expressões de comparação juntamente com `and` (conjunções) podem ser ainda mais combinadas com `or`. Esse formulário é conhecido em lógica booleana, como "[Disjunctive Normal formulário](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Por exemplo:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regras para `all`:
  - Expressões de igualdade simples não podem ser útil combinadas com qualquer outras expressões. Por exemplo, é permitida esta expressão:
    - `ratings/all(r: r eq 5)`

    mas não está esta expressão:
    - `ratings/all(r: r eq 5 or r le 2)`

    e embora esta expressão é permitida, não é útil porque as condições sobrepor-se:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Expressões de comparação simples envolvendo `ne`, `lt`, `le`, `gt`, ou `ge` pode ser combinado com `and` / `or`. Por exemplo:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Expressões de comparação juntamente com `or` (disjunctions) podem ser ainda mais combinados com `and`. Esse formulário é conhecido em lógica booleana, como "[Conjunctive Normal formulário](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Por exemplo:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regras para filtrar coleções complexas

As expressões lambda sobre coleções complexas suportam uma sintaxe muito mais flexível do que as expressões lambda em coleções de tipos primitivos. Pode usar qualquer construção de filtro dentro desse tipo uma expressão lambda que pode usar um exterior, com apenas duas exceções.

Primeiro, as funções `search.ismatch` e `search.ismatchscoring` não são suportados no interior de expressões lambda. Para obter mais informações, consulte [filtros de recolha de noções básicas sobre OData na Azure Search](search-query-understand-collection-filters.md).

Em segundo lugar, os campos que não estão a referenciar *vinculado* à variável de alcance (chamados *gratuita variáveis*) não é permitida. Por exemplo, considere as seguintes dois equivalentes OData expressões de filtro:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Será permitida a primeira expressão, enquanto o segundo formulário será rejeitado porque `details/margin` não está vinculado à variável de alcance `s`.

Esta regra também se estende a expressões com variáveis vinculadas no âmbito externo. Essas variáveis são gratuitos em relação ao âmbito em que aparecem. Por exemplo, a primeira expressão é permitida, enquanto a expressão equivalente em segundo lugar não é permitida porque `s/name` é gratuito em relação ao âmbito da variável de alcance `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Esta limitação não deve ser um problema na prática, uma vez que sempre é possível construir filtros de forma a que as expressões lambda contêm apenas as variáveis ligadas.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Referência rápida para as regras do filtro de coleção

A tabela seguinte resume as regras para a criação de filtros válidos para cada tipo de dados de coleção.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter exemplos de como construir filtros válidos para cada caso, consulte [como escrever filtros de recolha válido](#bkmk_examples).

Se filtros de escrita, muitas vezes, e conhecer as regras de princípios ajuda a mais do que apenas a memorização, consulte [filtros de recolha de noções básicas sobre OData na Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos Seguintes  

- [Noções básicas sobre filtros de recolha de OData no Azure Search](search-query-understand-collection-filters.md)
- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
