---
title: Resolução de problemas filtros de recolha OData
titleSuffix: Azure Cognitive Search
description: Aprenda abordagens para resolver erros de filtro de recolha OData em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 3050f701c11773207aa6054d4d08d908d87b2ce7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932071"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Resolução de problemas filtros de recolha OData em Pesquisa Cognitiva Azure

Para [filtrar](query-odata-filter-orderby-syntax.md) em campos de recolha em Azure Cognitive Search, pode utilizar os [ `any` operadores e `all` operadores](search-query-odata-collection-operators.md) juntamente com **expressões lambda.** Uma expressão lambda é um sub-filtro que é aplicado a cada elemento de uma coleção.

Nem todas as características de expressões de filtro estão disponíveis dentro de uma expressão lambda. Quais as funcionalidades disponíveis diferem consoante o tipo de dados do campo de recolha que pretende filtrar. Isto pode resultar num erro se tentar usar uma funcionalidade numa expressão lambda que não seja suportada nesse contexto. Se estiver a encontrar tais erros enquanto tenta escrever um filtro complexo sobre os campos de recolha, este artigo irá ajudá-lo a resolver o problema.

## <a name="common-collection-filter-errors"></a>Erros comuns do filtro de recolha

A tabela que se segue enumera os erros que poderá encontrar ao tentar executar um filtro de recolha. Estes erros acontecem quando se utiliza uma característica de expressões de filtro que não é suportada dentro de uma expressão lambda. Cada erro dá algumas orientações sobre como pode reescrever o filtro para evitar o erro. A tabela também inclui um link para a secção relevante deste artigo que fornece mais informações sobre como evitar esse erro.

| Mensagem de erro | Situação | Para obter mais informações, veja |
| --- | --- | --- |
| A função 'ismatch' não tem parâmetros ligados à variável 's' variável. Apenas as referências de campo encadernadas são suportadas dentro de expressões lambda ('any' ou 'all'). Por favor, mude o filtro para que a função 'ismatch' esteja fora da expressão lambda e tente novamente. | Usando `search.ismatch` ou dentro de uma expressão `search.ismatchscoring` lambda | [Regras para filtrar coleções complexas](#bkmk_complex) |
| Expressão de lambda inválida. Encontrou um teste para a igualdade ou desigualdade onde se esperava o oposto numa expressão lambda que itera sobre um campo de tipo Coleção (Edm.String). Para 'qualquer', utilize expressões do formulário 'x eq y' ou 'search.in(...)'. Para 'todos', por favor use expressões do formulário 'x ne y', 'não (x eq y)', ou 'não search.in(...)'. | Filtragem em um campo de tipo `Collection(Edm.String)` | [Regras para filtrar coleções de cordas](#bkmk_strings) |
| Expressão de lambda inválida. Encontrei uma forma não apoiada de expressão complexa booleana. Para 'qualquer', utilize expressões que sejam 'ORs of ANDs', também conhecidas como Formulário Normal Disjuntivo. Por exemplo: «a a e b) ou c e d», quando a, b, c e d são subexpressões de comparação ou igualdade. Para 'todos', por favor utilize expressões que sejam 'ANDs de ORs', também conhecidas como Formulário Normal Conjuntivo. Por exemplo: «a a ou b) e (c ou d)» quando a, b, c e d forem subexpressões de comparação ou desigualdade. Exemplos de expressões de comparação: 'x gt 5', 'x le 2'. Exemplo de uma expressão de igualdade: 'x eq 5'. Exemplo de uma expressão de desigualdade: 'x ne 5'. | Filtragem em campos de tipo `Collection(Edm.DateTimeOffset)` , `Collection(Edm.Double)` , `Collection(Edm.Int32)` ou `Collection(Edm.Int64)` | [Regras para filtragem de coleções comparáveis](#bkmk_comparables) |
| Expressão de lambda inválida. Encontrou uma utilização não apoiada de geo.distance() ou geo.intersects() numa expressão lambda que itera sobre um campo de tipo Coleção (Edm.GeographyPoint). Para 'qualquer', certifique-se de que compara geo.distance() utilizando os operadores 'lt' ou 'le' e certifique-se de que qualquer utilização de geo.intersects() não é negada. Para 'todos', certifique-se de que compara geo.distance() utilizando os operadores 'gt' ou 'ge' e certifique-se de que qualquer utilização de geo.intersects() é negada. | Filtragem em um campo de tipo `Collection(Edm.GeographyPoint)` | [Regras para filtrar coleções GeographyPoint](#bkmk_geopoints) |
| Expressão de lambda inválida. Expressões complexas booleanas não são suportadas em expressões lambda que iteram sobre campos de tipo Coleção (Edm.GeographyPoint). Para 'qualquer', por favor, junte subexpressões com 'ou'; 'e' não é apoiado. Para 'todos', por favor junte sub-expressões com 'e'; 'ou' não é apoiado. | Filtragem em campos de tipo `Collection(Edm.String)` ou `Collection(Edm.GeographyPoint)` | [Regras para filtrar coleções de cordas](#bkmk_strings) <br/><br/> [Regras para filtrar coleções GeographyPoint](#bkmk_geopoints) |
| Expressão de lambda inválida. Encontrei um operador de comparação (um de 'lt', 'le', 'gt', ou 'ge'). Apenas os operadores de igualdade são permitidos em expressões lambda que iteram sobre campos de tipo Coleção (Edm.String). Para 'qualquer', utilize expressões do formulário 'x eq y'. Para 'todos', utilize expressões do formulário 'x ne y' ou 'não (x eq y)'. | Filtragem em um campo de tipo `Collection(Edm.String)` | [Regras para filtrar coleções de cordas](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Como escrever filtros de recolha válidos

As regras para a escrita de filtros de recolha válidos são diferentes para cada tipo de dados. As seguintes secções descrevem as regras mostrando exemplos de quais as características do filtro suportadas e que não são:

- [Regras para filtrar coleções de cordas](#bkmk_strings)
- [Regras para filtrar coleções Boolean](#bkmk_bools)
- [Regras para filtrar coleções GeographyPoint](#bkmk_geopoints)
- [Regras para filtragem de coleções comparáveis](#bkmk_comparables)
- [Regras para filtrar coleções complexas](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regras para filtrar coleções de cordas

No interior das expressões lambda para coleções de cordas, os únicos operadores de comparação que podem ser utilizados são `eq` e `ne` .

> [!NOTE]
> A Azure Cognitive Search não suporta os `lt` / `le` / `gt` / `ge` operadores para cordas, dentro ou fora de uma expressão lambda.

O corpo de um `any` só pode testar a igualdade enquanto o corpo de um só pode testar a `all` desigualdade.

Também é possível combinar múltiplas expressões através `or` do corpo de um , e através no corpo de um `any` `and` `all` . Uma vez que a `search.in` função é equivalente a combinar verificações de igualdade `or` com, também é permitido no corpo de um `any` . Inversamente, `not search.in` é permitido no corpo de um `all` .

Por exemplo, estas expressões são permitidas:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

enquanto estas expressões não são permitidas:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regras para filtrar coleções Boolean

O tipo `Edm.Boolean` suporta apenas os `eq` `ne` operadores e operadores. Como tal, não faz muito sentido permitir combinar tais cláusulas que verificam a mesma variável de gama com `and` / `or` isso, uma vez que isso levaria sempre a tautologias ou contradições.

Aqui estão alguns exemplos de filtros em coleções Boolean que são permitidos:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Ao contrário das coleções de cordas, as coleções Boolean não têm limites para o operador poder ser utilizado em que tipo de expressão lambda. Ambos `eq` podem `ne` ser usados no corpo de ou `any` `all` .

Expressões como as seguintes não são permitidas para coleções Boolean:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regras para filtrar coleções GeographyPoint

Valores de tipo `Edm.GeographyPoint` numa coleção não podem ser comparados diretamente uns com os outros. Em vez disso, devem ser utilizados como parâmetros para as `geo.distance` `geo.intersects` funções e funções. A `geo.distance` função, por sua vez, deve ser comparada a um valor de distância utilizando um dos operadores de `lt` comparação, ou `le` `gt` `ge` . Estas regras aplicam-se também aos campos Edm.GeographyPoint não-coleção.

Tal como as coleções de cordas, `Edm.GeographyPoint` as coleções têm algumas regras para como as funções geo-espaciais podem ser usadas e combinadas nos diferentes tipos de expressões lambda:

- Quais operadores de comparação que você pode usar com a `geo.distance` função depende do tipo de expressão lambda. Para `any` , pode utilizar apenas ou . `lt` `le` . Para `all` , pode utilizar apenas ou . `gt` `ge` . Pode anular expressões `geo.distance` envolvendo, mas terá de mudar o operador de comparação `geo.distance(...) lt x` (torna-se `not (geo.distance(...) ge x)` e `geo.distance(...) le x` torna-se). `not (geo.distance(...) gt x)`
- No corpo de `all` um, a `geo.intersects` função deve ser negada. Inversamente, no corpo de um `any` , a `geo.intersects` função não deve ser negada.
- No corpo de um `any` , expressões geo-espaciais podem ser combinadas usando `or` . No corpo de um `all` , tais expressões podem ser combinadas usando `and` .

As limitações acima referidas existem por razões semelhantes à limitação da igualdade/desigualdade nas coleções de cordas. Consulte [os filtros de recolha OData da Azure Cognitive Search](search-query-understand-collection-filters.md) para obter um olhar mais aprofundado sobre estas razões.

Aqui estão alguns exemplos de filtros em `Edm.GeographyPoint` coleções que são permitidos:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Não são permitidas expressões como as seguintes para `Edm.GeographyPoint` coleções:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regras para filtragem de coleções comparáveis

Esta secção aplica-se a todos os seguintes tipos de dados:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Tipos como `Edm.Int32` e apoiar os seis `Edm.DateTimeOffset` operadores de comparação: `eq` , , , , , e `ne` `lt` `le` `gt` `ge` . As expressões lambda sobre coleções destes tipos podem conter expressões simples usando qualquer um destes operadores. Isto aplica-se a ambos `any` e `all` . Por exemplo, estes filtros são permitidos:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

No entanto, existem limitações na forma como tais expressões de comparação podem ser combinadas em expressões mais complexas dentro de uma expressão lambda:

- `any`Regras:
  - Expressões simples de desigualdade não podem ser úteis combinadas com outras expressões. Por exemplo, esta expressão é permitida:
    - `ratings/any(r: r ne 5)`

    mas esta expressão não é:
    - `ratings/any(r: r ne 5 and r gt 2)`

    e enquanto esta expressão é permitida, não é útil porque as condições se sobrepõem:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Simples expressões de comparação `eq` envolvendo, , , , , ou pode ser combinado com `lt` `le` `gt` `ge` `and` / `or` . Por exemplo:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - As expressões de comparação combinadas com `and` (conjunções) podem ser combinadas através de `or` . Esta forma é conhecida na lógica booleana como "[Formulário Normal Disjuntivo](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Por exemplo:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- `all`Regras:
  - Expressões simples de igualdade não podem ser úteis combinadas com outras expressões. Por exemplo, esta expressão é permitida:
    - `ratings/all(r: r eq 5)`

    mas esta expressão não é:
    - `ratings/all(r: r eq 5 or r le 2)`

    e enquanto esta expressão é permitida, não é útil porque as condições se sobrepõem:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Simples expressões de comparação `ne` envolvendo, , , , , ou pode ser combinado com `lt` `le` `gt` `ge` `and` / `or` . Por exemplo:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - As expressões de comparação combinadas com `or` (disjunções) podem ser combinadas através de `and` . Esta forma é conhecida na lógica booleana como "[Formulário Normal Conjuntivo](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Por exemplo:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regras para filtrar coleções complexas

Expressões lambda sobre coleções complexas suportam uma sintaxe muito mais flexível do que expressões lambda sobre coleções de tipos primitivos. Pode utilizar qualquer construção de filtro dentro de uma expressão lambda que pode usar fora de uma, com apenas duas exceções.

Primeiro, as funções `search.ismatch` e `search.ismatchscoring` não são suportadas dentro de expressões lambda. Para obter mais informações, consulte [filtros de recolha Understanding OData na Pesquisa Cognitiva Azure](search-query-understand-collection-filters.md).

Em segundo lugar, não é permitido referir campos que não estão *ligados* à variável de alcance (as chamadas *variáveis livres).* Por exemplo, considere as duas expressões equivalentes de filtro OData:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

A primeira expressão será permitida, enquanto a segunda forma será rejeitada porque `details/margin` não está ligada à variável de `s` alcance.

Esta regra também se estende a expressões que têm variáveis ligadas num âmbito exterior. Estas variáveis são livres em relação ao âmbito em que aparecem. Por exemplo, a primeira expressão é permitida, enquanto a segunda expressão equivalente não é permitida porque `s/name` é livre em relação ao âmbito da variável de gama `a` :

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Esta limitação não deve ser um problema na prática, uma vez que é sempre possível construir filtros de tal forma que as expressões de lambda contêm apenas variáveis ligadas.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Folha de batota para regras de filtro de coleção

O quadro seguinte resume as regras para a construção de filtros válidos para cada tipo de dados de recolha.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Por exemplo, como construir filtros válidos para cada caso, consulte [Como escrever filtros de recolha válidos.](#bkmk_examples)

Se escrever filtros com frequência e compreender as regras dos primeiros princípios ajuda-o mais do que apenas memorizá-los, ver [filtros de recolha OData no Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos seguintes  

- [Compreender filtros de recolha OData em Pesquisa Cognitiva Azure](search-query-understand-collection-filters.md)
- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)