---
title: moreLikeThis em Azure Search (versão prévia) – Azure Search
description: Documentação preliminar do recurso moreLikeThis (versão prévia), exposto na API REST do Azure Search.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182322"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis em Azure Search

> [!Note]
> moreLikeThis está em visualização e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.

`moreLikeThis=[key]`é um parâmetro de consulta na [API de documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) que localiza documentos semelhantes ao documento especificado pela chave do documento. Quando uma solicitação de pesquisa é feita `moreLikeThis`com, uma consulta é gerada com os termos de pesquisa extraídos do documento fornecido que descrevem melhor o documento. Em seguida, a consulta gerada é usada para fazer a solicitação de pesquisa. Por padrão, o conteúdo de todos os campos pesquisáveis é considerado, menos os campos restritos que você especificou usando o `searchFields` parâmetro. O `moreLikeThis` parâmetro não pode ser usado com o parâmetro de `search=[string]`pesquisa,.

Por padrão, o conteúdo de todos os campos pesquisáveis de nível superior é considerado. Se você quiser especificar campos específicos em vez disso, poderá usar o `searchFields` parâmetro. 

Não é possível usar moreLikeThis em subcampos pesquisáveis em um [tipo complexo](search-howto-complex-data-types.md).

## <a name="examples"></a>Exemplos 

Veja abaixo um exemplo de uma consulta moreLikeThis. A consulta localiza documentos cujos campos de descrição são mais semelhantes ao campo do documento de origem, conforme especificado pelo `moreLikeThis` parâmetro.

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


## <a name="next-steps"></a>Passos Seguintes

Você pode usar qualquer ferramenta de teste na Web para experimentar esse recurso.  É recomendável usar o postmaster para este exercício.

> [!div class="nextstepaction"]
> [Explorar Azure Search APIs REST usando o postmaster](search-get-started-postman.md)