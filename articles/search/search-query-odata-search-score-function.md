---
title: Referência de função de search.score OData - Azure Search
description: Função de search.score de OData em consultas de pesquisa do Azure.
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079694"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` função no Azure Search

Quando enviar uma consulta para o Azure Search sem o [ **$orderby** parâmetro](search-query-odata-orderby.md), os resultados retornados serão classificados em ordem decrescente pela pontuação de relevância. Até mesmo quando usar **$orderby**, a pontuação de relevância será utilizada para dividir os empates por predefinição. No entanto, às vezes, é útil utilizar a pontuação de relevância como um critério de ordenação inicial e alguns outros critérios, como o desempate. O `search.score` função permite que faça isso.

## <a name="syntax"></a>Sintaxe

A sintaxe para `search.score` no **$orderby** é `search.score()`. A função `search.score` não assume quaisquer parâmetros. Pode ser utilizado com o `asc` ou `desc` especificador de ordem de classificação, assim como qualquer outra cláusula na **$orderby** parâmetro. Podem aparecer em qualquer lugar na lista de critérios de ordenação.

## <a name="example"></a>Exemplo

Ordenar hotéis em ordem decrescente pela `search.score` e `rating`e, em seguida, em ordem por distância crescente das coordenadas de determinado, para que entre dois hotéis com classificações idênticas, aquele mais próxima é listada em primeiro lugar:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos Seguintes  

- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
