---
title: Novos comunicados de recursos
titleSuffix: Azure Cognitive Search
description: Anúncios de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para o Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 247378461a99d42f21a67a2c0aebc7019727f3a0
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555147"
---
# <a name="whats-new-in-azure-cognitive-search"></a>O que há de novo no Azure Pesquisa Cognitiva

Saiba o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Novo nome do serviço para Azure Search

Azure Search agora é renomeado para o **pesquisa cognitiva do Azure** para refletir o uso expandido de habilidades cognitivas e processamento de ia em operações principais. Embora as habilidades cognitivas adicionem novos recursos, usar o ia é estritamente opcional. Você pode continuar a usar o Azure Pesquisa Cognitiva sem ia para criar soluções avançadas de pesquisa de texto completo por meio de conteúdo privado, heterogêneo e baseado em texto em um índice que você cria e gerencia na nuvem. 

As versões de API, pacotes NuGet, namespaces e pontos de extremidade não são alterados. As soluções de pesquisa existentes não são afetadas pela alteração do nome do serviço.

## <a name="feature-announcements"></a>Anúncios de recursos

4 de novembro de 2019-conferência Ignite

+ A [indexação incremental (versão prévia)](cognitive-search-incremental-indexing-conceptual.md) permite que você escolha e escolha quais etapas reprocessar ao fazer modificações em um pipeline de enriquecimento. A indexação incremental será útil se você tiver conteúdo de imagem analisado anteriormente. A saída da análise dispendiosa é armazenada e, em seguida, usada como base para indexação ou enriquecimento adicional.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [extração de documentos (visualização)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva usada durante a indexação que permite extrair o conteúdo de um arquivo de dentro de um conferente. Anteriormente, a quebra de documento ocorreu apenas antes da execução do concodificador. Com a adição dessa habilidade, você também pode executar essa operação dentro da execução do conconjunto de habilidades.

+ A [conversão de texto (visualização)](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva usada durante a indexação que avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado.

+ Os [modelos de Power bi](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) podem iniciar suas visualizações e a análise de conteúdo aprimorado em uma loja de conhecimento em Power bi área de trabalho. Este modelo foi projetado para projeções de tabela do Azure criadas por meio do [Assistente de importação de dados](knowledge-store-create-portal.md).

+ [Azure data Lake Storage Gen2 (visualização](search-howto-index-azure-data-lake-storage.md)), [Cosmos DB API Gremlin (visualização)](search-howto-index-cosmosdb.md)e [Cosmos DB API do Cassandra (versão prévia)](search-howto-index-cosmosdb.md) agora têm suporte em indexadores. Você pode se inscrever usando [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Você receberá um email de confirmação quando tiver sido aceito no programa de visualização.

Julho de 24 2019

+ Geralmente disponível na [nuvem do Azure governamental](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Atualizações de serviço

Os [comunicados de atualização de serviço](https://azure.microsoft.com/updates/?product=search&status=all) do Azure pesquisa cognitiva podem ser encontrados no site do Azure.