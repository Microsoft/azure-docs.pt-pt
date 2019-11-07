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
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721727"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (versão prévia) no Azure Pesquisa Cognitiva

> [!IMPORTANT] 
> Este recurso está atualmente em visualização pública. A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. No momento, não há suporte para Portal ou SDK do .NET.

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