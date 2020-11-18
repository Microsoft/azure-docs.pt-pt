---
title: Exemplos de JavaScript
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código JavaScript de pesquisa cognitiva Azure que utilizam o Azure .NET SDK para JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6bcdb4a48f71e28514229116c10bd25747b55616
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701828"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Amostras de código JavaScript para Pesquisa Cognitiva Azure

Saiba mais sobre as amostras de código JavaScript que demonstram as funcionalidades e funcionalidades da Pesquisa Cognitiva Azure. Os repositórios primários são os seguintes:

| Repositório | Description |
|------------|-------------|
| [azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Amostras produzidas pela equipa da Azure SDK que envia com a biblioteca de clientes Azure.Search.Documents na SDK. Também pode rever [os testes de unidade](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) para a biblioteca do cliente para ver como são chamadas várias APIs. |
| [Amostras de Azure/azure-search-javascript-samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Amostras de código que acompanham artigos como fazer, incluindo [Quickstart: Criar um índice de pesquisa em JavaScript](search-get-started-javascript.md).|

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=csharp&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="javascript-sdk-samples"></a>Amostras de SDK JavaScript

O Azure SDK para Java inclui numerosas amostras e uma [página de início](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) que cobre a instalação de pacotes, configuração do cliente e resolução de problemas. A página também descreve as seguintes categorias de amostras, listadas aqui para sua conveniência.

| Amostras | Description |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Demonstra como criar, atualizar, obter, listar e eliminar [índices de pesquisa](search-what-is-an-index.md). Esta categoria de amostra também inclui uma amostra estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e eliminar fontes de dados indexantes, necessárias para indexar fontes de [dados suportadas](search-indexer-overview.md#supported-data-sources)pelo indexante . |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e eliminar [indexadores](search-indexer-overview.md).|
| [conjunto de habilidades](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e eliminar [habilidades](cognitive-search-working-with-skillsets.md) que são indexantes anexados, e que realizam enriquecimento baseado em IA durante a indexação. |
| [sinónimoMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e eliminar [mapas de sinónimos](search-synonyms.md).  |
| [Consultas](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Demonstra a execução de consultas contra um índice público apenas de leitura hospedado pela Microsoft.  |

## <a name="typescript-samples"></a>Amostras de TypeScript

O SDK também fornece exemplos TypeScript, listados aqui para sua conveniência.

| Amostras | Description |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Demonstra como criar, atualizar, obter, listar e eliminar [índices de pesquisa](search-what-is-an-index.md). Esta categoria de amostra também inclui uma amostra estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e eliminar fontes de dados indexantes, necessárias para indexar fontes de [dados suportadas](search-indexer-overview.md#supported-data-sources)pelo indexante . |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e eliminar [indexadores](search-indexer-overview.md).|
| [conjunto de habilidades](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e eliminar [habilidades](cognitive-search-working-with-skillsets.md) que são indexantes anexados, e que realizam enriquecimento baseado em IA durante a indexação. |
| [sinónimoMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e eliminar [mapas de sinónimos](search-synonyms.md).  |
| [Consultas](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Demonstra a execução de consultas contra um índice público apenas de leitura hospedado pela Microsoft.  |

## <a name="documentation-samples"></a>Exemplos de documentação

As seguintes amostras têm um artigo associado na [documentação de Pesquisa Cognitiva Azure.](https://docs.microsoft.com/azure/search/)

| Amostras | Description | 
|---------|-------------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Código fonte para [Quickstart: Criar um índice de pesquisa em JavaScript](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Amostras autónomas

| Amostras | Description |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Modelo de reação para Azure Cognitive Search (github.com) |