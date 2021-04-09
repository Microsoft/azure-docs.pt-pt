---
title: Exemplos de JavaScript
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código JavaScript de pesquisa cognitiva Azure que utilizam o Azure .NET SDK para JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 85a4d6390087100d8d9521f6ac20dbace3a711eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955946"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Amostras de código JavaScript para Pesquisa Cognitiva Azure

Saiba mais sobre as amostras de código JavaScript que demonstram a funcionalidade e fluxo de trabalho de uma solução de Pesquisa Cognitiva Azure. Estas amostras utilizam a biblioteca de [**clientes Azure Cognitive Search**](/javascript/api/overview/azure/search-documents-readme) para o [**Azure SDK para JavaScript,**](/azure/developer/javascript/)que pode explorar através dos seguintes links.

| Destino | Ligação |
|--------|------|
| Download de pacote | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| Referência da API | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Código de origem | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK samples (Amostras do SDK)

As amostras de código da equipa de desenvolvimento da Azure SDK demonstram o uso da API. Pode encontrar estas amostras em [**azure-sdk-for-js/tree/master/sdk/search/search-documents/samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) no GitHub.

### <a name="javascript-sdk-samples"></a>Amostras de SDK JavaScript

| Amostras | Description |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Demonstra como criar, atualizar, obter, listar e eliminar [índices de pesquisa](search-what-is-an-index.md). Esta categoria de amostra também inclui uma amostra estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e eliminar fontes de dados indexantes, necessárias para indexar fontes de [dados suportadas](search-indexer-overview.md#supported-data-sources)pelo indexante . |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e eliminar [indexadores](search-indexer-overview.md).|
| [conjunto de habilidades](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e eliminar [habilidades](cognitive-search-working-with-skillsets.md) que são indexantes anexados, e que realizam enriquecimento baseado em IA durante a indexação. |
| [sinónimoMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e eliminar [mapas de sinónimos](search-synonyms.md).  |

### <a name="typescript-samples"></a>Amostras de TypeScript

| Amostras | Description |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Demonstra como criar, atualizar, obter, listar e eliminar [índices de pesquisa](search-what-is-an-index.md). Esta categoria de amostra também inclui uma amostra estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e eliminar fontes de dados indexantes, necessárias para indexar fontes de [dados suportadas](search-indexer-overview.md#supported-data-sources)pelo indexante . |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e eliminar [indexadores](search-indexer-overview.md).|
| [conjunto de habilidades](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e eliminar [habilidades](cognitive-search-working-with-skillsets.md) que são indexantes anexados, e que realizam enriquecimento baseado em IA durante a indexação. |
| [sinónimoMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e eliminar [mapas de sinónimos](search-synonyms.md).  |

## <a name="doc-samples"></a>Amostras de doc

As amostras de código da equipa de Pesquisa Cognitiva demonstram funcionalidades e fluxos de trabalho. Muitas destas amostras são referenciadas em tutoriais, quickstarts e artigos de como fazer. Pode encontrar estas amostras em [**amostras Azure-Samples/azure-search-javascript-samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) no GitHub.

| Amostras | Artigo |
|---------|---------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Código fonte para [Quickstart: Criar um índice de pesquisa em JavaScript](search-get-started-javascript.md). Este artigo abrange o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de amostra. |

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=javascript&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

As seguintes amostras também são publicadas pela equipa de Pesquisa Cognitiva, mas não são referenciadas em documentação. Os ficheiros de leitura associados fornecem instruções de utilização.

| Amostras | Description |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Modelo de reação para Azure Cognitive Search (github.com) |