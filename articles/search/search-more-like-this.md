---
title: maisLikeThis (pré-visualização) recurso de consulta
titleSuffix: Azure Cognitive Search
description: Descreve a funcionalidade maisLikeThis (pré-visualização), que está disponível nas versões de pré-visualização da Azure Cognitive Search REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1c2f8058a85bbc0643ed31a7dc910339d0f6d9dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94697055"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (pré-visualização) em Azure Cognitive Search

> [!IMPORTANT] 
> Esta funcionalidade encontra-se atualmente em visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [versão REST API 2020-06-30-Preview](search-api-preview.md) fornece esta funcionalidade. Atualmente não existe porta ou suporte .NET SDK.

`moreLikeThis=[key]` é um parâmetro de consulta na [API de Documentos de Busca](/rest/api/searchservice/search-documents) que encontra documentos semelhantes ao documento especificado pela chave do documento. Quando um pedido de pesquisa é feito `moreLikeThis` com, uma consulta é gerada com termos de pesquisa extraídos do documento dado que descrevem melhor esse documento. A consulta gerada é então usada para fazer o pedido de pesquisa. Por predefinição, o conteúdo de todos os campos pes pesjáveis é considerado, menos quaisquer campos restritos que especificou usando o `searchFields` parâmetro. O `moreLikeThis` parâmetro não pode ser utilizado com o parâmetro de pesquisa, `search=[string]` .

Por padrão, o conteúdo de todos os campos pescáveis de alto nível são considerados. Se quiser especificar determinados campos, pode utilizar o `searchFields` parâmetro. 

Não é possível utilizar `MoreLikeThis` em sub-campos pesjáveis num [tipo complexo.](search-howto-complex-data-types.md)

## <a name="examples"></a>Exemplos

Todos os exemplos seguintes usam a amostra de hotéis do [Quickstart: Crie um índice de pesquisa no portal Azure](search-get-started-portal.md).

### <a name="simple-query"></a>Consulta simples

A seguinte consulta encontra documentos cujos campos de descrição são mais semelhantes ao campo do documento de origem, tal como especificado no `moreLikeThis` parâmetro:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

Neste exemplo, o pedido procura hotéis semelhantes aos dos `HotelId` 29.
Em vez de utilizar HTTP GET, também pode invocar `MoreLikeThis` usando HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Aplicar filtros

`MoreLikeThis` pode ser combinado com outros parâmetros de consulta comuns como `$filter` . Por exemplo, a consulta pode ser restrita apenas a hotéis cuja categoria é "Orçamento" e onde a classificação seja superior a 3.5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Selecione campos e limite de resultados

O `$top` seletor pode ser utilizado para limitar quantos resultados devem ser devolvidos numa `MoreLikeThis` consulta. Além disso, os campos podem ser selecionados com `$select` . Aqui os três melhores hotéis são selecionados juntamente com o seu ID, Nome e Classificação: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Passos seguintes

Pode utilizar qualquer ferramenta de teste web para experimentar esta funcionalidade.  Recomendamos a utilização do Carteiro para este exercício.

> [!div class="nextstepaction"]
> [Explore Azure Cognitive Search REST APIs](search-get-started-rest.md)