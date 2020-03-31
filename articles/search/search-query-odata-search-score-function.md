---
title: Referência de função de pesquisa oData.pontuação
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para a utilização da função search.score em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113132"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Função `search.score` OData em Pesquisa Cognitiva Azure

Quando enviar uma consulta à Pesquisa Cognitiva Azure sem o parâmetro [ **$orderby,** ](search-query-odata-orderby.md)os resultados que voltarem serão classificados em ordem descendente por pontuação de relevância. Mesmo quando utilizar **$orderby,** a pontuação de relevância será usada para quebrar os laços por defeito. No entanto, por vezes é útil usar a pontuação de relevância como critério de classificação inicial, e alguns outros critérios como desempate. A `search.score` função permite-lhe fazer isto.

## <a name="syntax"></a>Sintaxe

A sintaxe `search.score` para `search.score()` **$orderby** é. A `search.score` função não tem parâmetros. Pode ser usado `asc` com `desc` o ou especificar de ordem de classificação, como qualquer outra cláusula no parâmetro **$orderby.** Pode aparecer em qualquer lugar da lista de critérios de tipo.

## <a name="example"></a>Exemplo

Ordenar hotéis `search.score` em `rating`ordem descendente por e, em seguida, em ordem ascendente por distância das coordenadas dadas para que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos seguintes  

- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
