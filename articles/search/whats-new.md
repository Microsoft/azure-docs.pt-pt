---
title: Novos anúncios de recurso
titleSuffix: Azure Cognitive Search
description: Anúncios de funcionalidades novas e melhoradas, incluindo um nome de serviço de Azure Search to Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/23/2020
ms.openlocfilehash: 086e3cf71012f168851df1c85b19689294fe6302
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85515722"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades na Pesquisa Cognitiva Azure

Saiba o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço.

## <a name="feature-announcements"></a>Anúncios de recursos

### <a name="june-2020"></a>Junho de 2020

A azure Machine Learning é um novo tipo de habilidade para integrar um ponto final inferencing da Azure Machine Learning. A experiência do portal suporta a descoberta e integração do seu ponto final de Aprendizagem de Máquinas Azure dentro de um skillset de Pesquisa Cognitiva. A descoberta requer que os seus serviços de Pesquisa Cognitiva e Azure ML sejam implantados na mesma subscrição. Começa com [este tutorial.](cognitive-search-tutorial-aml-custom-skill.md)

### <a name="may-2020-microsoft-build"></a>Maio de 2020 (Microsoft Build)

+ O recurso [de sessões de Debug](cognitive-search-debug-session.md) está agora em pré-visualização. As sessões de Debug fornecem uma interface baseada no portal para investigar e resolver problemas com um skillset. As correções criadas na sessão de depurar podem ser guardadas para as habilidades de produção. Começa com [este tutorial.](cognitive-search-tutorial-debug-sessions.md)

+ As melhorias de segurança incluem a capacidade de [configurar um ponto final de pesquisa privado (pré-visualização)](service-create-private-endpoint.md) que é inacessível na internet pública. Também pode [configurar as regras de IP para suporte a firewall (pré-visualização)](service-configure-firewall.md).

+ Utilize uma [identidade gerida pelo sistema (pré-visualização)](search-howto-managed-identities-data-sources.md) para configurar uma ligação a uma fonte de dados Azure para indexação. Aplica-se a [indexadores](search-indexer-overview.md) que ingerem conteúdo de fontes de dados Azure, tais como Azure SQL Database, Azure Cosmos DB e Azure Storage.

+ Altere a base para a forma como as pontuações de pesquisa são calculadas, de por fragmento para todos os fragmentos, utilizando os parâmetros de consulta [ScoringStatistics=global](index-similarity-and-scoring.md#scoring-statistics) e sessionId.

### <a name="march-2020"></a>Março de 2020

+ [A exclusão suave de blob nativo (pré-visualização)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) significa que o indexante de armazenamento Azure Blob na Azure Cognitive Search reconhecerá as bolhas que estão em estado de eliminação suave e removerá o documento de pesquisa correspondente durante a indexação.

+ A Nova API de Gestão Estável [(2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) já está disponível. 

### <a name="february-2020"></a>Fevereiro de 2020

+ [A Deteção de PII (pré-visualização)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis a partir de um texto de entrada e lhe dá a opção de mascarê-lo desse texto de várias maneiras.

+ [O Custom Entity Lookup (pré-visualização)](cognitive-search-skill-custom-entity-lookup.md ) procura texto a partir de uma lista personalizada, definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência difusa que pode ser aplicada para encontrar fósforos que são semelhantes, mas não exatamente exatos. 

### <a name="january-2020"></a>Janeiro de 2020

+ [As chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) estão agora geralmente disponíveis. Se estiver a utilizar o REST, pode aceder à funcionalidade utilizando `api-version=2019-05-06` . Para o código gerido, o pacote correto ainda é [a pré-visualização da versão 8.0 net SDK,](search-dotnet-sdk-migration-version-9.md) mesmo que a funcionalidade esteja fora de pré-visualização. 

+ O acesso privado a um serviço de pesquisa está disponível através de dois mecanismos, ambos atualmente em pré-visualização:

  + Pode restringir o acesso a endereços IP específicos utilizando a API Management REST `api-version=2019-10-01-Preview` para criar o serviço. A API de pré-visualização tem novas propriedades **IpRule** e **NetworkRuleSet** na [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Esta funcionalidade de pré-visualização está disponível em regiões selecionadas. Para mais informações, consulte [Como utilizar a API Management REST](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Atualmente disponível através de uma pré-visualização de acesso limitado, pode disponibilizar um serviço de Pesquisa Azure que suporte o Azure Private Endpoint para ligações de clientes na mesma rede virtual. Para obter mais informações, consulte [Criar um Ponto Final Privado para obter uma ligação segura](service-create-private-endpoint.md).

### <a name="december-2019"></a>Dezembro de 2019

+ [Criar app (pré-visualização)](search-create-app-portal.md) é um novo assistente no portal que gera um ficheiro HTML transferível. O ficheiro vem com um script incorporado que torna uma aplicação web de estilo "localhost", ligada a um índice no seu serviço de pesquisa. As páginas são configuráveis no assistente e podem conter uma barra de pesquisa, área de resultados, navegação na barra lateral e suporte de consulta de cabeça de tipo. Pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência.

+ [Criar um ponto final privado para ligações seguras (pré-visualização)](service-create-private-endpoint.md) explica como configurar um Link Privado para ligações seguras ao seu serviço de pesquisa. Esta funcionalidade de pré-visualização está disponível mediante solicitação e utiliza [a Azure Private Link](../private-link/private-link-overview.md) e a [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) como parte da solução.

### <a name="november-2019---ignite-conference"></a>Novembro 2019 - Conferência Ignite

+ [O enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona o caching e a fruição de estado a um gasoduto de enriquecimento para que possa trabalhar em etapas ou fases específicas sem perder conteúdo que já está processado. Anteriormente, qualquer alteração a um gasoduto de enriquecimento exigia uma reconstrução completa. Com o enriquecimento incremental, a produção de análise dispendiosa, especialmente a análise de imagem, é preservada.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [A extração de documentos (pré-visualização)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva utilizada durante a indexação que permite extrair o conteúdo de um ficheiro dentro de um skillset. Anteriormente, a quebra de documentos só ocorreu antes da execução do skillset. Com a adição desta habilidade, também pode executar esta operação dentro da execução de skillset.

+ [A tradução de texto](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva utilizada durante a indexação que avalia o texto e, para cada registo, devolve o texto traduzido para a linguagem-alvo especificada.

+ [Os modelos power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) podem iniciar as suas visualizações e análise de conteúdos enriquecidos numa loja de conhecimento no ambiente de trabalho Power BI. Este modelo foi concebido para projeções da tabela Azure criadas através do [assistente de dados de importação.](knowledge-store-create-portal.md)

+ [Azure Data Lake Storage Gen2 (pré-visualização)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (pré-visualização)](search-howto-index-cosmosdb.md)e [Cosmos DB Cassandra API (pré-visualização)](search-howto-index-cosmosdb.md) são agora suportados em indexadores. Pode inscrever-se usando [este formulário.](https://aka.ms/azure-cognitive-search/indexer-preview) Receberá um e-mail de confirmação assim que tiver sido aceite no programa de pré-visualização.

### <a name="july-2019"></a>Julho de 2019

+ Geralmente disponível na [Nuvem do Governo de Azure.](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Novo nome do serviço

A Azure Search é agora renomeado para **Azure Cognitive Search** para refletir o uso expandido (ainda opcional) de habilidades cognitivas e processamento de IA em operações centrais. As versões API, pacotes NuGet, espaços de nome e pontos finais são inalterados. As novas e existentes soluções de pesquisa não são afetadas pela alteração do nome de serviço.

## <a name="service-updates"></a>Atualizações de serviço

[Os anúncios de atualização de](https://azure.microsoft.com/updates/?product=search&status=all) serviço para Azure Cognitive Search podem ser encontrados no site do Azure.