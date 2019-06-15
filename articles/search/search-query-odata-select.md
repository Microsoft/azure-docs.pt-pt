---
title: Referência de selecione OData - Azure Search
description: Referência de linguagem de OData para selecionar a sintaxe em consultas de pesquisa do Azure.
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079681"
---
# <a name="odata-select-syntax-in-azure-search"></a>Sintaxe de OData $select no Azure Search

 Pode utilizar o [OData **$select** parâmetro](query-odata-filter-orderby-syntax.md) escolher quais campos serão incluídos nos resultados de pesquisa do Azure Search. Este artigo descreve a sintaxe de **$select** detalhadamente. Para obter mais informações sobre como utilizar **$select** ao apresentar os resultados da pesquisa, veja [como trabalhar com a pesquisa resulta no Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O **$select** parâmetro determina quais os campos para cada documento são devolvidos no conjunto de resultados de consulta. A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para o **$select** parâmetro:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

O **$select** parâmetro é fornecido em dois formatos:

1. Um único asterisco (`*`), indicando que devem ser devolvidos todos os campos recuperáveis, ou
1. Uma lista separada por vírgulas de caminhos de campo, identificar os campos que deve ser devolvido.

Ao utilizar o segundo formulário, só pode especificar os campos recuperáveis na lista.

Se listar um campo complexo sem especificar explicitamente os respetivos campos secundárias, todos os campos de secundárias recuperáveis serão incluídos no conjunto de resultados de consulta. Por exemplo, suponha o índice tem um `Address` campo com `Street`, `City`, e `Country` secundárias campos que são recuperáveis tudo. Se especificar `Address` no **$select**, os resultados da consulta incluirá todos os três campos secundárias.

## <a name="examples"></a>Exemplos

Incluem o `HotelId`, `HotelName`, e `Rating` campos de nível superior nos resultados, bem como a `City` inferiores campo de `Address`:

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

Incluir o `HotelName` campo de nível superior nos resultados, bem como todos os campos de frações de `Address`e o `Type` e `BaseRate` secundárias campos de cada objeto no `Rooms` coleção:

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

- [Como trabalhar com a pesquisa resulta no Azure Search](search-pagination-page-layout.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
