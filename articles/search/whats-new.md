---
title: Novos comunicados de recursos
titleSuffix: Azure Cognitive Search
description: Anúncios de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para o Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0ce2884a2382c7dff2bdb90bd92934609675f314
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834377"
---
# <a name="whats-new-in-azure-cognitive-search"></a>O que há de novo no Azure Pesquisa Cognitiva

Saiba o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Novo nome do serviço para Azure Search

Azure Search agora é renomeado para o **pesquisa cognitiva do Azure** para refletir o uso expandido de habilidades cognitivas e processamento de ia em operações principais. Embora as habilidades cognitivas adicionem novos recursos, usar o ia é estritamente opcional. Você pode continuar a usar o Azure Pesquisa Cognitiva sem ia para criar soluções avançadas de pesquisa de texto completo por meio de conteúdo privado, heterogêneo e baseado em texto em um índice que você cria e gerencia na nuvem. 

As versões de API, pacotes NuGet, namespaces e pontos de extremidade não são alterados. As soluções de pesquisa existentes não são afetadas pela alteração do nome do serviço.

## <a name="feature-announcements"></a>Anúncios de recursos

### <a name="january-2020"></a>Janeiro de 2020

+ [As chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) agora estão disponíveis para o público geral. Se você estiver usando o REST, poderá acessar o recurso usando `api-version=2019-05-06`. Para código gerenciado, o pacote correto ainda é o [SDK do .NET versão 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , embora o recurso esteja fora de visualização. 

+ O *acesso IP restrito e o ponto de extremidade privado (visualização)* em um ponto de extremidade de serviço de pesquisa agora estão disponíveis em **API-Version = 2019-10-01-Preview**. Você pode configurar um ponto de extremidade seguro usando as novas propriedades **IpRule** e **NETWORKRULESET** na API REST de gerenciamento de [criação ou atualização](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) . Para obter mais informações sobre versões de API e disponibilidade regional, consulte [como usar a API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>Dezembro de 2019

+ [Criar aplicativo (versão prévia)](search-create-app-portal.md) é um novo assistente no portal que gera um arquivo HTML para download. O arquivo vem com um script incorporado que renderiza um aplicativo Web de estilo "localhost" operacional, associado a um índice em seu serviço de pesquisa. As páginas são configuráveis no assistente e podem conter uma barra de pesquisa, área de resultados, navegação de barra lateral e suporte a consultas typeahead. Você pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência.

### <a name="november-2019---ignite-conference"></a>2019 de novembro-conferência Ignite

+ O [enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona Caching e statefullness a um pipeline de enriquecimento para que você possa trabalhar em etapas ou fases específicas sem perder o conteúdo que já foi processado. Anteriormente, qualquer alteração em um pipeline de enriquecimento exigia uma recompilação completa. Com o enriquecimento incremental, a saída da análise dispendiosa, especialmente a análise de imagens, é preservada.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [extração de documentos (visualização)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva usada durante a indexação que permite extrair o conteúdo de um arquivo de dentro de um conferente. Anteriormente, a quebra de documento ocorreu apenas antes da execução do concodificador. Com a adição dessa habilidade, você também pode executar essa operação dentro da execução do conconjunto de habilidades.

+ A [conversão de texto (visualização)](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva usada durante a indexação que avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado.

+ Os [modelos de Power bi](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) podem iniciar suas visualizações e a análise de conteúdo aprimorado em uma loja de conhecimento em Power bi área de trabalho. Este modelo foi projetado para projeções de tabela do Azure criadas por meio do [Assistente de importação de dados](knowledge-store-create-portal.md).

+ [Azure data Lake Storage Gen2 (visualização](search-howto-index-azure-data-lake-storage.md)), [Cosmos DB API Gremlin (visualização)](search-howto-index-cosmosdb.md)e [Cosmos DB API do Cassandra (versão prévia)](search-howto-index-cosmosdb.md) agora têm suporte em indexadores. Você pode se inscrever usando [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Você receberá um email de confirmação quando tiver sido aceito no programa de visualização.

### <a name="july-2019"></a>Julho de 2019

+ Geralmente disponível na [nuvem do Azure governamental](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Atualizações de serviço

Os [comunicados de atualização de serviço](https://azure.microsoft.com/updates/?product=search&status=all) do Azure pesquisa cognitiva podem ser encontrados no site do Azure.