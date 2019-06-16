---
title: moreLikeThis no Azure Search (pré-visualização) - Azure Search
description: Documentação preliminar para a funcionalidade de moreLikeThis (pré-visualização), exposta na API de REST da Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 5723f1ab7258a9e0d672b5c0fd9fd0b9c4dc8721
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522930"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis no Azure Search

> [!Note]
> moreLikeThis está em pré-visualização e não se utilização em produção. O [2019 no versão REST API-05-06-Preview](search-api-preview.md) fornece esta funcionalidade. Não existe nenhum suporte de .NET SDK neste momento.

`moreLikeThis=[key]` é um parâmetro de consulta no [API de pesquisa de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) que localiza documentos semelhante para o documento especificado pela chave do documento. Quando é efetuado um pedido de pesquisa com `moreLikeThis`, é gerada uma consulta com os termos de pesquisa extraídos do documento fornecido, que descrevem melhor esse documento. A consulta gerada, em seguida, é utilizada para fazer a solicitação de pesquisa. Por predefinição, são considerados os conteúdos de todos os campos pesquisáveis, menos quaisquer campos restritos que especificou utilizando o `searchFields` parâmetro. O `moreLikeThis` parâmetro não pode ser utilizado com o parâmetro de pesquisa, `search=[string]`.

Por predefinição, são considerados os conteúdos de todos os campos pesquisáveis de nível superior. Se pretender especificar campos específicos em vez disso, pode utilizar o `searchFields` parâmetro. 

Não é possível utilizar moreLikeThis em campos secundárias pesquisáveis numa [tipo complexo](search-howto-complex-data-types.md).

## <a name="examples"></a>Exemplos 

Segue-se um exemplo de uma consulta de moreLikeThis. A consulta localizar documentos cujos campos de descrição são mais semelhantes para o campo do documento de origem, tal como especificado pelo `moreLikeThis` parâmetro.

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

Pode utilizar qualquer ferramenta de teste da web para experimentar esta funcionalidade.  Recomendamos que utilize o Postman para este exercício.

> [!div class="nextstepaction"]
> [Explorar as APIs REST de pesquisa do Azure com o Postman](search-fiddler.md)