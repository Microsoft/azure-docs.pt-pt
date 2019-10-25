---
title: recurso de consulta moreLikeThis (visualização)
titleSuffix: Azure Cognitive Search
description: Descreve o recurso moreLikeThis (versão prévia), que está disponível em versões prévias da API REST do Azure Pesquisa Cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793479"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (versão prévia) no Azure Pesquisa Cognitiva

> [!Note]
> moreLikeThis está em visualização e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.

`moreLikeThis=[key]` é um parâmetro de consulta na [API de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) que localiza documentos semelhantes ao documento especificado pela chave do documento. Quando uma solicitação de pesquisa é feita com `moreLikeThis`, uma consulta é gerada com os termos de pesquisa extraídos do documento fornecido que descrevem melhor o documento. Em seguida, a consulta gerada é usada para fazer a solicitação de pesquisa. Por padrão, o conteúdo de todos os campos pesquisáveis é considerado, menos os campos restritos que você especificou usando o parâmetro `searchFields`. O parâmetro `moreLikeThis` não pode ser usado com o parâmetro de pesquisa `search=[string]`.

Por padrão, o conteúdo de todos os campos pesquisáveis de nível superior é considerado. Se você quiser especificar campos específicos em vez disso, poderá usar o parâmetro `searchFields`. 

Não é possível usar moreLikeThis em subcampos pesquisáveis em um [tipo complexo](search-howto-complex-data-types.md).

## <a name="examples"></a>Exemplos 

Veja abaixo um exemplo de uma consulta moreLikeThis. A consulta localiza documentos cujos campos de descrição são mais semelhantes ao campo do documento de origem, conforme especificado pelo parâmetro `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Passos seguintes

Você pode usar qualquer ferramenta de teste na Web para experimentar esse recurso.  É recomendável usar o postmaster para este exercício.

> [!div class="nextstepaction"]
> [Explorar as APIs REST do Azure Pesquisa Cognitiva usando o postmaster](search-get-started-postman.md)