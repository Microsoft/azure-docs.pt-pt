---
title: API REST versão 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: API REST do serviço de Pesquisa Cognitiva do Azure versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e cache do indexador para aprimoramentos incrementais.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: 940ada83aeabf4bf8746ad5f90592e0917f7b403
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844450"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>API REST do serviço de Pesquisa Cognitiva do Azure – versão 2019-05-06-visualização

Este artigo descreve a versão `api-version=2019-05-06-Preview` da API REST do serviço de pesquisa, oferecendo recursos experimentais que ainda não estão disponíveis para o público em geral.

> [!NOTE]
> Os recursos de visualização estão disponíveis para teste e experimentação com o objetivo de coletar comentários e estão sujeitos a alterações. Aconselhamos enfaticamente o uso de APIs de visualização em aplicativos de produção.


## <a name="new-in-2019-05-06-preview"></a>Novo no 2019-05-06-Preview

+ O [enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona o cache a um pipeline de enriquecimento, permitindo que você reutilize a saída existente se uma modificação direcionada, como uma atualização de um conjunto de qualificações ou outro objeto, não alterar o conteúdo. O Caching aplica-se somente a documentos aprimorados produzidos por um configurador de qualificações.

+ [Cosmos DB indexador](search-howto-index-cosmosdb.md) dá suporte à API do MongoDB (versão prévia), API Gremlin (versão prévia) e API do Cassandra (versão prévia).

+ [Azure data Lake Storage Gen2 indexador (versão prévia)](search-howto-index-azure-data-lake-storage.md) pode indexar conteúdo e metadados de data Lake Storage Gen2.

+ A [loja de conhecimento](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseado em ia. A estrutura de dados física existe no armazenamento de BLOBs do Azure e no armazenamento de tabelas do Azure, e é criada e populada quando você executa um indexador que tem um associador cognitiva. A definição de uma loja de conhecimento é especificada em uma definição de contratação de habilidades. Na definição da loja de conhecimento, você controla as estruturas físicas de seus dados por meio de elementos de *projeção* que determinam como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

## <a name="earlier-preview-features"></a>Recursos anteriores de visualização

Os recursos anunciados em versões anteriores ainda estão em visualização pública. Se você estiver chamando uma API com uma versão de API de visualização anterior, poderá continuar a usar essa versão ou alternar para `2019-05-06-Preview` sem alterações no comportamento esperado.

+ o [parâmetro de consulta moreLikeThis](search-more-like-this.md) localiza documentos que são relevantes para um documento específico. Este recurso está em versões anteriores. 

+ A [indexação de BLOBs CSV](search-howto-index-csv-blobs.md) cria um documento por linha, em oposição a um documento por blob de texto.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de visualização

As visualizações mais antigas ainda são operacionais, mas tornam-se obsoletas ao longo do tempo. Se seu código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, essas chamadas ainda são válidas. No entanto, somente a versão de visualização mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão da API de visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de Pesquisa Cognitiva do Azure está disponível em várias versões. Para obter mais informações, consulte [versões de API](search-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)