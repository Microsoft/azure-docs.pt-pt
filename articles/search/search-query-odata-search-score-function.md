---
title: Referência de função de pesquisa.pontuação OData
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74113132"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Função OData `search.score` em Pesquisa Cognitiva Azure

Quando envia uma consulta para a Azure Cognitive Search sem o parâmetro [ **$orderby,** ](search-query-odata-orderby.md)os resultados que voltarem serão classificados em ordem descendente por pontuação de relevância. Mesmo quando utilizar **$orderby,** a pontuação de relevância será usada para quebrar laços por defeito. No entanto, por vezes é útil utilizar a pontuação de relevância como critérios de classificação inicial, e alguns outros critérios como o desempate. A `search.score` função permite-lhe fazer isto.

## <a name="syntax"></a>Syntax

A sintaxe para `search.score` **em $orderby** é `search.score()` . A função `search.score` não requer parâmetros. Pode ser usado com o `asc` especificador ou `desc` de ordem de classificação, assim como qualquer outra cláusula no parâmetro **$orderby.** Pode aparecer em qualquer lugar da lista de critérios de classificação.

## <a name="example"></a>Exemplo

Ordenar hotéis em ordem descendente por `search.score` `rating` e, em seguida, em ordem ascendente por ordem ascendente das coordenadas dadas para que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Próximos passos  

- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
