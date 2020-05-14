---
title: Novos anúncios de funcionalidades
titleSuffix: Azure Cognitive Search
description: Anúncios de novas e melhoradas funcionalidades, incluindo um renome de serviço de Azure Search to Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: 0aea6a42269712e5c1e505c270f9d88635de83e5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195291"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades na Pesquisa Cognitiva Azure

Saiba o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Novo nome do serviço

A Azure Search é agora renomeada para **Azure Cognitive Search** para refletir o uso expandido (mas opcional) de habilidades cognitivas e processamento de IA em operações nucleares. As versões API, pacotes NuGet, espaços de nome e pontos finais mantêm-se inalteradas. As novas e existentes soluções de pesquisa não são afetadas pela mudança de nome do serviço.

## <a name="feature-announcements"></a>Anúncios de funcionalidades

### <a name="may-2020"></a>Maio de 2020

+ A funcionalidade de sessões de depuração está agora em pré-visualização. [Inscreva-se para solicitar acesso](https://aka.ms/DebugSessions).

### <a name="march-2020"></a>Março de 2020

+ [A eliminação suave de bolhas nativas (pré-visualização)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) significa que o indexante de armazenamento de Blob Azure em Pesquisa Cognitiva Azure reconhecerá bolhas que se encontram em estado suave mente eliminada e removerá o documento de pesquisa correspondente durante a indexação.

+ A Nova [Estável Management REST API (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) já está disponível. 

### <a name="february-2020"></a>Fevereiro de 2020

+ [PiI Deteção (pré-visualização)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de a mascarar desse texto de várias formas.

+ [O Lookup de EntidadePersonalizada (pré-visualização)](cognitive-search-skill-custom-entity-lookup.md ) procura texto a partir de uma lista personalizada e definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência fuzzy que pode ser aplicado para encontrar fósforos que são semelhantes, mas não exatamente. 

### <a name="january-2020"></a>Janeiro de 2020

+ As [chaves de encriptação geridas pelo cliente](search-security-manage-encryption-keys.md) estão agora geralmente disponíveis. Se estiver a utilizar o REST, pode aceder à funcionalidade utilizando `api-version=2019-05-06` . Para o código gerido, o pacote correto ainda é [a versão 8.0 do SDK .NET,](search-dotnet-sdk-migration-version-9.md) mesmo que a funcionalidade esteja fora de pré-visualização. 

+ O acesso privado a um serviço de pesquisa está disponível através de dois mecanismos, ambos atualmente em pré-visualização:

  + Pode restringir o acesso a endereços IP específicos utilizando a API MANAGEMENT REST `api-version=2019-10-01-Preview` para criar o serviço. A API de pré-visualização tem novas propriedades **IpRule** e **NetworkRuleSet** no [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Esta função de pré-visualização está disponível em regiões selecionadas. Para mais informações, consulte [Como utilizar a API DE GESTÃO REST](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Atualmente disponível através de uma pré-visualização de acesso limitado, pode fornecer um serviço de Pesquisa Azure que suporta o Azure Private Endpoint para ligações de clientes na mesma rede virtual. Para mais informações, consulte [Criar um Ponto Final Privado para uma ligação segura](service-create-private-endpoint.md).

### <a name="december-2019"></a>Dezembro de 2019

+ [Criar app (pré-visualização)](search-create-app-portal.md) é um novo assistente no portal que gera um ficheiro HTML descarregado. O ficheiro vem com script incorporado que torna uma aplicação web de estilo "localhost" operacional, ligada a um índice no seu serviço de pesquisa. As páginas são configuráveis no assistente e podem conter uma barra de pesquisa, área de resultados, navegação de barra lateral e suporte de consulta de tipo. Pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência.

+ [Criar um ponto final privado para ligações seguras (pré-visualização)](service-create-private-endpoint.md) explica como configurar um Link Privado para ligações seguras ao seu serviço de pesquisa. Esta funcionalidade de pré-visualização está disponível mediante pedido e utiliza o [Azure Private Link](../private-link/private-link-overview.md) e a [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) como parte da solução.

### <a name="november-2019---ignite-conference"></a>Novembro 2019 - Conferência Ignite

+ [O enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona cachee e statefullness a um oleoduto de enriquecimento para que possa trabalhar em passos ou fases específicos sem perder conteúdo que já é processado. Anteriormente, qualquer alteração a um gasoduto de enriquecimento exigia uma reconstrução completa. Com o enriquecimento incremental, a produção de análise suculegância, especialmente a análise de imagem, é preservada.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [A extração de documentos (pré-visualização)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva utilizada durante a indexação que permite extrair o conteúdo de um ficheiro dentro de um conjunto de habilidades. Anteriormente, a rachadura de documentos só ocorreu antes da execução de skillset. Com a adição desta habilidade, você também pode realizar esta operação dentro da execução de skillset.

+ [A Tradução de Texto](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva utilizada durante a indexação que avalia o texto e, para cada registo, devolve o texto traduzido para a linguagem-alvo especificada.

+ [Os modelos power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) podem iniciar as suas visualizações e análise de conteúdos enriquecidos numa loja de conhecimentos no ambiente de trabalho power BI. Este modelo foi concebido para projeções de tabela Azure criadas através do [assistente de dados de importação.](knowledge-store-create-portal.md)

+ [Azure Data Lake Storage Gen2 (pré-visualização)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (pré-visualização)](search-howto-index-cosmosdb.md)e [Cosmos DB Cassandra API (pré-visualização)](search-howto-index-cosmosdb.md) são agora suportados em indexadores. Pode inscrever-se usando [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Receberá um e-mail de confirmação assim que tiver sido aceite no programa de pré-visualização.

### <a name="july-2019"></a>Julho de 2019

+ Geralmente disponível na [Nuvem do Governo de Azure.](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)

## <a name="service-updates"></a>Atualizações de serviço

[Os anúncios](https://azure.microsoft.com/updates/?product=search&status=all) de atualização de serviço para A Pesquisa Cognitiva Azure podem ser encontrados no site do Azure.