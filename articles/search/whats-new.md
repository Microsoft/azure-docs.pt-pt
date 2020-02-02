---
title: Novos comunicados de recursos
titleSuffix: Azure Cognitive Search
description: Anúncios de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para o Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: d0e0e8a5aa3a3e43997e3f9512525be9f51d2018
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934873"
---
# <a name="whats-new-in-azure-cognitive-search"></a>O que há de novo no Azure Pesquisa Cognitiva

Saiba o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Novo nome de serviço

A Azure Search é agora renomeada para **Azure Cognitive Search** para refletir o uso expandido (mas opcional) de habilidades cognitivas e processamento de IA em operações nucleares. As versões API, pacotes NuGet, espaços de nome e pontos finais mantêm-se inalteradas. As novas e existentes soluções de pesquisa não são afetadas pela mudança de nome do serviço.

## <a name="feature-announcements"></a>Anúncios de recursos

### <a name="february-2020"></a>fevereiro de 2020

+ [PiI Deteção (pré-visualização)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de a mascarar desse texto de várias formas.

+ [O Lookup de EntidadePersonalizada (pré-visualização)](cognitive-search-skill-custom-entity-lookup.md ) procura texto a partir de uma lista personalizada e definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência fuzzy que pode ser aplicado para encontrar fósforos que são semelhantes, mas não exatamente. 

### <a name="january-2020"></a>Janeiro de 2020

+ [As chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) agora estão disponíveis para o público geral. Se você estiver usando o REST, poderá acessar o recurso usando `api-version=2019-05-06`. Para código gerenciado, o pacote correto ainda é o [SDK do .NET versão 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , embora o recurso esteja fora de visualização. 

+ O acesso privado a um serviço de pesquisa está disponível através de dois mecanismos, ambos atualmente em pré-visualização:

  + Você pode restringir o acesso a endereços IP específicos usando a API REST de gerenciamento `api-version=2019-10-01-Preview` para criar o serviço. A API de visualização tem novas propriedades **IpRule** e **NetworkRuleSet** na [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate). Esse recurso de visualização está disponível em regiões selecionadas. Para obter mais informações, consulte [como usar a API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Atualmente disponível por meio de uma visualização de acesso limitado, você pode provisionar um serviço de Azure Search que dá suporte ao ponto de extremidade privado do Azure para conexões de clientes na mesma rede virtual. Para obter mais informações, consulte [criar um ponto de extremidade privado para uma conexão segura](service-create-private-endpoint.md).

### <a name="december-2019"></a>Dezembro de 2019

+ [Criar aplicativo (versão prévia)](search-create-app-portal.md) é um novo assistente no portal que gera um arquivo HTML para download. O arquivo vem com um script incorporado que renderiza um aplicativo Web de estilo "localhost" operacional, associado a um índice em seu serviço de pesquisa. As páginas são configuráveis no assistente e podem conter uma barra de pesquisa, área de resultados, navegação de barra lateral e suporte a consultas typeahead. Você pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência.

+ [Criar um ponto de extremidade privado para conexões seguras (visualização)](service-create-private-endpoint.md) explica como configurar um link privado para conexões seguras para o serviço de pesquisa. Esse recurso de visualização está disponível mediante solicitação e usa o [link privado do Azure](../private-link/private-link-overview.md) e a [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) como parte da solução.

### <a name="november-2019---ignite-conference"></a>2019 de novembro-conferência Ignite

+ O [enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona Caching e statefullness a um pipeline de enriquecimento para que você possa trabalhar em etapas ou fases específicas sem perder o conteúdo que já foi processado. Anteriormente, qualquer alteração em um pipeline de enriquecimento exigia uma recompilação completa. Com o enriquecimento incremental, a saída da análise dispendiosa, especialmente a análise de imagens, é preservada.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [extração de documentos (visualização)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva usada durante a indexação que permite extrair o conteúdo de um arquivo de dentro de um conferente. Anteriormente, a quebra de documento ocorreu apenas antes da execução do concodificador. Com a adição dessa habilidade, você também pode executar essa operação dentro da execução do conconjunto de habilidades.

+ [A Tradução de Texto](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva utilizada durante a indexação que avalia o texto e, para cada registo, devolve o texto traduzido para a linguagem-alvo especificada.

+ Os [modelos de Power bi](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) podem iniciar suas visualizações e a análise de conteúdo aprimorado em uma loja de conhecimento em Power bi área de trabalho. Este modelo foi projetado para projeções de tabela do Azure criadas por meio do [Assistente de importação de dados](knowledge-store-create-portal.md).

+ [Azure data Lake Storage Gen2 (visualização](search-howto-index-azure-data-lake-storage.md)), [Cosmos DB API Gremlin (visualização)](search-howto-index-cosmosdb.md)e [Cosmos DB API do Cassandra (versão prévia)](search-howto-index-cosmosdb.md) agora têm suporte em indexadores. Você pode se inscrever usando [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Você receberá um email de confirmação quando tiver sido aceito no programa de visualização.

### <a name="july-2019"></a>Julho de 2019

+ Geralmente disponível na [nuvem do Azure governamental](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Atualizações de serviço

Os [comunicados de atualização de serviço](https://azure.microsoft.com/updates/?product=search&status=all) do Azure pesquisa cognitiva podem ser encontrados no site do Azure.