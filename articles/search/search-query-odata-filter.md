---
title: Referência do filtro OData
titleSuffix: Azure Cognitive Search
description: Referência linguística OData e sintaxe completa usada para criar expressões de filtro em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282890"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter sintaxe na Pesquisa Cognitiva de Azure

A Pesquisa Cognitiva Azure utiliza expressões de [filtro OData](query-odata-filter-orderby-syntax.md) para aplicar critérios adicionais a uma consulta de pesquisa, além de termos de pesquisa de texto completo. Este artigo descreve a sintaxe dos filtros em detalhe. Para obter informações mais gerais sobre quais são os filtros e como usá-los para realizar cenários específicos de consulta, consulte [Filtros em Pesquisa Cognitiva Azure](search-filters.md).

## <a name="syntax"></a>Sintaxe

Um filtro na linguagem OData é uma expressão booleana, que por sua vez pode ser um dos vários tipos de expressão, como mostra o seguinte EBNF ([Formulário Backus-Naur Estendido):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

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

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Os tipos de expressões booleanas incluem:

- Recolha de expressões de filtro utilizando `any` ou `all`. Estes aplicam critérios de filtro nos campos de recolha. Para mais informações, consulte [os operadores de recolha de OData em Pesquisa Cognitiva Azure](search-query-odata-collection-operators.md).
- Expressões lógicas que combinam outras `and`expressões booleanas usando os operadores, `or`e `not`. Para mais informações, consulte [os operadores lógicos OData em Pesquisa Cognitiva Azure](search-query-odata-logical-operators.md).
- Expressões de comparação, que comparam campos `eq`ou `ne` `gt`variáveis de gama a valores constantes usando os operadores, , `lt`, , `ge`e `le`. Para mais informações, consulte os operadores de [comparação OData em Pesquisa Cognitiva Azure](search-query-odata-comparison-operators.md). As expressões de comparação também são usadas para `geo.distance` comparar distâncias entre coordenadas geo-espaciais utilizando a função. Para mais informações, consulte [funções geoespaciais OData na Pesquisa Cognitiva Azure](search-query-odata-geo-spatial-functions.md).
- Os literais `true` `false`booleanos e. Estas constantes podem ser úteis às vezes quando geram filtros programáticamente, mas de outra forma não tendem a ser usadas na prática.
- Chamadas para funções booleanas, incluindo:
  - `geo.intersects`, que testa se um determinado ponto está dentro de um determinado polígono. Para mais informações, consulte [funções geoespaciais OData na Pesquisa Cognitiva Azure](search-query-odata-geo-spatial-functions.md).
  - `search.in`, que compara uma variável de campo ou de gama com cada valor numa lista de valores. Para mais informações, consulte a [função OData `search.in` na Pesquisa Cognitiva Azure](search-query-odata-search-in-function.md).
  - `search.ismatch`e `search.ismatchscoring`, que executam operações de pesquisa de texto completo num contexto de filtro. Para mais informações, consulte [as funções de pesquisa de texto completo OData na Pesquisa Cognitiva Azure](search-query-odata-full-text-search-functions.md).
- Percursos de campo ou `Edm.Boolean`variáveis de alcance de tipo. Por exemplo, se o seu índice `IsEnabled` tiver um campo Boolean chamado `true`e quiser devolver todos `IsEnabled`os documentos onde este campo está, a expressão do filtro pode ser apenas o nome .
- Expressões booleanas em parênteses. A utilização de parênteses pode ajudar a determinar explicitamente a ordem de operações num filtro. Para obter mais informações sobre a precedência predefinida dos operadores oDados, consulte a secção seguinte.

### <a name="operator-precedence-in-filters"></a>Precedência do operador nos filtros

Se escrever uma expressão de filtro sem parênteses em torno das suas subexpressões, a Pesquisa Cognitiva Azure irá avaliá-la de acordo com um conjunto de regras de precedência do operador. Estas regras baseiam-se nas empresas utilizadas para combinar subexpressões. O quadro seguinte lista grupos de operadores de maior a menor precedência:

| Grupo | Operadores |
| --- | --- |
| Operadores lógicos | `not` |
| Operadores de Comparação | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operadores lógicos | `and` |
| Operadores lógicos | `or` |

Um operador mais elevado no quadro acima irá "ligar-se mais firmemente" aos seus operands do que outros operadores. Por exemplo, `and` é de `or`maior precedência do que , e os operadores de comparação são de maior precedência do que qualquer um deles, pelo que as duas expressões seguintes são equivalentes:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

O `not` operador tem a maior precedência de todas- ainda maior do que os operadores de comparação. É por isso que se tentar escrever um filtro como este:

    not Rating gt 5

Receberá esta mensagem de erro:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Este erro acontece porque o operador `Rating` está associado apenas `Edm.Int32`ao campo, que é de tipo, e não a toda a expressão de comparação. A correção é colocar o `not` operand de parênteses:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitações do tamanho do filtro

Existem limites para o tamanho e complexidade das expressões de filtro que pode enviar para a Pesquisa Cognitiva Azure. Os limites baseiam-se aproximadamente no número de cláusulas na expressão do filtro. Uma boa orientação é que, se tiver centenas de cláusulas, está em risco de ultrapassar o limite. Recomendamos que desenháu a sua aplicação de forma a não gerar filtros de tamanho ilimitado.

> [!TIP]
> A utilização [ `search.in` da função](search-query-odata-search-in-function.md) em vez de longas disjunções de comparações de igualdade pode ajudar a evitar o limite da cláusula de filtro, uma vez que uma chamada de função conta como uma única cláusula.

## <a name="examples"></a>Exemplos

Encontre todos os hotéis com pelo menos um quarto com uma taxa base inferior a $200 que estejam avaliados em ou acima de 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Encontre todos os hotéis que não o "Sea View Motel" que foram renovados desde 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Encontre todos os hotéis que foram renovados em 2010 ou mais tarde. A data literal inclui informações sobre fuso horário para o Horário Normal do Pacífico:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Encontre todos os hotéis que tenham estacionamento incluído e onde todos os quartos não fumam:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \-OU-  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Encontre todos os hotéis que sejam luxuosos ou incluam estacionamento e tenha uma classificação de 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Encontre todos os hotéis com a etiqueta "wifi" em pelo menos `Collection(Edm.String)` um quarto (onde cada quarto tem etiquetas armazenadas num campo):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Encontre todos os hotéis com quaisquer quartos:  

    $filter=Rooms/any()

Encontre todos os hotéis que não têm quartos:

    $filter=not Rooms/any()

Encontre todos os hotéis num raio `Location` de 10 `Edm.GeographyPoint`km de um determinado ponto de referência (onde é um campo de tipo):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Encontre todos os hotéis dentro de um `Location` determinado viewport descrito como um polígono (onde está um campo de tipo Edm.GeographyPoint). O polígono deve ser fechado, o que significa que os conjuntos de primeiro e último ponto devem ser os mesmos. Além disso, [os pontos devem ser enumerados no sentido contrário ao dos ponteiros do relógio](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Encontre todos os hotéis onde o campo "Descrição" é nulo. O campo será nulo se nunca tiver sido definido, ou se foi explicitamente definido para nulo:  

    $filter=Description eq null

Encontre todos os hotéis com nome igual ao 'Sea View motel' ou 'Budget hotel'). Estas frases contêm espaços, e o espaço é um delimitador padrão. Pode especificar um delimitador alternativo em cotações únicas como o terceiro parâmetro de cadeia:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Encontre todos os hotéis com nome igual a 'Sea View motel' ou 'Budget hotel' separados por '[/'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Encontre todos os hotéis onde todos os quartos tenham a etiqueta 'wifi' ou 'tub':

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Encontre uma correspondência com frases dentro de uma coleção, tais como "toalheiros aquecidos" ou "secador de cabelo incluído" em etiquetas.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Encontre documentos com a palavra "cais". Esta consulta de filtro é idêntica `search=waterfront`a um pedido de [pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com .

    $filter=search.ismatchscoring('waterfront')

Encontre documentos com a palavra "hostel" e classificação maior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Este pedido não podia ser `search.ismatchscoring` expresso sem a função, uma `or`vez que combina pesquisa de texto completo com operações de filtro utilizando .

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Encontre documentos sem a palavra "luxo".

    $filter=not search.ismatch('luxury')

Encontre documentos com a frase "vista para o mar" ou classificação igual a 5. A `search.ismatchscoring` consulta será executada apenas `HotelName` `Description`contra campos e . Documentos que correspondam apenas à segunda cláusula da disjunção `Rating` também serão devolvidos. Esses documentos serão devolvidos com pontuação igual a zero para deixar claro que não correspondem a nenhuma das partes pontuadas da expressão.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Encontre hotéis onde os termos "hotel" e "aeroporto" não estejam a lém de cinco palavras de diferença na descrição, e onde todos os quartos não fumam. Esta consulta utiliza toda a [linguagem lucene.](query-lucene-syntax.md)

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
