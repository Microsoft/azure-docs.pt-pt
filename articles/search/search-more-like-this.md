---
title: maisLikeThis (pré-visualização) funcionalidade de consulta
titleSuffix: Azure Cognitive Search
description: Descreve a funcionalidade mais LikeThis (pré-visualização), que está disponível nas versões de pré-visualização da API de pesquisa cognitiva Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873816"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (pré-visualização) em Pesquisa Cognitiva Azure

> [!IMPORTANT] 
> Esta funcionalidade encontra-se atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece esta funcionalidade. Atualmente não existe nenhum suporte de Portal ou .NET SDK.

`moreLikeThis=[key]`é um parâmetro de consulta na API de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) de Pesquisa que encontra documentos semelhantes ao documento especificado pela chave do documento. Quando um pedido de `moreLikeThis`pesquisa é feito com , uma consulta é gerada com termos de pesquisa extraídos do documento que descrevem melhor esse documento. A consulta gerada é então usada para fazer o pedido de pesquisa. Por predefinição, considera-se o conteúdo de todos os campos pesquisáveis, menos quaisquer campos restritos que especificou utilizando o `searchFields` parâmetro. O `moreLikeThis` parâmetro não pode ser utilizado `search=[string]`com o parâmetro de pesquisa, .

Por padrão, o conteúdo de todos os campos pesquisáveis de alto nível é considerado. Se quiser especificar determinados campos, pode `searchFields` utilizar o parâmetro. 

Não é `MoreLikeThis` possível utilizar em sub-campos pesquisáveis num [tipo complexo](search-howto-complex-data-types.md).

## <a name="examples"></a>Exemplos

Todos os exemplos seguintes utilizam a amostra de hotéis da [Quickstart: Crie um índice](search-get-started-portal.md)de pesquisa no portal Azure .

### <a name="simple-query"></a>Consulta simples

A seguinte consulta encontra documentos cujos campos de descrição são mais `moreLikeThis` semelhantes ao campo do documento de origem especificado pelo parâmetro:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

Neste exemplo, o pedido procura hotéis `HotelId` semelhantes aos de 29.
Em vez de utilizar http `MoreLikeThis` GET, também pode invocar usando http post:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Aplicar filtros

`MoreLikeThis`pode ser combinado com outros parâmetros comuns de consulta como `$filter`. Por exemplo, a consulta só pode ser restringida a hotéis cuja categoria é "Orçamento" e onde a classificação seja superior a 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Selecione campos e limite resultados

O `$top` seletor pode ser usado para limitar quantos resultados devem ser devolvidos numa `MoreLikeThis` consulta. Além disso, os `$select`campos podem ser selecionados com . Aqui os três melhores hotéis são selecionados juntamente com o seu ID, Nome e Classificação: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Passos seguintes

Pode utilizar qualquer ferramenta de teste web para experimentar esta funcionalidade.  Recomendamos a utilização do Carteiro para este exercício.

> [!div class="nextstepaction"]
> [Explore a Pesquisa Cognitiva Azure REST APIs usando o carteiro](search-get-started-postman.md)
