---
title: Amostras de .NET
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código de demonstração de pesquisa cognitiva Azure C# que utilizam as bibliotecas de clientes .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5567cf3bf606b08ce430f9189467d796498ae691
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953908"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>.NET (C#) amostras de código para Azure Cognitive Search

Saiba mais sobre as amostras de código C# que demonstram a funcionalidade e fluxo de trabalho de uma solução de Pesquisa Cognitiva Azure. Estas amostras utilizam a biblioteca de [**clientes Azure Cognitive Search**](/dotnet/api/overview/azure/search) para o [**Azure SDK para .NET,**](/dotnet/azure/)que pode explorar através dos seguintes links.

| Destino | Ligação |
|--------|------|
| Download de pacote | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| Referência da API | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/testes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Código de origem | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK samples (Amostras do SDK)

As amostras de código da equipa de desenvolvimento da Azure SDK demonstram o uso da API. Pode encontrar estas amostras em [**Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) no GitHub.

| Amostras | Description |
|---------|-------------|
| ["Olá mundo", sincronizadamente](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Demonstra como criar um cliente, autenticar e lidar com erros utilizando métodos sincronizados.|
| ["Olá mundo", assíncronea](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Demonstra como criar um cliente, autenticar e lidar com erros utilizando métodos assíncronos.  |
| [Operações de nível de serviço](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Demonstra como criar índices, indexadores, fontes de dados, skillsets e mapas de sinónimo. Esta amostra também mostra como obter estatísticas de serviço e como consultar um índice.  |
| [Operações de índice](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Demonstra como realizar uma ação sobre o índice existente, neste caso obtendo uma contagem de documentos armazenados no índice.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Demonstra uma técnica para trabalhar com tipos de dados não suportados.  |
| [Documentos de indexação (modelo de impulso)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexação do modelo "Push", onde envia uma carga útil JSON para um índice de um serviço.   |
| [Amostra chave de encriptação](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Demonstra a utilização de uma chave de encriptação gerida pelo cliente para adicionar uma camada extra de proteção sobre conteúdo sensível.  |

## <a name="doc-samples"></a>Amostras de doc

As amostras de código da equipa de Pesquisa Cognitiva demonstram funcionalidades e fluxos de trabalho. Muitas destas amostras são referenciadas em tutoriais, quickstarts e artigos de como fazer. Pode encontrar estas amostras em [**amostras Azure-Samples/azure-search-dotnet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) e em [**Azure-Samples/search-dotnet-start-start-start-ins**](https://github.com/Azure-Samples/search-dotnet-getting-started/) no GitHub.

| Amostras | Artigo  |
|---------|-------------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Código fonte para [Quickstart: Criar um índice de pesquisa ](search-get-started-dotnet.md). Este artigo abrange o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de amostra. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Código fonte [para como utilizar a biblioteca de clientes .NET](search-howto-dotnet-sdk.md). Este artigo percorre o fluxo básico de trabalho, mas em mais detalhes e discussão sobre o uso da API.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Código fonte por [exemplo: Adicionar sinónimos em C#](search-synonyms-tutorial-sdk.md). As listas de synonym são usadas para a expansão de consultas, fornecendo termos compatíveis que são externos a um índice. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Código-fonte para [tutorial: Dados indexados Azure SQL utilizando o .NET SDK](search-indexer-tutorial.md). Este artigo mostra como configurar um indexador Azure SQL que tem um horário, mapeamentos de campo e parâmetros.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Código fonte [para como configurar chaves geridas pelo cliente para encriptação de dados](search-security-manage-encryption-keys.md). |
| [Crie a sua primeira aplicação em C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Código fonte para [Tutorial: Crie a sua primeira aplicação de pesquisa.](tutorial-csharp-create-first-app.md) Embora a maioria das amostras sejam aplicações de consolas, esta amostra de MVC usa uma página web para fazer frente ao índice de hotéis de amostra, demonstrando pesquisa básica, paginação, consultas, facetas e filtros sugeridos. |
| [fontes de dados múltiplos](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Código-fonte para [Tutorial: Índice de várias fontes de dados](tutorial-multiple-data-sources.md). |
|  [otimizar a indexação de dados](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Código-fonte para [Tutorial: Otimizar a indexação com a API push](tutorial-optimize-indexing-push-api.md).  |
| [tutorial-ai-enriquecimento](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Código fonte para [tutorial: Conteúdo pesmável gerado pela IA a partir de bolhas Azure utilizando o .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=csharp&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

As seguintes amostras também são publicadas pela equipa de Pesquisa Cognitiva, mas não são referenciadas em documentação. Os ficheiros de leitura associados fornecem instruções de utilização.

| Amostras | Description |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Código fonte para habilidades personalizadas consumíveis que pode incorporar nas suas soluções ganhas.  |
| [Acelerador de Soluções de Extração de Conhecimentos](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Inclui modelos, ficheiros de suporte e relatórios analíticos para ajudá-lo a protótipo de uma solução de mineração de conhecimento de ponta a ponta.  |
| [Repositório de app de pesquisa Covid-19](https://github.com/liamca/covid19search) | Repositório de código fonte para a App de Pesquisa Cognitiva Baseada em [Covid-19](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Saiba mais sobre a [solução JFK.](https://www.microsoft.com/ai/ai-lab-jfk-files) |