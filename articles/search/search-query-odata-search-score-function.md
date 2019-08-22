---
title: Pesquisa de OData. referência de função de Pontuação-Azure Search
description: Pesquisa de OData. função de pontuação em consultas Azure Search.
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647523"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Função `search.score` OData no Azure Search

Quando você envia uma consulta para Azure Search sem o [parâmetro **$OrderBy** ](search-query-odata-orderby.md), os resultados que retornam serão classificados em ordem decrescente por Pontuação de relevância. Mesmo quando você usa **$OrderBy**, a pontuação de relevância será usada para quebrar as ligações por padrão. No entanto, às vezes, é útil usar a pontuação de relevância como um critério de classificação inicial e alguns outros critérios como o desempate. A `search.score` função permite que você faça isso.

## <a name="syntax"></a>Sintaxe

A sintaxe para `search.score` no **$OrderBy** é `search.score()`. A função `search.score` não pega nenhum parâmetro. Ele pode ser usado com o `asc` especificador de `desc` ordem de classificação, assim como qualquer outra cláusula no parâmetro **$OrderBy** . Ele pode aparecer em qualquer lugar na lista de critérios de classificação.

## <a name="example"></a>Exemplo

Classifique os hotéis em ordem decrescente `rating`por `search.score` e e, em seguida, em ordem crescente por distância das coordenadas fornecidas para que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos Seguintes  

- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
