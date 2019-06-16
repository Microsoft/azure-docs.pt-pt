---
title: Referência de filtro de OData - Azure Search
description: Referência de linguagem de OData para a sintaxe de filtro em consultas de pesquisa do Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: c44645ebcbf8808cc929bfaa0c3cb0d3ee9c90cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079915"
---
# <a name="odata-filter-syntax-in-azure-search"></a>Sintaxe de OData $filter no Azure Search

Utiliza o Azure Search [expressões de filtro de OData](query-odata-filter-orderby-syntax.md) para aplicar critérios adicionais para uma consulta de pesquisa, além de termos de pesquisa de texto completo. Este artigo descreve a sintaxe de filtros em detalhes. Para obter mais informações sobre o que são os filtros e como utilizá-los para concretizar os cenários de consulta específica, consulte [filtros no Azure Search](search-filters.md).

## <a name="syntax"></a>Sintaxe

Um filtro na linguagem de OData é uma expressão booleana, que por sua vez pode ser um dos vários tipos de expressão, conforme exibido a seguir EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

Os tipos de expressões booleanas incluem:

- Expressões de filtro de coleção usando `any` ou `all`. Estes aplicam-se os critérios de filtro para os campos de coleção. Para obter mais informações, consulte [operadores de coleção OData na Azure Search](search-query-odata-collection-operators.md).
- Expressões lógicas que combinam outras expressões booleanas utilizando os operadores `and`, `or`, e `not`. Para obter mais informações, consulte [OData operadores lógicos no Azure Search](search-query-odata-logical-operators.md).
- Expressões de comparação, o qual comparam campos ou variáveis para utilizar os operadores de valores de constantes de alcance `eq`, `ne`, `gt`, `lt`, `ge`, e `le`. Para obter mais informações, consulte [operadores de comparação de OData no Azure Search](search-query-odata-comparison-operators.md). Expressões de comparação também são usadas para comparar as distâncias entre coordenadas geoespacial utilizando o `geo.distance` função. Para obter mais informações, consulte [OData funções geoespaciais no Azure Search](search-query-odata-geo-spatial-functions.md).
- Os literais booleanos `true` e `false`. Essas constantes podem ser útil, por vezes, ao gerar programaticamente os filtros, mas caso contrário, não tendem a ser utilizado na prática.
- Chamadas de funções booleanas, incluindo:
  - `geo.intersects`, que testa se um determinado ponto é dentro de um determinado polígono. Para obter mais informações, consulte [OData funções geoespaciais no Azure Search](search-query-odata-geo-spatial-functions.md).
  - `search.in`, que compara uma variável de campo ou intervalo com cada valor de uma lista de valores. Para obter mais informações, consulte [OData `search.in` função no Azure Search](search-query-odata-search-in-function.md).
  - `search.ismatch` e `search.ismatchscoring`, qual executar operações de pesquisa em texto completo num contexto de filtro. Para obter mais informações, consulte [funções de pesquisa em texto completo de OData no Azure Search](search-query-odata-full-text-search-functions.md).
- Caminhos de campo ou variáveis do tipo de alcance `Edm.Boolean`. Por exemplo, se o seu índice tem um campo Booleano chamado `IsEnabled` e pretende devolver todos os documentos em que este campo é `true`, a expressão de filtro pode ser o nome `IsEnabled`.
- Expressões booleanas parênteses. Utilizar parênteses pode ajudar a determinar explicitamente a ordem de operações num filtro. Para obter mais informações sobre a precedência de padrão dos operadores de OData, veja a secção seguinte.

### <a name="operator-precedence-in-filters"></a>Precedência de operador em filtros

Se escrever uma expressão de filtro com sem parênteses em torno de seu as subexpressões, o Azure Search será avaliá-lo, de acordo com um conjunto de regras de precedência de operador. Estas regras são baseiam-se de que operadores são usados para combinar as subexpressões. A tabela seguinte apresenta uma lista de grupos de operadores em ordem, do mais alto ao mais baixa precedência:

| Grupo | Operator(s) |
| --- | --- |
| Operadores lógicos | `not` |
| Operadores de Comparação | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operadores lógicos | `and` |
| Operadores lógicos | `or` |

Um operador que é maior na tabela acima irá "vincular mais rigidamente" para seus operandos que outros operadores. Por exemplo, `and` é de precedência superior `or`, e operadores de comparação são de precedência superior a qualquer um deles, pelo que as seguintes duas expressões são equivalentes:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

O `not` operador tem precedência máxima de tudo – até mesmo superior do que os operadores de comparação. É por isso que se tentar escrever um filtro semelhante a esta:

    not Rating gt 5

Obterá esta mensagem de erro:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Este erro ocorre porque o operador está associado apenas o `Rating` campo, o que é do tipo `Edm.Int32`e não com a expressão de comparação de todo. A correção é colocar o operando de `not` parênteses:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitações de tamanho de filtro

Existem limites para o tamanho e a complexidade de expressões de filtro que pode enviar para o Azure Search. Os limites baseiam-se mais ou menos o número de cláusulas na sua expressão de filtro. Uma boa diretriz é que se tiver centenas de cláusulas, está em risco de exceder o limite. Recomendamos a criação do seu aplicativo de tal forma que ele não gera filtros de tamanho não vinculado.

> [!TIP]
> Usando [a `search.in` função](search-query-odata-search-in-function.md) em vez de longo disjunctions de igualdade comparações podem ajudar a evitar o limite de cláusula de filtro, uma vez que uma chamada de função é contabilizado como uma única cláusula.

## <a name="examples"></a>Exemplos

Localize todos os hotéis com, pelo menos, uma sala com uma taxa base com menos de US $200 que são classificados igual ou superior a 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Localize hotéis todos os que não seja "Mar vista Motel" que foi renovated desde 2010:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Localize hotéis todos os que foram renovated no 2010 ou posterior. O literal de datetime inclui informações de fuso horário para hora padrão do Pacífico:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Localizar hotéis todos os que têm de estacionamento incluído e em que todos os ambientes são não fumar:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- OU-  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Localize hotéis todos os que são luxo ou incluem estacionamento e tem uma classificação de 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Localizar todos os hotéis com a etiqueta "Wi-Fi" em, pelo menos, uma sala (em que cada espaço tem etiquetas armazenadas num `Collection(Edm.String)` campo):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Localize todos os hotéis com todos os ambientes:  

    $filter=Rooms/any()

Localize hotéis todos os que não têm os ambientes:

    $filter=not Rooms/any()

Localizar todos os hotéis dentro de 10 quilómetros de distância de um ponto de referência especificado (em que `Location` é um campo do tipo `Edm.GeographyPoint`):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Localizar todos os hotéis dentro de uma determinado da janela viewport descrita como um polígono (onde `Location` é um campo do tipo geographypoint). O polígono tem de ser fechado, que significa que o primeiro e último ponto conjuntos têm de ser iguais. Além disso, [os pontos devem ser listados na ordem para a esquerda](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Encontre todos os hotéis em que o campo de "Descrição" é nulo. O campo será nulo, se nunca foi definida ou se foi definido explicitamente como nulo:  

    $filter=Description eq null

Encontre todos os hotéis com nome igual a 'Motel vista mar' ou "Hotel orçamento"). Estas expressões contenham espaços e o espaço é um delimitador de predefinição. Pode especificar um delimitador alternativo plicas como o terceiro parâmetro de cadeia de caracteres:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Localizar todos os hotéis com nome igual a 'Motel vista mar' ou 'hotel orçamento"separados por" | "):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Localize todos os hotéis em que todos os ambientes têm a etiqueta "Wi-Fi" ou "tub":

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Encontre uma correspondência com frases dentro de uma coleção, como 'toalhas exaltados racks' ou "hairdryer incluídos" em etiquetas.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Encontre os documentos com a palavra "frente marítima". Esta consulta de filtro é idêntica para um [solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Encontre os documentos com hostel"palavra" e classificação superior ou igual a 4 ou documentos com a palavra "motel" e classificação igual a 5. Este pedido não foi possível ser expressado sem o `search.ismatchscoring` funcionar, uma vez que combina pesquisa de texto completo com operações de filtro com `or`.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Encontre documentos sem a palavra "luxo".

    $filter=not search.ismatch('luxury')

Encontre os documentos com a frase "Oceano view" ou uma classificação igual a 5. O `search.ismatchscoring` consulta será executada apenas em relação a campos `HotelName` e `Description`. Apenas a segunda cláusula da disjunção de correspondentes serão devolvidos documentos demasiado – hotéis com `Rating` igual a 5. Esses documentos serão devolvidos com score igual a zero para torná-lo claro que não correspondem qualquer uma das partes com a pontuação da expressão.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Localize hotéis onde o hotel"termos" e "aeroporto" são palavras não mais do que cinco afastadas na descrição e, em que todos os ambientes são não fumar. Esta consulta utiliza a [completa de linguagem de consulta Lucene](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
