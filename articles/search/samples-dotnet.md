---
title: Amostras de .NET
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código de demonstração de pesquisa cognitiva Azure C# que utilizam as bibliotecas de clientes .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: ab6408621616a4be62631391456f73e90fced752
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499004"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>.NET (C#) amostras de código para Azure Cognitive Search

Saiba mais sobre as amostras de código C# que demonstram as funcionalidades e funcionalidades da Azure Cognitive Search. Os repositórios primários são os seguintes:

| Repositório | Descrição |
|------------|-------------|
| [azure-sdk-for-net/sdk/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Amostras produzidas pela equipa da Azure SDK que envia com a biblioteca de clientes Azure.Search.Documents na SDK. Também pode rever [os testes de unidade](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) para a biblioteca do cliente para ver como são chamadas várias APIs. |
| [Amostras de Azure/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Amostras que acompanham artigos de como fazer na documentação, incluindo [Como utilizar a biblioteca de clientes .NET](search-howto-dotnet-sdk.md).|
| [Azure-Samples/search-dotnet-start-start-start](https://github.com/Azure-Samples/search-dotnet-getting-started) | Amostras que acompanham quickstarts e tutoriais na documentação.|

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=csharp&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="net-sdk-samples"></a>Exemplos de SDK .NET

O Azure SDK para .NET inclui numerosas amostras e um [leitura de amostras](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) que descreve cada uma delas. Essa lista é fornecida abaixo para sua conveniência.

| Amostras | Descrição |
|---------|-------------|
| ["Olá mundo", sincronizadamente](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Demonstra como criar um cliente, autenticar e lidar com erros utilizando métodos sincronizados.|
| ["Olá mundo", assíncronea](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Demonstra como criar um cliente, autenticar e lidar com erros utilizando métodos assíncronos.  |
| [Operações de nível de serviço](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Demonstra como criar índices, indexadores, fontes de dados, skillsets e mapas de sinónimo. Esta amostra também mostra como obter estatísticas de serviço e como consultar um índice.  |
| [Operações de índice](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Demonstra como realizar uma ação sobre o índice existente, neste caso obtendo uma contagem de documentos armazenados no índice.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Demonstra uma técnica para trabalhar com tipos de dados não suportados.  |
| [Documentos de indexação (modelo de impulso)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexação do modelo "Push", onde envia uma carga útil JSON para um índice de um serviço.   |
| [Amostra chave de encriptação](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Demonstra a utilização de uma chave de encriptação gerida pelo cliente para adicionar uma camada extra de proteção sobre conteúdo sensível.  |

## <a name="documentation-samples"></a>Exemplos de documentação

As seguintes amostras têm um artigo associado na [documentação de Pesquisa Cognitiva Azure.](./index.yml)

| Amostras | Descrição |
|---------|-------------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Código fonte para [Quickstart: Criar um índice de pesquisa ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Código fonte [para como utilizar a biblioteca de clientes .NET](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | As listas de synonym são usadas para a expansão de consultas, fornecendo termos compatíveis que são externos a um índice. Esta amostra está incluída em [Exemplo: Adicionar sinónimos em C#](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Código fonte por trás de snippets relacionados com indexante em vários artigos. Este exemplo mostra como configurar um indexante que tem um horário, mapeamentos de campo e parâmetros.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Código fonte para [como configurar chaves geridas pelo cliente para encriptação de dados](search-security-manage-encryption-keys.md) |
| [Crie a sua primeira aplicação em C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Código fonte para [Tutorial: Crie a sua primeira aplicação de pesquisa.](tutorial-csharp-create-first-app.md) Embora a maioria das amostras sejam aplicações de consolas, esta amostra de MVC usa uma página web para fazer frente ao índice de hotéis de amostra, demonstrando pesquisa básica, paginação, consultas, facetas e filtros sugeridos. |
| [fontes de dados múltiplos](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Código-fonte para [Tutorial: Índice de várias fontes de dados](tutorial-multiple-data-sources.md). |
|  [otimizar a indexação de dados](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Código-fonte para [Tutorial: Otimizar a indexação com a API push](tutorial-optimize-indexing-push-api.md).  |
| [tutorial-ai-enriquecimento](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Código fonte para [tutorial: Conteúdo pesmável gerado pela IA a partir de bolhas Azure utilizando o .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Amostras e soluções autónomas

| Amostras | Descrição |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Código fonte para habilidades personalizadas consumíveis que pode incorporar nas suas soluções ganhas.  |
| [Acelerador de Soluções de Extração de Conhecimentos](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Inclui modelos, ficheiros de suporte e relatórios analíticos para ajudá-lo a protótipo de uma solução de mineração de conhecimento de ponta a ponta.  |
| [Repositório de app de pesquisa Covid-19](https://github.com/liamca/covid19search) | Repositório de código fonte para a App de Pesquisa Cognitiva Baseada em [Covid-19](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Saiba mais sobre a [solução JFK.](https://www.microsoft.com/ai/ai-lab-jfk-files) |