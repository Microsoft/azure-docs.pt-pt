---
title: Referência seleciona OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e referência linguística para seleção explícita de campos para voltar nos resultados de pesquisa de consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113096"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select sintaxe na Pesquisa Cognitiva de Azure

 Pode utilizar o [parâmetro OData **$select** ](query-odata-filter-orderby-syntax.md) para escolher quais os campos a incluir nos resultados de pesquisa da Pesquisa Cognitiva Azure. Este artigo descreve a sintaxe de **$select** em detalhe. Para obter informações mais gerais sobre como usar **$select** ao apresentar resultados de pesquisa, consulte Como trabalhar com os resultados da [pesquisa em Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$select** determina quais os campos para cada documento devolvidos no conjunto de resultados de consulta. O seguinte EBNF ( Forma Estendida de[Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática para o parâmetro **$select:**

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

O parâmetro **$select** vem de duas formas:

1. Uma única`*`estrela ( ), indicando que todos os campos recuperáveis devem ser devolvidos, ou
1. Uma lista separada de caminhos de campo separados, identificando quais os campos que devem ser devolvidos.

Ao utilizar o segundo formulário, só pode especificar os campos recuperáveis da lista.

Se listar um campo complexo sem especificar explicitamente os seus sub-campos, todos os sub-campos recuperáveis serão incluídos no conjunto de resultados da consulta. Por exemplo, assuma `Address` que `Street` `City`o `Country` seu índice tem um campo com , e sub-campos que são todos recuperáveis. Se especificar `Address` em **$select,** os resultados da consulta incluirão os três subcampos.

## <a name="examples"></a>Exemplos

Incluir `HotelId`os `HotelName`campos `Rating` de nível superior nos resultados, bem como o `City` subcampo de: `Address`

    $select=HotelId, HotelName, Rating, Address/City

Um resultado de exemplo pode parecer este:

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

Incluir `HotelName` o campo de alto nível nos resultados, `Address`bem `Type` como `BaseRate` todos os sub-campos `Rooms` de, e os subcampos e sub-campos de cada objeto na coleção:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Um resultado de exemplo pode parecer este:

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

- [Como trabalhar com os resultados da pesquisa em Pesquisa Cognitiva Azure](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
