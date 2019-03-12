---
title: moreLikeThis no Azure Search (pré-visualização) - Azure Search
description: Documentação preliminar para a funcionalidade de moreLikeThis (pré-visualização), exposta na API de REST da Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569047"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis no Azure Search (pré-visualização)

`moreLikeThis=[key]` é um parâmetro de consulta no [a API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents). Ao especificar o `moreLikeThis` parâmetro numa consulta de pesquisa, pode encontrar os documentos que são semelhantes ao documento especificado pela chave do documento. Quando é efetuado um pedido de pesquisa com `moreLikeThis`, é gerada uma consulta com os termos de pesquisa extraídos do documento fornecido, que descrevem melhor esse documento. A consulta gerada, em seguida, é utilizada para fazer a solicitação de pesquisa. Por predefinição, o conteúdo de todos os `searchable` campos são considerados, a menos que o `searchFields` parâmetro é utilizado para restringir os campos. O `moreLikeThis` parâmetro não pode ser utilizado com o parâmetro de pesquisa, `search=[string]`.

## <a name="examples"></a>Exemplos 

Segue-se um exemplo de uma consulta de moreLikeThis. A consulta localizar documentos cujos campos de descrição são mais semelhantes para o campo do documento de origem, tal como especificado pelo `moreLikeThis` parâmetro.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>Disponibilidade de funcionalidades

A funcionalidade de moreLikeThis está atualmente em pré-visualização e só é suportado nas versões de pré-visualização api-, `2015-02-28-Preview` e `2016-09-01-Preview`. Uma vez que a versão de API é especificada no pedido, é possível combinar em disponibilidade geral (GA) e APIs de pré-visualização na mesma aplicação. No entanto, as APIs não estão sob o SLA e funcionalidades de pré-visualização poderá mudar, assim, recomendamos que não usá-los em aplicações de produção.
