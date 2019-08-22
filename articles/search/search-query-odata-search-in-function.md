---
title: Referência da função search.in do OData-Azure Search
description: Função search.in OData em consultas Azure Search.
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
ms.openlocfilehash: 8bac0205fa2de8378abaa4d9e8ba8e05ea69192e
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647929"
---
# <a name="odata-searchin-function-in-azure-search"></a>Função `search.in` OData no Azure Search

Um cenário comum em [expressões de filtro OData](query-odata-filter-orderby-syntax.md) é verificar se um único campo em cada documento é igual a um dos muitos valores possíveis. Por exemplo, é assim que alguns aplicativos implementam a [remoção de segurança](search-security-trimming-for-azure-search.md) , verificando um campo que contém uma ou mais IDs de entidade em relação a uma lista de IDs de entidade que representa o usuário que está emitindo a consulta. Uma maneira de escrever uma consulta como esta é usar os [`eq`](search-query-odata-comparison-operators.md) operadores e: [`or`](search-query-odata-logical-operators.md)

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

No entanto, há uma maneira mais curta de escrever isso, usando `search.in` a função:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Além de ser mais curto e fácil de ler, `search.in` o uso do também fornece [benefícios de desempenho](#bkmk_performance) e evita determinadas [limitações de tamanho dos filtros](search-query-odata-filter.md#bkmk_limits) quando há centenas ou até milhares de valores a serem incluídos no filtro. Por esse motivo, é altamente recomendável `search.in` usar em vez de uma disjunção mais complexa de expressões de igualdade.

> [!NOTE]
> A versão 4, 1 do padrão OData introduziu recentemente o [ `in` operador](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), que tem comportamento `search.in` semelhante à função no Azure Search. No entanto, Azure Search não oferece suporte a esse operador, portanto, `search.in` você deve usar a função em vez disso.

## <a name="syntax"></a>Sintaxe

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática `search.in` da função:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md) para o EBNF completo.

A `search.in` função testa se um determinado campo de cadeia de caracteres ou variável de intervalo é igual a uma de uma determinada lista de valores. A igualdade entre a variável e cada valor na lista é determinada de uma maneira que diferencia maiúsculas de minúsculas, da mesma `eq` maneira que para o operador. Portanto, uma expressão `search.in(myfield, 'a, b, c')` como é equivalente `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`a, exceto `search.in` que produzirá um desempenho muito melhor.

Há duas sobrecargas da `search.in` função:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Type | Descrição |
| --- | --- | --- |
| `variable` | `Edm.String` | Uma referência de campo de cadeia de caracteres (ou uma variável de intervalo em um campo de `search.in` coleção de cadeia de `any` caracteres `all` no caso em que é usada dentro de uma expressão ou). |
| `valueList` | `Edm.String` | Uma cadeia de caracteres que contém uma lista delimitada de valores para `variable` corresponder ao parâmetro. Se o `delimiters` parâmetro não for especificado, os delimitadores padrão serão espaço e vírgula. |
| `delimiters` | `Edm.String` | Uma cadeia de caracteres em que cada caractere é tratado como um separador ao analisar o `valueList` parâmetro. O valor padrão desse parâmetro é `' ,'` o que significa que todos os valores com espaços e/ou vírgulas entre eles serão separados. Se você precisar usar separadores diferentes de espaços e vírgulas porque seus valores incluem esses caracteres, você pode especificar delimitadores alternativos, `'|'` como nesse parâmetro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Desempenho de`search.in`

Se você usar `search.in`o, poderá esperar um tempo de resposta de segundo plano quando o segundo parâmetro contiver uma lista de centenas ou milhares de valores. Não há nenhum limite explícito para `search.in`o número de itens que você pode passar, embora ainda esteja limitado pelo tamanho máximo da solicitação. No entanto, a latência aumentará conforme o número de valores aumentar.

## <a name="examples"></a>Exemplos

Localize todos os hotéis com o nome igual a ' Sea View Motel ' ou ' Budget Orca '. As frases contêm espaços, que é um delimitador padrão. Você pode especificar um delimitador alternativo entre aspas simples como o terceiro parâmetro de cadeia de caracteres:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Localize todos os hotéis com o nome igual ao ' Sea View Motel ' ou ao ' Budget Hotel ', separados por ' | '):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Localize todos os hotéis com salas que têm a marca ' WiFi ' ou ' Tub ':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Encontre uma correspondência em frases em uma coleção, como "racks de toalha aquecidos" ou "hairdryer incluídos" nas marcas.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Localize todos os hotéis sem a marca ' motel ' ou ' cabin':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
