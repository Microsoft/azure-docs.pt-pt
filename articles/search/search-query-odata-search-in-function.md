---
title: Referência de função search.in OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para a utilização da função search.in em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113114"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Função `search.in` OData em Pesquisa Cognitiva Azure

Um cenário comum nas expressões de [filtro OData](query-odata-filter-orderby-syntax.md) é verificar se um único campo em cada documento é igual a um dos muitos valores possíveis. Por exemplo, é assim que algumas aplicações implementam o [aparador](search-security-trimming-for-azure-search.md) de segurança -- verificando um campo contendo uma ou mais identificações principais contra uma lista de iDs principais que representam o utilizador que emite a consulta. Uma maneira de escrever uma consulta como [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) esta é usar os e operadores:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

No entanto, há uma maneira mais `search.in` curta de escrever isto, usando a função:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Além de ser mais curto `search.in` e mais fácil de ler, a utilização também proporciona [benefícios](#bkmk_performance) de desempenho e evita certas [limitações de tamanho dos filtros](search-query-odata-filter.md#bkmk_limits) quando há centenas ou mesmo milhares de valores a incluir no filtro. Por esta razão, recomendamos vivamente a utilização `search.in` em vez de uma disjunção mais complexa das expressões de igualdade.

> [!NOTE]
> A versão 4.01 da norma OData introduziu recentemente o `search.in` [ `in` operador,](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)que tem um comportamento semelhante à função na Pesquisa Cognitiva Azure. No entanto, a Pesquisa Cognitiva Azure não suporta `search.in` este operador, pelo que deve utilizar a função.

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([Formulário Backus-Naur Estendido)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática da `search.in` função:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

A `search.in` função testa se um determinado campo de cordas ou variável de alcance é igual a uma de uma determinada lista de valores. A igualdade entre a variável e cada valor da lista é determinada de `eq` forma sensível a casos, da mesma forma que para o operador. Portanto, uma `search.in(myfield, 'a, b, c')` expressão como `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`é `search.in` equivalente a, exceto que irá produzir muito melhor desempenho.

Há duas sobrecargas `search.in` da função:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Os parâmetros são definidos na tabela seguinte:

| Nome do parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `variable` | `Edm.String` | Uma referência de campo de cordas (ou uma variável de alcance sobre um campo de recolha de cordas no caso em que `search.in` é usada dentro de uma `any` ou `all` expressão). |
| `valueList` | `Edm.String` | Uma cadeia contendo uma lista de valores delimitadas para combinar com o `variable` parâmetro. Se `delimiters` o parâmetro não for especificado, os delimitadores predefinidos são espaço e vírem. |
| `delimiters` | `Edm.String` | Uma corda onde cada personagem é tratado como `valueList` um separador ao analisar o parâmetro. O valor padrão deste `' ,'` parâmetro significa que quaisquer valores com espaços e/ou vírgulas entre eles serão separados. Se precisar de utilizar separadores que não os espaços e as vírgulas porque `'|'` os seus valores incluem esses caracteres, pode especificar delimitadores alternativos, como neste parâmetro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Desempenho de`search.in`

Se utilizar, `search.in`pode esperar um tempo de resposta sub-segundo quando o segundo parâmetro contiver uma lista de centenas ou milhares de valores. Não existe um limite explícito no número de `search.in`itens a que pode passar, embora ainda esteja limitado pelo tamanho máximo do pedido. No entanto, a latência vai crescer à medida que o número de valores aumenta.

## <a name="examples"></a>Exemplos

Encontre todos os hotéis com nome igual ao "Sea View motel" ou "Budget hotel". As frases contêm espaços, que é um delimitador padrão. Pode especificar um delimitador alternativo em cotações únicas como o terceiro parâmetro de cadeia:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Encontre todos os hotéis com nome igual a 'Sea View motel' ou 'Budget hotel' separados por '[/'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Encontre todos os hotéis com quartos com a etiqueta 'wifi' ou 'tub':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Encontre uma correspondência com frases dentro de uma coleção, tais como "toalheiros aquecidos" ou "secador de cabelo incluído" em etiquetas.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Encontre todos os hotéis sem a etiqueta 'motel' ou 'cabina':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
