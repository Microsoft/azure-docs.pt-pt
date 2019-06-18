---
title: REST API de pré-visualização para pré-visualização do Azure Search 2019-05-06-- o Azure Search
description: Azure Search Service REST 2019 de versão de API-05-06-Preview inclui funcionalidades experimentais, como o arquivo de dados de conhecimento e as chaves de encriptação gerida pelo cliente.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5374ff896613dd8f8563a2054be8a92103e63fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523907"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Versão de api REST do serviço de pesquisa do Azure de 2019-05-06-pré-visualização
Este artigo descreve o `api-version=2019-05-06-Preview` versão do serviço de Azure Search REST API, oferecendo funcionalidades experimentais não ainda em disponibilidade geral.

> [!NOTE]
> Funcionalidades de pré-visualização estão disponíveis para teste e experimentação com o objetivo de recolha de comentários e estão sujeitos a alterações. É altamente recomendável contra a utilização de APIs de pré-visualização em aplicações de produção.


## <a name="new-in-2019-05-06-preview"></a>Novo no 2019-05-06-pré-visualização

[**Arquivo de dados de conhecimento** ](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseada em IA. Além de um índice, agora pode manter estruturas de dados preenchida criadas durante a indexação no armazenamento do Azure. Controlar as estruturas físicas dos seus dados por meio de elementos num conjunto de capacidades, incluindo como dados são formatados, se os dados são armazenados no armazenamento de BLOBs ou de armazenamento de tabelas, e se existem várias exibições.

[**Chaves de encriptação gerida pelo cliente** ](search-security-manage-encryption-keys.md) para o lado do serviço de encriptação em repouso também é uma nova funcionalidade de pré-visualização. Para além do incorporada encriptação em repouso gerenciado pela Microsoft, pode aplicar uma camada adicional de onde está a única proprietária das chaves de encriptação.

## <a name="other-preview-features"></a>Outras funcionalidades de pré-visualização

Funcionalidades anunciadas em pré-visualizações anteriores ainda estão em pré-visualização pública. Se estiver chamando uma API com uma api-version anterior pré-visualização, pode continuar a utilizar essa versão ou mudar para `2019-05-06-Preview` sem alterações ao comportamento esperado.

+ [parâmetro de consulta de moreLikeThis](search-more-like-this.md) documentos que são relevantes para um documento específico. Esta funcionalidade está pré-visualizações anteriores. 
* [A indexação de BLOBs CSV](search-howto-index-csv-blobs.md) cria um documento por linha, em vez de um documento por blob de texto.
* [Suporte de API do MongoDB para o Cosmos DB indexadores](search-howto-index-cosmosdb.md) está em pré-visualização.


## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de pré-visualização

Pré-visualizações mais antigas são ainda está operacionais, mas se tornem obsoletas ao longo do tempo. Se o seu código chamará `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, essas chamadas ainda são válidas. No entanto, apenas a versão de pré-visualização mais recente é atualizada com melhorias. 

A sintaxe de exemplo seguinte ilustra uma chamada para a versão de API de pré-visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Serviço do Azure Search está disponível em várias versões. Para obter mais informações, consulte [versões de API](search-api-versions.md).

## <a name="next-steps"></a>Passos Seguintes

Reveja a documentação de referência da API de REST do serviço de pesquisa do Azure. Se tiver problemas, peça-nos para obter ajuda no [Stack Overflow](https://stackoverflow.com/) ou [contacte o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)