---
title: Referência do OData Select-Azure Search
description: Referência de linguagem OData para a sintaxe SELECT em consultas Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647879"
---
# <a name="odata-select-syntax-in-azure-search"></a>Sintaxe de $select OData no Azure Search

 Você pode usar o [parâmetro **$Select** OData](query-odata-filter-orderby-syntax.md) para escolher quais campos incluir nos resultados da pesquisa de Azure Search. Este artigo descreve a sintaxe de **$Select** em detalhes. Para obter mais informações gerais sobre como usar **$Select** ao apresentar resultados da pesquisa, consulte [como trabalhar com resultados da pesquisa em Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$Select** determina quais campos de cada documento são retornados no conjunto de resultados da consulta. O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para o parâmetro **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md) para o EBNF completo.

O parâmetro **$Select** é fornecido em duas formas:

1. Uma única estrela (`*`), indicando que todos os campos recuperáveis devem ser retornados ou
1. Uma lista separada por vírgulas de caminhos de campo, identificando quais campos devem ser retornados.

Ao usar o segundo formulário, você só pode especificar campos recuperáveis na lista.

Se você listar um campo complexo sem especificar os subcampos explicitamente, todos os subcampos recuperáveis serão incluídos no conjunto de resultados da consulta. Por exemplo, suponha que o índice tenha `Address` um campo `Street`com `City`os subcampos, e `Country` que são todos recuperáveis. Se você especificar `Address` em **$Select**, os resultados da consulta incluirão todos os três subcampos.

## <a name="examples"></a>Exemplos

Inclua os `HotelId`campos `HotelName`, e `Rating` de nível superior nos resultados, bem como o `City` subcampo de `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Um resultado de exemplo pode ser assim:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Inclua o `HotelName` campo de nível superior nos resultados, bem como todos os subcampos de `Address`e os `Type` subcampos `BaseRate` e de cada objeto na `Rooms` coleção:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Um resultado de exemplo pode ser assim:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes  

- [Como trabalhar com resultados de pesquisa em Azure Search](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
