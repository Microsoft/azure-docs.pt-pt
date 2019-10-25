---
title: API REST versão 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: API REST do serviço de Pesquisa Cognitiva do Azure versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e chaves de criptografia gerenciadas pelo cliente.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a4daaac6abababcedb5d6dd7eb2122ef29846ef4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792542"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>API REST do serviço de Pesquisa Cognitiva do Azure – versão 2019-05-06-visualização

Este artigo descreve a versão `api-version=2019-05-06-Preview` da API REST do serviço de pesquisa, oferecendo recursos experimentais que ainda não estão disponíveis para o público em geral.

> [!NOTE]
> Os recursos de visualização estão disponíveis para teste e experimentação com o objetivo de coletar comentários e estão sujeitos a alterações. Aconselhamos enfaticamente o uso de APIs de visualização em aplicativos de produção.


## <a name="new-in-2019-05-06-preview"></a>Novo no 2019-05-06-Preview

A [**loja de conhecimento**](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseado em ia. Além de um índice, agora você pode persistir estruturas de dados preenchidas criadas durante a indexação no armazenamento do Azure. Você controla as estruturas físicas de seus dados por meio de elementos em um configurador de habilidades, incluindo como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

[**As chaves de criptografia gerenciadas pelo cliente**](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso também são um novo recurso de visualização. Além da criptografia interna em repouso gerenciada pela Microsoft, você pode aplicar uma camada adicional de criptografia em que você é o único proprietário das chaves.

## <a name="other-preview-features"></a>Outros recursos de visualização

Os recursos anunciados em versões anteriores ainda estão em visualização pública. Se você estiver chamando uma API com uma versão de API de visualização anterior, poderá continuar a usar essa versão ou alternar para `2019-05-06-Preview` sem alterações no comportamento esperado.

+ o [parâmetro de consulta moreLikeThis](search-more-like-this.md) localiza documentos que são relevantes para um documento específico. Este recurso está em versões anteriores. 
* A [indexação de BLOBs CSV](search-howto-index-csv-blobs.md) cria um documento por linha, em oposição a um documento por blob de texto.
* [Suporte à API do MongoDB para Cosmos DB indexadores](search-howto-index-cosmosdb.md) está em versão prévia.


## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de visualização

As visualizações mais antigas ainda são operacionais, mas tornam-se obsoletas ao longo do tempo. Se seu código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, essas chamadas ainda são válidas. No entanto, somente a versão de visualização mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão da API de visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de Pesquisa Cognitiva do Azure está disponível em várias versões. Para obter mais informações, consulte [versões de API](search-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)