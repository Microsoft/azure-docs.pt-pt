---
title: Resolução de problemas filtros de recolha oData
titleSuffix: Azure Cognitive Search
description: Aprenda abordagens para resolver erros de filtro de recolha oData em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113091"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Filtros de recolha de OData de resolução de problemas em Pesquisa Cognitiva Azure

Para [filtrar](query-odata-filter-orderby-syntax.md) em campos de recolha em Azure Cognitive Search, pode utilizar os [ `any` e `all` operadores](search-query-odata-collection-operators.md) juntamente com **expressões de lambda.** Uma expressão lambda é um sub-filtro que é aplicado a cada elemento de uma coleção.

Nem todas as características das expressões de filtro estão disponíveis dentro de uma expressão lambda. Quais as funcionalidades disponíveis diferem consoante o tipo de dados do campo de recolha que pretende filtrar. Isto pode resultar num erro se tentar usar uma característica numa expressão lambda que não é suportada nesse contexto. Se você está encontrando tais erros enquanto tenta escrever um filtro complexo sobre campos de coleção, este artigo vai ajudá-lo a resolver o problema.

## <a name="common-collection-filter-errors"></a>Erros comuns do filtro de recolha

A tabela seguinte enumera erros que poderá encontrar ao tentar executar um filtro de recolha. Estes erros acontecem quando se usa uma característica de expressões de filtro que não é suportada dentro de uma expressão de lambda. Cada erro dá alguma orientação sobre como pode reescrever o filtro para evitar o erro. O quadro inclui também um link para a secção relevante deste artigo que fornece mais informações sobre como evitar esse erro.

| Mensagem de erro | Situação | Para obter mais informações, veja |
| --- | --- | --- |
| A função 'ismatch' não tem parâmetros ligados à variável de gama 's'. Apenas as referências de campo encadernadas são suportadas dentro das expressões lambda ('qualquer' ou 'all'). Por favor, mude o filtro para que a função 'ismatch' fique fora da expressão lambda e tente novamente. | Usando `search.ismatch` `search.ismatchscoring` ou dentro de uma expressão lambda | [Regras para filtrar coleções complexas](#bkmk_complex) |
| Expressão de lambda inválida. Encontrou um teste para a igualdade ou desigualdade onde se esperava o contrário numa expressão de lambda que iterates sobre um campo de tipo Collection (Edm.String). Para "qualquer", utilize expressões do formulário 'x eq y' ou 'search.in(...)'. Para 'todos', utilize expressões da forma 'x ne y', 'not (x eq y)', ou 'não search.in(...)'. | Filtragem em um campo de tipo`Collection(Edm.String)` | [Regras para filtrar coleções de cordas](#bkmk_strings) |
| Expressão de lambda inválida. Encontrou uma forma não apoiada de expressão booleancomplexa complexa. Para "qualquer", utilize expressões que sejam "ORs of ANDs", também conhecidacomo Forma Normal Disjuntiva. Por exemplo: «a e b) ou (c e d)» quando as subexpressões de comparação ou de igualdade. Para "todos", utilize expressões que são "ANDs of ORs", também conhecidacomo Forma Normal Conjuntiva. Por exemplo: «a ou b) e c ou d, quando as subexpressões de comparação ou de desigualdade são subexpressões de comparação ou desigualdade. Exemplos de expressões de comparação: 'x gt 5', 'x le 2'. Exemplo de uma expressão de igualdade: 'x eq 5'. Exemplo de uma expressão de desigualdade: 'x ne 5'. | Filtragem em campos `Collection(Edm.DateTimeOffset)` `Collection(Edm.Double)`de `Collection(Edm.Int32)`tipo, , ou`Collection(Edm.Int64)` | [Regras para a filtragem de coleções comparáveis](#bkmk_comparables) |
| Expressão de lambda inválida. Encontrou um uso não suportado de geo.distance() ou geo.intersects() numa expressão lambda que iterates sobre um campo de coleção tipo (Edm.GeographyPoint). Para "qualquer", certifique-se de comparar geo.distance() utilizando os operadores 'lt' ou 'le' e certifique-se de que qualquer utilização de geo.intersects() não é negada. Para 'todos', certifique-se de comparar geo.distance() utilizando os operadores 'gt' ou 'ge' e certifique-se de que qualquer utilização de geo.intersects() é negada. | Filtragem em um campo de tipo`Collection(Edm.GeographyPoint)` | [Regras para filtrar coleções de GeografiaPoint](#bkmk_geopoints) |
| Expressão de lambda inválida. Expressões booleanas complexas não são suportadas em expressões de lambda que iteram sobre campos de tipo Coleção (Edm.GeographyPoint). Para "qualquer", por favor, junte-se às subexpressões com 'ou'; "e" não é apoiado. Para 'todos', por favor junte-se a subexpressões com 'e'; "ou" não é apoiado. | Filtragem em campos `Collection(Edm.String)` de tipo ou`Collection(Edm.GeographyPoint)` | [Regras para filtrar coleções de cordas](#bkmk_strings) <br/><br/> [Regras para filtrar coleções de GeografiaPoint](#bkmk_geopoints) |
| Expressão de lambda inválida. Encontrou um operador de comparação (um de 'lt', 'le', 'gt' ou 'ge'). Apenas os operadores de igualdade são permitidos em expressões de lambda que iteram sobre campos de tipo Coleção (Edm.String). Para "qualquer", utilize expressões do formulário 'x eq y'. Para 'todos', utilize expressões do formulário 'x ne y' ou 'not (x eq y)'. | Filtragem em um campo de tipo`Collection(Edm.String)` | [Regras para filtrar coleções de cordas](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Como escrever filtros de recolha válidos

As regras para a escrita de filtros de recolha válidos são diferentes para cada tipo de dados. As seguintes secções descrevem as regras mostrando exemplos de que as funcionalidades do filtro são suportadas e que não são:

- [Regras para filtrar coleções de cordas](#bkmk_strings)
- [Regras para filtrar coleções Boolean](#bkmk_bools)
- [Regras para filtrar coleções de GeografiaPoint](#bkmk_geopoints)
- [Regras para a filtragem de coleções comparáveis](#bkmk_comparables)
- [Regras para filtrar coleções complexas](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regras para filtrar coleções de cordas

Dentro de expressões de lambda para coleções de `eq` cordas, os únicos operadores de comparação que podem ser usados são e `ne`.

> [!NOTE]
> A Azure Cognitive Search `lt` / `le` / `gt` / `ge` não suporta os operadores para cordas, seja dentro ou fora de uma expressão de lambda.

O corpo `any` de um só pode testar a `all` igualdade enquanto o corpo de um só pode testar a desigualdade.

Também é possível combinar múltiplas `or` expressões através `any`do `and` corpo de um `all`, e via no corpo de um . Uma `search.in` vez que a função é `or`equivalente a combinar verificações de `any`igualdade com, também é permitida no corpo de um . Inversamente, `not search.in` é permitido no `all`corpo de um .

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

O `Edm.Boolean` tipo suporta `eq` apenas os operadores e operadores. `ne` Como tal, não faz muito sentido permitir combinar tais cláusulas que `and` / `or` verificam a mesma variável de gama, uma vez que isso levaria sempre a tautologias ou contradições.

Aqui estão alguns exemplos de filtros em coleções Boolean as que são permitidas:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Ao contrário das coleções de cordas, as coleções Boolean não têm limites para que o operador possa ser utilizado em que tipo de expressão lambda. Ambos `eq` `ne` e podem ser usados no corpo de `any` ou `all`.

Expressões como as seguintes não são permitidas para coleções booleanas:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regras para filtrar coleções de GeografiaPoint

Valores `Edm.GeographyPoint` de tipo numa coleção não podem ser comparados diretamente uns com os outros. Em vez disso, devem ser `geo.distance` utilizados como parâmetros para as funções e funções. `geo.intersects` A `geo.distance` função, por sua vez, deve ser comparada `lt` `le`a `gt`um `ge`valor de distância utilizando um dos operadores de comparação, ou . Estas regras aplicam-se também aos campos Edm.GeographyPoint não-colectores.

Tal como as `Edm.GeographyPoint` coleções de cordas, as coleções têm algumas regras para a forma como as funções geo-espaciais podem ser usadas e combinadas nos diferentes tipos de expressões de lambda:

- Que operadores de comparação pode usar com a `geo.distance` função depende do tipo de expressão lambda. Para, `any`só pode `lt` `le`utilizar ou . Para, `all`só pode `gt` `ge`utilizar ou . Pode anular expressões `geo.distance`envolvendo, mas terá de mudar`geo.distance(...) lt x` o `not (geo.distance(...) ge x)` `geo.distance(...) le x` operador `not (geo.distance(...) gt x)`de comparação ( torna-se e torna-se).
- No corpo de `all`um, a `geo.intersects` função deve ser negada. Inversamente, no corpo `any`de `geo.intersects` um, a função não deve ser negada.
- No corpo de `any`uma , expressões geo-espaciais podem ser combinadas usando `or`. No corpo de `all`um, tais expressões `and`podem ser combinadas usando .

As limitações acima existem por razões semelhantes à limitação da igualdade/desigualdade nas coleções de cordas. Consulte [a Compreensão dos filtros de recolha oData em Azure Cognitive Search](search-query-understand-collection-filters.md) para uma análise mais profunda destas razões.

Aqui estão alguns exemplos de filtros em `Edm.GeographyPoint` coleções que são permitidas:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Não são permitidas expressões `Edm.GeographyPoint` como as seguintes coleções:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regras para a filtragem de coleções comparáveis

Esta secção aplica-se a todos os seguintes tipos de dados:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Tipos como `Edm.Int32` `Edm.DateTimeOffset` e suporte aos seis `eq`operadores `lt` `le`de `gt`comparação: , `ge` `ne`, , e . As expressões da Lambda sobre coleções deste tipo podem conter expressões simples usando qualquer um destes operadores. Isto aplica-se `any` `all`tanto a ambos como . Por exemplo, estes filtros são permitidos:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

No entanto, existem limitações na forma como tais expressões de comparação podem ser combinadas em expressões mais complexas dentro de uma expressão lambda:

- Regras `any`para:
  - Expressões simples de desigualdade não podem ser úteis combinadas com quaisquer outras expressões. Por exemplo, esta expressão é permitida:
    - `ratings/any(r: r ne 5)`

    mas esta expressão não é:
    - `ratings/any(r: r ne 5 and r gt 2)`

    e enquanto esta expressão é permitida, não é útil porque as condições se sobrepõem:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Expressões de `eq`comparação `gt`simples `ge` que envolvam, `and` / `or` `lt` `le`, ou podem ser combinadas com . Por exemplo:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - As expressões `and` de comparação combinadas `or`com (conjunções) podem ser combinadas utilizando . Esta forma é conhecida na lógica booleana como "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Por exemplo:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regras `all`para:
  - Expressões simples de igualdade não podem ser úteis combinadas com quaisquer outras expressões. Por exemplo, esta expressão é permitida:
    - `ratings/all(r: r eq 5)`

    mas esta expressão não é:
    - `ratings/all(r: r eq 5 or r le 2)`

    e enquanto esta expressão é permitida, não é útil porque as condições se sobrepõem:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Expressões de `ne`comparação `gt`simples `ge` que envolvam, `and` / `or` `lt` `le`, ou podem ser combinadas com . Por exemplo:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - As expressões `or` de comparação combinadas com `and`(disjunções) podem ser combinadas utilizando . Esta forma é conhecida na lógica booleana como "[Forma Normal Conjuntiva](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Por exemplo:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regras para filtrar coleções complexas

As expressões lambda sobre coleções complexas suportam uma sintaxe muito mais flexível do que expressões de lambda sobre coleções de tipos primitivos. Você pode usar qualquer construção de filtro dentro de uma expressão de lambda que você pode usar fora de uma, com apenas duas exceções.

Primeiro, as `search.ismatch` funções e `search.ismatchscoring` não são suportadas dentro de expressões de lambda. Para mais informações, consulte [A Compreensão dos filtros de recolha oData em Pesquisa Cognitiva Azure](search-query-understand-collection-filters.md).

Em segundo lugar, não é permitido fazer referência a campos que não estejam *ligados* à variável de gama (as chamadas *variáveis livres).* Por exemplo, considere as seguintes duas expressões equivalentes de filtro OData:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

A primeira expressão será permitida, enquanto a `details/margin` segunda forma será rejeitada `s`porque não está ligada à variável de gama.

Esta regra estende-se também a expressões que têm variáveis ligadas num âmbito exterior. Tais variáveis são livres no que diz respeito ao âmbito em que aparecem. Por exemplo, a primeira expressão é permitida, enquanto `s/name` a segunda expressão equivalente não é `a`permitida porque é livre no que diz respeito ao âmbito da variável gama:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Esta limitação não deve ser um problema na prática, uma vez que é sempre possível construir filtros de tal forma que as expressões de lambda contêm apenas variáveis ligadas.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Folha de batota para regras de filtro de coleção

O quadro seguinte resume as regras para a construção de filtros válidos para cada tipo de dados de recolha.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Para exemplos de como construir filtros válidos para cada caso, consulte [Como escrever filtros de recolha válidos](#bkmk_examples).

Se escrever filtros com frequência, e compreender as regras dos primeiros princípios, ajudá-lo-ia mais do que apenas memorizá-los, consulte a compreensão dos filtros de [recolha OData em Pesquisa Cognitiva Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos seguintes  

- [Compreender filtros de recolha oData em Pesquisa Cognitiva Azure](search-query-understand-collection-filters.md)
- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
