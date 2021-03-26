---
title: Novidades na Pesquisa Cognitiva Azure
description: Anúncios de funcionalidades novas e melhoradas, incluindo um nome de serviço de Azure Search to Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/12/2021
ms.custom: references_regions
ms.openlocfilehash: 99a7f08b6d366f2f928b99caba2424c2fae0d625
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559524"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Novidades na Pesquisa Cognitiva Azure

Saiba o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço. Consulte a [lista de funcionalidades de pré-visualização](search-api-preview.md) para ver uma lista completa de funcionalidades que ainda não estão disponíveis em geral.

## <a name="march-2021"></a>março de 2021

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Disponibilidade  |
|------------------------------|---------------|---------------|
| [Pesquisa semântica](semantic-search-overview.md) | Uma coleção de funcionalidades relacionadas com consultas que melhoram significativamente a relevância dos resultados da pesquisa através de ajustes mínimos a um pedido de consulta. </br></br>[O ranking semântico](semantic-ranking.md) calcula pontuações de relevância usando o significado semântico por trás de palavras e conteúdo. </br></br>[As legendas semânticas](semantic-how-to-query-request.md) devolvem passagens relevantes do documento que melhor resumem o documento, com destaques sobre os termos ou frases mais importantes. </br></br>[As respostas semânticas](semantic-answers.md) devolvem passagens-chave, extraídas de um documento de pesquisa, que são formuladas como resposta direta a uma consulta que parece uma pergunta. | Pré-visualização pública ([a pedido).](https://aka.ms/SemanticSearchPreviewSignup) </br></br>Utilize [documentos de pesquisa (REST)](/rest/api/searchservice/preview-api/search-documents) versão api=2020-06-30-Preview ou [Explorador de Pesquisa](search-explorer.md) no portal Azure. </br></br>Aplicam-se restrições de região e de nível. |
| [Termos de consulta de verificação ortográfica](speller-how-to-add.md) | Antes de os termos de consulta chegarem ao motor de busca, pode verificar se há erros ortográficos. A `speller` opção funciona com qualquer tipo de consulta (simples, completa ou semântica). |  Pré-visualização pública, REST only, api-version=2020-06-30-Preview|
| [Indexador Online SharePoint](search-howto-index-sharepoint-online.md) | Este indexante liga-o a um site SharePoint Online para que possa indexar o conteúdo a partir de uma biblioteca de documentos. | Pré-visualização pública, REST only, api-version=2020-06-30-Preview |
| [Normalizadores](search-normalizers.md) | Os normalizadores fornecem um simples pré-processamento de texto como invólucro, remoção de sotaque, asciifolding e assim por diante sem passar por toda a cadeia de análise.| Pré-visualização pública, REST only, api-version=2020-06-30-Preview |

## <a name="february-2021"></a>Fevereiro de 2021

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Disponibilidade  |
|------------------------------|---------------|---------------|
| [Documentos de reset (pré-visualização)](search-howto-run-reset-indexers.md) |  Reprocessa documentos de pesquisa selecionados individualmente em cargas de trabalho indexantes. | [Pesquisa REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [Zonas de Disponibilidade](search-performance-optimization.md#availability-zones)| Os serviços de pesquisa com duas ou mais réplicas em determinadas regiões, conforme listados em [Escala para desempenho,](search-performance-optimization.md#availability-zones)ganham resiliência por terem réplicas em dois ou locais físicos mais distintos.  | A região e a data de criação do serviço de pesquisa determinam a disponibilidade. Consulte a Escala para obter mais detalhes. |
| [CLI do Azure](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | As novas revisões passam a fornecer toda a gama de operações na Gestão REST API 2020-08-01, incluindo o apoio às regras de firewall IP e ao ponto final privado. | Geralmente disponível. |

## <a name="january-2021"></a>Janeiro de 2021

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Disponibilidade  |
|------------------------------|-------------|---------------|
| [Acelerador de soluções para Azure Cognitive Search e QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Retira perguntas e respostas do documento e sugere as respostas mais relevantes. Uma aplicação de demonstração ao vivo pode ser encontrada em [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) .  | Projeto de código aberto (sem SLA) |

## <a name="2020-archive"></a>Arquivo 2020

| Mensal | Funcionalidade | Descrição |
|-------|---------|-------------|
| Novembro | [Encriptação de chaves gerida pelo cliente (estendida)](search-security-manage-encryption-keys.md) | Alarga a encriptação gerida pelo cliente sobre toda a gama de ativos criados e geridos por um serviço de pesquisa. Geralmente disponível.|
| Setembro | [Extensão do Código do Estúdio Visual para Pesquisa Cognitiva Azure](search-get-started-vs-code.md) | Adiciona um espaço de trabalho, navegação, intellisense e modelos para a criação de índices, indexadores, fontes de dados e skillsets. Esta funcionalidade encontra-se atualmente em visualização pública.| 
| Setembro | [Identidade de serviço gerida (indexantes)](search-howto-managed-identities-data-sources.md) | Geralmente disponível.  |
| Setembro | [Pedidos de saída usando um link privado](search-indexer-howto-access-private.md) | Geralmente disponível.  |
| Setembro | [Gestão REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Geralmente disponível. |
| Setembro | [Gestão REST API (2020-08-01-Pré-visualização)](/rest/api/searchmanagement/management-api-versions) | Adiciona recurso de ligação privada compartilhado para Funções Azure e Azure SQL para MySQL Databases. |
| Setembro | [Gestão .NET SDK 4.0](/dotnet/api/overview/azure/search/management) |  A atualização Azure SDK para a gestão SDK, direcionada para a versão REST API 2020-08-01. Geralmente disponível.|
| Agosto | [encriptação dupla](search-security-overview.md#encryption) | Geralmente disponível em todos os serviços de pesquisa criados após 1 de agosto de 2020 nestas regiões: West US 2, East US, South Central US, US Gov Virginia, US Gov Arizona. |
| Julho | [ biblioteca de clientesAzure.Search.Doc](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK para .NET, geralmente disponível. |
| Julho | [ biblioteca de clientesazure.search.doc](/python/api/overview/azure/search-documents-readme)  | Azure SDK para Python, geralmente disponível. |
| Julho | [@azure/search-documents biblioteca cliente](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK para JavaScript, geralmente disponível. |
| Junho | [Arquivo de dados de conhecimento](knowledge-store-concept-intro.md) | Geralmente disponível. |
| Junho | [Pesquisa REST API 2020-06-30](/rest/api/searchservice/) | Geralmente disponível. |
| Junho | [Pesquisa REST API 2020-06-30-Preview](/rest/api/searchservice/) | Adiciona o Reset Skillset a habilidades de reprocessamento seletiva e enriquecimento incremental. |
| Junho | [Algoritmo de relevância Okapi BM25](index-ranking-similarity.md) | Geralmente disponível. |
| Junho |  **execuçãoObiente** (aplica-se aos serviços de pesquisa utilizando a Azure Private Link.) | Geralmente disponível. |
| Junho | [Habilidade AML (pré-visualização)](cognitive-search-aml-skill.md) | Uma habilidade cognitiva que alarga o enriquecimento de IA com um modelo personalizado de Aprendizagem automática Azure (AML). |
| May | [Sessões de depuro (pré-visualização)](cognitive-search-debug-session.md) | Skillset debugger no portal.  |
| May | [Regras de IP para suporte a firewall in-bound](service-configure-firewall.md) | Geralmente disponível.  |
| May | [Azure Private Link para um ponto final de pesquisa privada](service-create-private-endpoint.md) | Geralmente disponível.  |
| May | [Identidade de serviço gerida (indexantes) - (pré-visualização)](search-howto-managed-identities-data-sources.md) | Ligue-se a fontes de dados Azure usando uma identidade gerida.  |
| May | [parâmetro de consulta sessionId](index-similarity-and-scoring.md), [scoringStatistics=parâmetro global](index-similarity-and-scoring.md#scoring-statistics)  | Estatísticas globais de pesquisa, úteis para [modelos de machine learning (LearnToRank) para relevância de pesquisa.](https://github.com/Azure-Samples/search-ranking-tutorial)  |
| May | [featuresA expansão da pontuação de relevância demode (pré-visualização)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Março  | [Exclusão suave de blob nativo (pré-visualização)](search-howto-index-changed-deleted-blobs.md) | Elimina os documentos de pesquisa se a bolha de origem for apagada suavemente no armazenamento de bolhas. |
|Março  | [Gestão REST API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Geralmente disponível. |
|February | [Habilidade de deteção pii (pré-visualização)](cognitive-search-skill-pii-detection.md)  | Uma habilidade cognitiva que extrai e mascara informações pessoais. |
|February | [Habilidade de procura de entidade personalizada (pré-visualização)](cognitive-search-skill-custom-entity-lookup.md) | Uma habilidade cognitiva que encontra palavras e frases de uma lista e rotula todos os documentos com entidades correspondentes.  |
|Janeiro | [Encriptação de chaves gerida pelo cliente](search-security-manage-encryption-keys.md) | Disponível em Geral  |
|Janeiro | [Regras de IP para suporte a firewall (pré-visualização)](service-configure-firewall.md) | Novas propriedades **IpRule** e **NetworkRuleSet** em [CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|Janeiro | [Criar um ponto final privado (pré-visualização)](service-create-private-endpoint.md) | Crie uma Ligação Privada para ligações seguras ao seu serviço de pesquisa. Esta funcionalidade de pré-visualização tem uma [ligação privada Azure](../private-link/private-link-overview.md) de dependência e [rede virtual Azure](../virtual-network/virtual-networks-overview.md) como parte da solução. |

## <a name="2019-archive"></a>Arquivo 2019

| Mensal | Funcionalidade | Descrição |
|-------|---------|-------------|
|Dezembro | [Criar App Demo (pré-visualização)](search-create-app-portal.md) | Um assistente que gere um ficheiro HTML transferível com acesso de consulta (apenas para leitura) a um índice, destinado como uma ferramenta de validação e teste em vez de um atalho para uma aplicação completa do cliente.|
|Novembro | [Enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) | Caches skillset processamento para futura reutilização.  |
|Novembro | [Habilidade de extração de documentos (pré-visualização)](cognitive-search-skill-document-extraction.md) | Uma habilidade cognitiva para extrair o conteúdo de um ficheiro de dentro de um skillset.|
|Novembro | [Habilidade de tradução de texto](cognitive-search-skill-text-translation.md) | Uma habilidade cognitiva usada durante a indexação que avalia e traduz texto. Geralmente disponível.|
|Novembro | [Modelos de BI de potência](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Modelo para visualizar conteúdo na loja de conhecimento |
|Novembro | [Azure Data Lake Storage Gen2 (pré-visualização)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (pré-visualização)](search-howto-index-cosmosdb.md)e [Cosmos DB Cassandra API (pré-visualização)](search-howto-index-cosmosdb.md) | Novas fontes de dados indexantes na pré-visualização pública. |
|Julho | [Apoio à nuvem do governo Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Geralmente disponível.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Novo nome do serviço

A Azure Search foi renomeada para **Azure Cognitive Search** em outubro de 2019 para refletir o uso expandido (ainda opcional) de habilidades cognitivas e processamento de IA em operações centrais. As versões API, pacotes NuGet, espaços de nome e pontos finais são inalterados. As novas e existentes soluções de pesquisa não são afetadas pela alteração do nome de serviço.

## <a name="service-updates"></a>Atualizações de serviço

[Os anúncios de atualização de](https://azure.microsoft.com/updates/?product=search&status=all) serviço para Azure Cognitive Search podem ser encontrados no site do Azure.