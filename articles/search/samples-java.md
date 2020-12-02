---
title: Exemplos do Java
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código de demonstração de Azure Cognitive Search Java que usam o Azure .NET SDK para Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: c1d21d88ed49cb14aa9f964791154f001a40fbe2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499582"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Amostras de código de Java para pesquisa cognitiva Azure

Conheça as amostras de código Java que demonstram as funcionalidades e funcionalidades da Azure Cognitive Search. Os repositórios primários são os seguintes:

| Repositório | Descrição |
|------------|-------------|
| [azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Amostras produzidas pela equipa da Azure SDK que envia com a biblioteca de clientes Azure.Search.Documents na SDK. Também pode rever [os testes de unidade](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) para a biblioteca do cliente para ver como são chamadas várias APIs. |
| [Amostras de Azure/azure-search-java-samples](https://github.com/Azure-Samples/azure-search-java-samples) | Amostras de código que acompanham artigos como fazer. **As amostras deste repositório ainda não foram atualizadas para a utilização do Azure SDK para Java**. Atualmente, estas amostras chamam APIs REST em código Java.|

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=csharp&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="java-sdk-samples"></a>Exemplos do SDK Java

O Azure SDK para Java inclui numerosas amostras e uma [página de início](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) que cobre a instalação de pacotes. A página também lista uma ampla gama de exemplos. Várias das operações mais comuns estão listadas abaixo para sua conveniência.

| Amostras | Descrição |
|---------|-------------|
| [Criação de índice de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Demonstra como criar [índices de pesquisa.](search-what-is-an-index.md) |
| [Criação de sinónimo](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Demonstra como criar [mapas de sinónimo.](search-synonyms.md)  |
| [Criação de indexação de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Demonstra como criar [indexantes.](search-indexer-overview.md) |
| [Criação de fonte de dados indexante de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Demonstra como criar fontes de dados indexantes, necessárias para indexar fontes de [dados suportadas](search-indexer-overview.md#supported-data-sources)pelo indexante . |
| [Criação de Skillset](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Demonstra como criar [skillsets](cognitive-search-working-with-skillsets.md) que são indexadores anexados, e que realizam enriquecimento baseado em IA durante a indexação. |
| [Carregar documentos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Demonstra como carregar ou fundir documentos num índice numa operação [de importação de dados.](search-what-is-data-import.md) |
| [Sintaxe de consulta](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Demonstra como configurar uma [consulta básica.](search-query-overview.md) |

## <a name="documentation-samples"></a>Exemplos de documentação

As seguintes amostras têm um artigo associado na [documentação de Pesquisa Cognitiva Azure.](./index.yml)

| Amostras | Descrição | 
|---------|-------------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Código fonte para [Quickstart: Criar um índice de pesquisa em Java](search-get-started-java.md). Esta amostra chama as APIs rest. |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Demonstra um indexador Azure Cosmos DB em Java. Esta amostra chama as APIs rest. |