---
title: Referência selecionada o OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e referência linguística para a seleção explícita de campos para retorno nos resultados de pesquisa de consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 54b6ae227fc4b3b951717799660543c02874dda0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919663"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select sintaxe na pesquisa cognitiva do Azure

 Pode utilizar o [parâmetro OData **$select** ](query-odata-filter-orderby-syntax.md) para escolher quais os campos a incluir nos resultados de pesquisa da Azure Cognitive Search. Este artigo descreve a sintaxe de **$select** em detalhe. Para obter informações mais gerais sobre como utilizar **$select** ao apresentar resultados de pesquisa, consulte [Como trabalhar com os resultados da pesquisa na Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

O **parâmetro $select** determina quais os campos para cada documento que são devolvidos no conjunto de resultados de consulta. O seguinte EBNF[(Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática para o parâmetro **$select:**

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

O **parâmetro $select** vem de duas formas:

1. Uma única estrela , `*` indicando que todos os campos recuperáveis devem ser devolvidos, ou
1. Uma lista separada por vírgulas de caminhos de campo, identificando quais campos devem ser devolvidos.

Ao utilizar o segundo formulário, só poderá especificar campos recuperáveis na lista.

Se listar um campo complexo sem especificar explicitamente os seus sub-campos, todos os sub-campos recuperáveis serão incluídos no conjunto de resultados de consulta. Por exemplo, assuma que o seu índice tem um `Address` campo com `Street` `City` `Country` sub-campos e sub-campos que são todos recuperáveis. Se especificar `Address` em **$select,** os resultados da consulta incluirão os três sub-campos.

## <a name="examples"></a>Exemplos

Incluir os `HotelId` campos , e de alto `HotelName` `Rating` nível nos resultados, bem como o `City` sub-campo `Address` de:

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

Um resultado exemplo pode ser assim:

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

Inclua o `HotelName` campo de alto nível nos resultados, bem como todos os sub-campos `Address` de, e os `Type` `BaseRate` sub-campos e sub-campos de cada objeto na `Rooms` coleção:

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

Um resultado exemplo pode ser assim:

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

- [Como trabalhar com resultados de pesquisa na Azure Cognitive Search](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)