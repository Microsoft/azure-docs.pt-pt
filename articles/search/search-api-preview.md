---
title: API REST de visualização para Azure Search 2019-05-06-Preview-Azure Search
description: Azure Search API REST do serviço versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e chaves de criptografia gerenciadas pelo cliente.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649986"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>API REST do serviço de Azure Search-versão 2019-05-06-Preview
Este artigo descreve a `api-version=2019-05-06-Preview` versão da API REST do serviço Azure Search, oferecendo recursos experimentais que ainda não estão disponíveis para o público em geral.

> [!NOTE]
> Os recursos de visualização estão disponíveis para teste e experimentação com o objetivo de coletar comentários e estão sujeitos a alterações. Aconselhamos enfaticamente o uso de APIs de visualização em aplicativos de produção.


## <a name="new-in-2019-05-06-preview"></a>Novo no 2019-05-06-Preview

A [**loja de conhecimento**](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseado em ia. Além de um índice, agora você pode persistir estruturas de dados preenchidas criadas durante a indexação no armazenamento do Azure. Você controla as estruturas físicas de seus dados por meio de elementos em um configurador de habilidades, incluindo como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

[**As chaves de criptografia gerenciadas pelo cliente**](search-security-manage-encryption-keys.md) para criptografia do lado do serviço em repouso também são um novo recurso de visualização. Além da criptografia interna em repouso gerenciada pela Microsoft, você pode aplicar uma camada adicional de criptografia em que você é o único proprietário das chaves.

## <a name="other-preview-features"></a>Outros recursos de visualização

Os recursos anunciados em versões anteriores ainda estão em visualização pública. Se você estiver chamando uma API com uma versão prévia da API de visualização, poderá continuar a usar essa versão ou alternar para `2019-05-06-Preview` o sem alterações no comportamento esperado.

+ o [parâmetro de consulta moreLikeThis](search-more-like-this.md) localiza documentos que são relevantes para um documento específico. Este recurso está em versões anteriores. 
* A indexação de [BLOBs CSV](search-howto-index-csv-blobs.md) cria um documento por linha, em oposição a um documento por blob de texto.
* [Suporte à API do MongoDB para Cosmos DB indexadores](search-howto-index-cosmosdb.md) está em versão prévia.


## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de visualização

As visualizações mais antigas ainda são operacionais, mas tornam-se obsoletas ao longo do tempo. Se seu código chamar `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, essas chamadas ainda serão válidas. No entanto, somente a versão de visualização mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão da API de visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Search serviço está disponível em várias versões. Para obter mais informações, consulte [versões de API](search-api-versions.md).

## <a name="next-steps"></a>Passos Seguintes

Examine a documentação de referência da API REST do serviço de Azure Search. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)