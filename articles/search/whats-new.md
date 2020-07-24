---
title: Novidades na Pesquisa Cognitiva Azure
description: Anúncios de funcionalidades novas e melhoradas, incluindo um nome de serviço de Azure Search to Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/15/2020
ms.openlocfilehash: 8c6486a3a64dcbe3dd37770844296da73724f3a3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076339"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades na Pesquisa Cognitiva Azure

Saiba o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço.

## <a name="feature-announcements-in-2020"></a>Anúncios de recursos em 2020

### <a name="july-2020"></a>Julho de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [biblioteca de clientesAzure.Search.Doc](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) | SDK do Azure para .NET | Biblioteca de clientes .NET lançada pela equipa Azure SDK, projetada para a consistência com outras bibliotecas de clientes .NET. <br/><br/>A versão 11 tem como alvo a versão api-versão search REST=2020-06-30, mas ainda não suporta a loja de conhecimento, geo-filtros ou [FieldBuilder.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet) | Geralmente disponível. </br> Instale o [pacoteAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) da NuGet. |
| [biblioteca de clientesazure.search.doc](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme?view=azure-python)  | SDK do Azure para Python| Biblioteca de clientes Python lançada pela equipa Azure SDK, projetada para a consistência com outras bibliotecas de clientes Python. <br/><br/>A versão 11 tem como alvo a versão api-versão search REST=2020-06-30. | Geralmente disponível. </br> Instale o [pacote de documentos de pesquisa de azul](https://pypi.org/project/azure-search-documents/) a partir de PyPI. |
| [@azure/search-documentsbiblioteca cliente](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest)  | SDK do Azure para JavaScript | Biblioteca de clientes JavaScript lançada pela equipa Azure SDK, projetada para a consistência com outras bibliotecas de clientes JavaScript. <br/><br/>A versão 11 tem como alvo a versão api-versão search REST=2020-06-30. | Geralmente disponível. </br> Instale a [ @azure/search-documents embalagem](https://www.npmjs.com/package/@azure/search-documents) a partir da npm. |

### <a name="june-2020"></a>Junho de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
[**Arquivo de dados de conhecimento**](knowledge-store-concept-intro.md) | Melhoramento de IA | Saída de um indexante enriquecido em IA, armazenando conteúdo no Azure Storage para utilização em outras aplicações e processos. | Geralmente disponível. </br> Utilize [a API DE PESQUISA REST 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) ou posterior, ou o portal. |
| [**Pesquisa REST API 2020-06-30**](https://docs.microsoft.com/rest/api/searchservice/) | REST | Uma nova versão estável das APIs REST. Além da loja de conhecimento, esta versão inclui melhorias para pesquisar relevância e pontuação. | Geralmente disponível. |
| [**Algoritmo de relevância Okapi BM25**](https://en.wikipedia.org/wiki/Okapi_BM25) | Consulta | Novo algoritmo de classificação de relevância automaticamente usado para todos os novos serviços de pesquisa criados após 15 de julho. Para serviços criados anteriormente, pode optar pela definição da propriedade em `similarity` campos de índice. | Geralmente disponível. </br> Use [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) ou mais tarde, ou REST API 2019-05-06. |
| **execuçãoEnvironment** | Segurança (indexantes) | Desaponte explicitamente esta propriedade de configuração indexante `private` para forçar todas as ligações a fontes de dados externas sobre um ponto final privado. Aplicável apenas aos serviços de pesquisa que alavancam a Azure Private Link. | Geralmente disponível. </br> Utilize [a API DE PESQUISA REST 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) para definir este parâmetro de configuração geral. |

### <a name="may-2020-microsoft-build"></a>Maio de 2020 (Microsoft Build)

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Sessões de depuração**](cognitive-search-debug-session.md) | Melhoramento de IA | As sessões de Debug fornecem uma interface baseada no portal para investigar e resolver problemas com um skillset existente. As correções criadas na sessão de depurar podem ser guardadas para as habilidades de produção. Começa com [este tutorial.](cognitive-search-tutorial-debug-sessions.md) | Pré-visualização pública, no portal. |
| [**Regras de IP para suporte a firewall in-bound**](service-configure-firewall.md) | Segurança | Limite o acesso a um ponto final do serviço de pesquisa a endereços IP específicos. | Geralmente disponível. </br> Utilizar [Gestão REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) ou posterior, ou o portal. |
| [**Azure Private Link para um ponto final de pesquisa privada**](service-create-private-endpoint.md) | Segurança| Proteja um serviço de pesquisa da internet pública executando-o como um recurso de ligação privada, acessível apenas a aplicações de clientes e outros serviços Azure na mesma rede virtual. | Geralmente disponível. </br> Utilizar [Gestão REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) ou posterior, ou o portal. |
| [**identidade gerida pelo sistema (pré-visualização)**](search-howto-managed-identities-data-sources.md) | Segurança (indexantes) | Registe um serviço de pesquisa como um serviço de confiança com o Azure Ative Directory para configurar ligações para a fonte de dados suportada do Azure para indexação. Aplica-se a [indexadores](search-indexer-overview.md) que ingerem conteúdo de fontes de dados Azure, tais como Azure SQL Database, Azure Cosmos DB e Azure Storage. | Pré-visualização pública. </br> Utilize o portal para registar o serviço de pesquisa. |
| [**parâmetro de consulta sessionId**](index-similarity-and-scoring.md), [scoringStatistics=parâmetro global](index-similarity-and-scoring.md#scoring-statistics) | Consulta (relevância) | Adicione a sessãoID a uma consulta para estabelecer uma sessão para calcular pontuações de pesquisa, com pontuaçãoStatísticas=global para recolher pontuações de todos os fragmentos, para cálculos de pontuação de pesquisa mais consistentes. | Geralmente disponível. </br> Use [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) ou mais tarde, ou REST API 2019-05-06. |
| [**featuresMode (pré-visualização)**](index-similarity-and-scoring.md#featuresMode-param) | Consulta | Adicione este parâmetro de consulta para expandir uma pontuação de relevância para mostrar mais detalhes: por pontuação de semelhança de campo, por frequência de período de campo, e por número de campo de fichas únicas correspondidas. Pode consumir estes pontos de dados em algoritmos de pontuação personalizados. Para obter uma amostra que demonstre esta capacidade, consulte [Adicionar machine learning (LearnToRank) para procurar relevância](https://github.com/Azure-Samples/search-ranking-tutorial). | Pré-visualização pública. </br> Use [Search REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) ou REST API 2019-05-06-Preview. |

### <a name="march-2020"></a>Março de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Exclusão suave de blob nativo (pré-visualização)**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | Indexadores | Um indexante de armazenamento Azure Blob na Azure Cognitive Search reconhecerá as bolhas que estão em estado de eliminação suave e removerá o documento de pesquisa correspondente durante a indexação. | Pré-visualização pública. </br> Utilize o [Search REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) e REST API 2019-05-06-Preview, com Índice de Execução contra uma fonte de dados Azure Blob que tem "soft delete" ativada. |
| [**Gestão REST API (2020-03-13)**](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | Nova API rest estável para criar e gerir um serviço de pesquisa. Adiciona firewall IP e suporte de ligação privada | Geralmente disponível. |

### <a name="february-2020"></a>Fevereiro de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Deteção pii (pré-visualização)**](cognitive-search-skill-pii-detection.md) | Melhoramento de IA | Uma nova habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de mascar a partir desse texto de várias maneiras. | Pré-visualização pública. </br> Utilize o portal ou [Search REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) ou REST API 2019-05-06-Preview. |
| [**Procura de Entidade Personalizada (pré-visualização)**](cognitive-search-skill-custom-entity-lookup.md )| Melhoramento de IA | Uma nova habilidade cognitiva que procura texto a partir de uma lista personalizada, definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência difusa que pode ser aplicada para encontrar fósforos que são semelhantes, mas não exatos. | Pré-visualização pública. </br> Utilize o portal ou [Search REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) ou REST API 2019-05-06-Preview. |

### <a name="january-2020"></a>Janeiro de 2020

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Chaves de encriptação geridas pelo cliente**](search-security-manage-encryption-keys.md) |Segurança | Adiciona uma camada extra de encriptação para além da encriptação incorporada da plataforma. Utilizando uma chave de encriptação que cria e gere, pode encriptar o conteúdo do índice e os mapas de sinónimo antes que a carga útil chegue a um serviço de pesquisa. | Geralmente disponível. </br> Utilize a API DE PESQUISA REST 2019-05-06 ou posterior. Para o código gerido, o pacote correto ainda é [a pré-visualização da versão 8.0 net SDK,](search-dotnet-sdk-migration-version-9.md) mesmo que a funcionalidade esteja fora de pré-visualização. |
| [**Regras de IP para suporte a firewall (pré-visualização)**](service-configure-firewall.md) | Segurança | Limite o acesso a um ponto final do serviço de pesquisa a endereços IP específicos. A API de pré-visualização tem novas propriedades **IpRule** e **NetworkRuleSet** na [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Esta funcionalidade de pré-visualização está disponível em regiões selecionadas. |  Pré-visualização pública utilizando api-versão=2019-10-01-Preview.  |
| [**Azure Private Link para um ponto final de pesquisa privado (pré-visualização)**](service-create-private-endpoint.md) | Segurança| Proteja um serviço de pesquisa da internet pública executando-o como um recurso de ligação privada, acessível apenas a aplicações de clientes e outros serviços Azure na mesma rede virtual. | Pré-visualização pública utilizando api-versão=2019-10-01-Preview.  |

## <a name="feature-announcements-in-2019"></a>Anúncios de recursos em 2019

### <a name="december-2019"></a>Dezembro de 2019

+ [Create Demo App (pré-visualização)](search-create-app-portal.md) é um novo assistente no portal que gera um ficheiro HTML transferível com acesso de consulta (apenas para leitura) a um índice. O ficheiro vem com um script incorporado que torna uma aplicação web de estilo "localhost", ligada a um índice no seu serviço de pesquisa. As páginas são configuráveis no assistente e podem conter uma barra de pesquisa, área de resultados, navegação na barra lateral e suporte de consulta de cabeça de tipo. Pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência. Uma app de demonstração não é facilmente estendida para incluir camadas de segurança e hospedagem que são normalmente necessárias em cenários de produção. Deve considerá-lo como uma ferramenta de validação e teste em vez de um atalho para uma aplicação completa do cliente.

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