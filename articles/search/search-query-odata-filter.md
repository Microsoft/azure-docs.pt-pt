---
title: Referência do filtro OData
titleSuffix: Azure Cognitive Search
description: Referência linguística OData e sintaxe completa utilizada para criar expressões de filtro em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 0f33b5a28d7c83be7e546c3f61bc517047c51312
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934859"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter sintaxe na pesquisa cognitiva de Azure

A Azure Cognitive Search utiliza [expressões de filtro OData](query-odata-filter-orderby-syntax.md) para aplicar critérios adicionais a uma consulta de pesquisa além de termos de pesquisa de texto completo. Este artigo descreve em detalhe a sintaxe dos filtros. Para obter informações mais gerais sobre o que são os filtros e como usá-los para realizar cenários específicos de consulta, consulte [Filtros em Pesquisa Cognitiva Azure](search-filters.md).

## <a name="syntax"></a>Syntax

Um filtro na linguagem OData é uma expressão booleana, que por sua vez pode ser um dos vários tipos de expressão, como mostra o seguinte formulário EBNF[(Formulário Backus-Naur Alargado):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Os tipos de expressões booleanas incluem:

- Recolha de expressões de filtro utilizando `any` ou `all` . Estes aplicam critérios de filtro aos campos de recolha. Para obter mais informações, consulte [os operadores de recolha OData na Pesquisa Cognitiva Azure.](search-query-odata-collection-operators.md)
- Expressões lógicas que combinam outras expressões booleanas usando os `and` `or` operadores, e `not` . Para obter mais informações, consulte [os operadores lógicos OData na Pesquisa Cognitiva Azure.](search-query-odata-logical-operators.md)
- Expressões de comparação, que comparam campos ou variáveis de gama a valores constantes utilizando os `eq` operadores, , , , e `ne` `gt` `lt` `ge` `le` . Para obter mais informações, consulte [os operadores de comparação OData na Pesquisa Cognitiva Azure.](search-query-odata-comparison-operators.md) As expressões de comparação também são usadas para comparar distâncias entre coordenadas geo-espaciais usando a `geo.distance` função. Para obter mais informações, consulte [as funções geo-espaciais OData na Pesquisa Cognitiva Azure](search-query-odata-geo-spatial-functions.md).
- Os literais booleanos `true` `false` e. Estas constantes podem ser úteis às vezes quando geram filtros programáticas, mas de outra forma não tendem a ser usados na prática.
- Chamadas para funções booleanas, incluindo:
  - `geo.intersects`, que testa se um determinado ponto está dentro de um determinado polígono. Para obter mais informações, consulte [as funções geo-espaciais OData na Pesquisa Cognitiva Azure](search-query-odata-geo-spatial-functions.md).
  - `search.in`, que compara uma variável de campo ou intervalo com cada valor numa lista de valores. Para obter mais informações, consulte [a função OData `search.in` na Pesquisa Cognitiva Azure](search-query-odata-search-in-function.md).
  - `search.ismatch` e `search.ismatchscoring` , que executam operações de pesquisa de texto completo num contexto de filtro. Para obter mais informações, consulte [as funções de pesquisa completa de texto OData na Pesquisa Cognitiva Azure](search-query-odata-full-text-search-functions.md).
- Percursos de campo ou variáveis de alcance do `Edm.Boolean` tipo. Por exemplo, se o seu índice tiver um campo Boolean chamado `IsEnabled` e quiser devolver todos os documentos onde este campo `true` está, a sua expressão de filtro pode ser apenas o nome `IsEnabled` .
- Expressões booleanas em parênteses. A utilização de parênteses pode ajudar a determinar explicitamente a ordem de operações num filtro. Para obter mais informações sobre a precedência predefinida dos operadores OData, consulte a secção seguinte.

### <a name="operator-precedence-in-filters"></a>Precedência do operador em filtros

Se escrever uma expressão de filtro sem parênteses em torno das suas subexpressões, a Azure Cognitive Search irá avaliá-la de acordo com um conjunto de regras de precedência do operador. Estas regras baseiam-se nas quais os operadores são utilizados para combinar subexpressões. O quadro que se segue enumera grupos de operadores para, de forma mais elevada e mais baixa:

| Group | Operador(s) |
| --- | --- |
| Operadores lógicos | `not` |
| Operadores de comparação | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operadores lógicos | `and` |
| Operadores lógicos | `or` |

Um operador mais alto na tabela acima irá "ligar-se mais firmemente" aos seus operáticos do que a outros operadores. Por exemplo, `and` tem uma precedência superior à de `or` , e os operadores de comparação têm uma precedência superior à de qualquer um deles, pelo que as duas expressões seguintes são equivalentes:

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

O `not` operador tem a maior precedência de todas - ainda mais do que os operadores de comparação. É por isso que se tentar escrever um filtro como este:

```odata-filter-expr
    not Rating gt 5
```

Receberá esta mensagem de erro:

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

Este erro acontece porque o operador está associado apenas ao `Rating` campo, que é de tipo `Edm.Int32` , e não com toda a expressão de comparação. A correção é colocar a ópera de `not` parênteses:

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitações do tamanho do filtro

Existem limites para o tamanho e complexidade das expressões de filtro que pode enviar para a Azure Cognitive Search. Os limites baseiam-se aproximadamente no número de cláusulas na expressão do filtro. Uma boa orientação é que, se tiver centenas de cláusulas, corre o risco de ultrapassar o limite. Recomendamos que desenhe a sua aplicação de forma a não gerar filtros de tamanho ilimitado.

> [!TIP]
> A [ `search.in` utilização da função](search-query-odata-search-in-function.md) em vez de longas disjunções de comparações de igualdade pode ajudar a evitar o limite da cláusula de filtragem, uma vez que uma chamada de função conta como uma única cláusula.

## <a name="examples"></a>Exemplos

Encontre todos os hotéis com pelo menos um quarto com uma taxa base inferior a $200 que estão avaliados em ou acima de 4:

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

Encontre todos os hotéis que não o "Sea View Motel" que foram renovados desde 2010:

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

Encontre todos os hotéis que foram renovados em 2010 ou mais tarde. A data literal inclui informações sobre fuso horário para a hora padrão do Pacífico:  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

Encontre todos os hotéis que tenham estacionamento incluído e onde todos os quartos não fumam:

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- OU -  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

Encontre todos os hotéis que sejam de luxo ou incluam estacionamento e tenha uma classificação de 5:  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

Encontre todos os hotéis com a etiqueta "wifi" em pelo menos um quarto (onde cada quarto tem etiquetas armazenadas num `Collection(Edm.String)` campo):  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

Encontre todos os hotéis com quaisquer quartos:  

```odata-filter-expr
    $filter=Rooms/any()
```

Encontre todos os hotéis que não tenham quartos:

```odata-filter-expr
    $filter=not Rooms/any()
```

Encontre todos os hotéis a menos de 10 km de um determinado ponto de referência (onde `Location` está um campo de `Edm.GeographyPoint` tipo):

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

Encontre todos os hotéis dentro de um determinado viewport descrito como um polígono (onde `Location` está um campo de tipo Edm.GeographyPoint). O polígono deve ser fechado, o que significa que os conjuntos de primeiro e último ponto devem ser os mesmos. Além disso, [os pontos devem ser listados por ordem anti-horário](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

Encontre todos os hotéis onde o campo "Descrição" é nulo. O campo será nulo se nunca foi definido, ou se foi explicitamente definido para nulo:  

```odata-filter-expr
    $filter=Description eq null
```

Encontre todos os hotéis com nome igual a 'Sea View motel' ou 'Budget hotel'). Estas frases contêm espaços, e o espaço é um delimiter padrão. Pode especificar um limontinho alternativo em cotações simples como o terceiro parâmetro de cadeia:  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Encontre todos os hotéis com nome igual a 'Sea View motel' ou 'Budget hotel' separados por '|'):  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Encontre todos os hotéis onde todos os quartos tenham a etiqueta 'wifi' ou 'tub':

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

Encontre uma correspondência em frases dentro de uma coleção, tais como "toalheiros aquecidos" ou "secador de cabelo incluído" em etiquetas.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Encontre documentos com a palavra "cais". Esta consulta de filtro é idêntica a um [pedido de pesquisa](/rest/api/searchservice/search-documents) com `search=waterfront` .

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

Encontre documentos com a palavra "hostel" e classificação superior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Este pedido não poderia ser expresso sem a `search.ismatchscoring` função, uma vez que combina pesquisa de texto completo com operações de filtro usando `or` .

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Encontre documentos sem a palavra "luxo".

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

Encontre documentos com a frase "vista para o mar" ou classificação igual a 5. A `search.ismatchscoring` consulta será executada apenas contra campos e `HotelName` `Description` . Documentos que correspondam apenas à segunda cláusula da disjunção também serão devolvidos- hotéis com `Rating` igual a 5. Esses documentos serão devolvidos com pontuação igual a zero para deixar claro que não correspondem a nenhuma das partes pontuadas da expressão.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

Encontre hotéis onde os termos "hotel" e "aeroporto" não são mais do que cinco palavras separadas na descrição, e onde todos os quartos não fumam. Esta consulta utiliza a [linguagem de consulta lucene completa.](query-lucene-syntax.md)

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)