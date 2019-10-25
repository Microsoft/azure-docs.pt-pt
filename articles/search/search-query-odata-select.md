---
title: Referência do OData Select
titleSuffix: Azure Cognitive Search
description: Referência de linguagem OData para a sintaxe SELECT nas consultas do Azure Pesquisa Cognitiva.
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
ms.openlocfilehash: 7786974f3d39f9cbc81e1ffea955156d623f1476
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793254"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Sintaxe de $select OData no Azure Pesquisa Cognitiva

 Você pode usar o [parâmetro **$Select** OData](query-odata-filter-orderby-syntax.md) para escolher quais campos incluir nos resultados da pesquisa do Azure pesquisa cognitiva. Este artigo descreve a sintaxe de **$Select** em detalhes. Para obter mais informações gerais sobre como usar **$Select** ao apresentar os resultados da pesquisa, consulte [como trabalhar com os resultados da pesquisa no Azure pesquisa cognitiva](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$Select** determina quais campos de cada documento são retornados no conjunto de resultados da consulta. O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para o parâmetro **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

O parâmetro **$Select** é fornecido em duas formas:

1. Uma única estrela (`*`), indicando que todos os campos recuperáveis devem ser retornados ou
1. Uma lista separada por vírgulas de caminhos de campo, identificando quais campos devem ser retornados.

Ao usar o segundo formulário, você só pode especificar campos recuperáveis na lista.

Se você listar um campo complexo sem especificar os subcampos explicitamente, todos os subcampos recuperáveis serão incluídos no conjunto de resultados da consulta. Por exemplo, suponha que o índice tenha um campo de `Address` com `Street`, `City`e `Country` subcampos que sejam recuperáveis. Se você especificar `Address` em **$Select**, os resultados da consulta incluirão todos os três subcampos.

## <a name="examples"></a>Exemplos

Inclua os campos `HotelId`, `HotelName`e `Rating` de nível superior nos resultados, bem como o subcampo `City` do `Address`:

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

Inclua o `HotelName` campo de nível superior nos resultados, bem como todos os subcampos de `Address`e os subcampos `Type` e `BaseRate` de cada objeto na coleção de `Rooms`:

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

## <a name="next-steps"></a>Passos seguintes  

- [Como trabalhar com os resultados da pesquisa no Azure Pesquisa Cognitiva](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
