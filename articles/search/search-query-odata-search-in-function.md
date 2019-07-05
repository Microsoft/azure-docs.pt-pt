---
title: Referência de função de search.in OData - Azure Search
description: Função de search.in de OData em consultas de pesquisa do Azure.
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449973"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` função no Azure Search

Um cenário comum em [expressões de filtro de OData](query-odata-filter-orderby-syntax.md) é verificar se um único campo de cada documento é igual a um dos muitos valores possíveis. Por exemplo, isso é como implementam alguns aplicativos [remoção de segurança](search-security-trimming-for-azure-search.md) – verificando um campo que contém um ou mais IDs principal numa lista de IDs de principal representando o usuário a emitir a consulta. Uma forma de escrever uma consulta como esta é a utilizar o [ `eq` ](search-query-odata-comparison-operators.md) e [ `or` ](search-query-odata-logical-operators.md) operadores:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

No entanto, há uma forma mais curta de escrever isso, usando o `search.in` função:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Além de ser menor e mais fácil de ler, usando `search.in` também fornece [benefícios de desempenho](#bkmk_performance) e evita a determinados [limitações de filtros de tamanho](search-query-odata-filter.md#bkmk_limits) quando existem centenas ou mesmo milhares de valores para incluir no filtro. Por esse motivo, recomendamos vivamente utilizar `search.in` em vez de uma disjunção mais complexa das expressões de igualdade.

> [!NOTE]
> Versão 4.01 do padrão OData recentemente introduziu o [ `in` operador](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), que tem um comportamento semelhante como o `search.in` função no Azure Search. No entanto, Azure Search não suporta este operador, deve usar o `search.in` funcionar em vez disso.

## <a name="syntax"></a>Sintaxe

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática do `search.in` função:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

O `search.in` função testa se um campo de determinada seqüência de caracteres ou variável de alcance é igual a uma determinada lista de valores. Igualdade entre cada valor na lista e a variável é determinada de forma diferencia maiúsculas de minúsculas, da mesma forma como para o `eq` operador. Por conseguinte, como uma expressão `search.in(myfield, 'a, b, c')` é equivalente ao `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, exceto pelo fato de `search.in` resulta num desempenho muito melhor.

Existem duas sobrecargas do `search.in` função:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Type | Descrição |
| --- | --- | --- |
| `variable` | `Edm.String` | Uma referência de campo de cadeia de caracteres (ou uma variável de alcance ao longo de um campo de coleção de cadeia de caracteres no caso em que `search.in` é utilizada dentro de um `any` ou `all` expressão). |
| `valueList` | `Edm.String` | Uma cadeia de caracteres que contém uma lista delimitada de valores a correspondência com a `variable` parâmetro. Se o `delimiters` parâmetro não for especificado, os delimitadores de padrão são espaço e vírgula. |
| `delimiters` | `Edm.String` | Uma cadeia de caracteres em que cada caractere é tratado como um separador ao analisar o `valueList` parâmetro. O valor padrão desse parâmetro é `' ,'` que significa que quaisquer valores com espaços de e/ou vírgulas entre eles ficarão separados. Se precisar de utilizar os separadores que não sejam espaços e vírgulas porque os valores incluem aqueles caracteres, pode especificar como delimitadores alternativos `'|'` neste parâmetro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Desempenho de `search.in`

Se usar `search.in`, pode esperar quando o segundo parâmetro contém uma lista de centenas ou milhares de valores de tempo de resposta de frações de segundos. Não tem qualquer limite explícito sobre o número de itens, pode passar para `search.in`, apesar de ainda está limitado pelo tamanho máximo do pedido. No entanto, a latência aumentará à medida que aumenta o número de valores.

## <a name="examples"></a>Exemplos

Encontre todos os hotéis com nome igual a 'Motel vista mar' ou 'Hotel orçamento'. Frases contenham espaços, que é um delimitador de predefinição. Pode especificar um delimitador alternativo plicas como o terceiro parâmetro de cadeia de caracteres:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Localizar todos os hotéis com nome igual a 'Motel vista mar' ou 'hotel orçamento"separados por" | "):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Localize todos os hotéis com ambientes que tenham a tag 'Wi-Fi' ou "tub":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Encontre uma correspondência com frases dentro de uma coleção, como 'toalhas exaltados racks' ou "hairdryer incluídos" em etiquetas.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Localize todos os hotéis sem o hotel"tag" ou "cabine é":

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
