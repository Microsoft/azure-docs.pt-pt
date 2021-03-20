---
title: Exemplos do Java
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código de demonstração de Azure Cognitive Search Java que usam o Azure .NET SDK para Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955042"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Amostras de código de Java para pesquisa cognitiva Azure

Conheça as amostras de código Java que demonstram a funcionalidade e fluxo de trabalho de uma solução de Pesquisa Cognitiva Azure. Estas amostras utilizam a biblioteca de [**clientes Azure Cognitive Search**](/java/api/overview/azure/search-documents-readme) para o [**Azure SDK para Java,**](/azure/developer/java/sdk)que pode explorar através dos seguintes links.

| Destino | Ligação |
|--------|------|
| Download de pacote | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| Referência da API | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Código de origem | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK samples (Amostras do SDK)

As amostras de código da equipa de desenvolvimento da Azure SDK demonstram o uso da API. Pode encontrar estas amostras em [**Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) no GitHub.

| Amostras | Description |
|---------|-------------|
| [Criação de índice de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Demonstra como criar [índices de pesquisa.](search-what-is-an-index.md) |
| [Criação de sinónimo](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Demonstra como criar [mapas de sinónimo.](search-synonyms.md)  |
| [Criação de indexação de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Demonstra como criar [indexantes.](search-indexer-overview.md) |
| [Criação de fonte de dados indexante de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Demonstra como criar fontes de dados indexantes, necessárias para indexar fontes de [dados suportadas](search-indexer-overview.md#supported-data-sources)pelo indexante . |
| [Criação de Skillset](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Demonstra como criar [skillsets](cognitive-search-working-with-skillsets.md) que são indexadores anexados, e que realizam enriquecimento baseado em IA durante a indexação. |
| [Carregar documentos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Demonstra como carregar ou fundir documentos num índice numa operação [de importação de dados.](search-what-is-data-import.md) |
| [Sintaxe de consulta](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Demonstra como configurar uma [consulta básica.](search-query-overview.md) |

## <a name="doc-samples"></a>Amostras de doc

As amostras de código da equipa de Pesquisa Cognitiva demonstram funcionalidades e fluxos de trabalho. Muitas destas amostras são referenciadas em tutoriais, quickstarts e artigos de como fazer. Pode encontrar estas amostras em [**Azure-Samples/azure-search-java-samples**](https://github.com/Azure-Samples/azure-search-java-samples) no GitHub.

| Amostras | Artigo | 
|---------|-------------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Código fonte para [Quickstart: Criar um índice de pesquisa em Java e REST](search-get-started-java.md). Esta amostra não foi atualizada para o SDK Java. Chama as APIs de DESCANSO. |

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=java&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

As seguintes amostras também são publicadas pela equipa de Pesquisa Cognitiva, mas não são referenciadas em documentação. Os ficheiros de leitura associados fornecem instruções de utilização.

| Amostras | Description |
|---------|-------------|
| [search-java-getting-start](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Utiliza a biblioteca de clientes Java SDK para criar, carregar e consultar um índice de pesquisa. Esta amostra é atualmente autónoma. |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Demonstra um indexador Azure Cosmos DB em Java. Esta amostra não foi atualizada para o SDK Java. Chama as APIs de DESCANSO.|