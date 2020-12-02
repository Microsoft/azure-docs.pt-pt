---
title: Amostras de Python
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código python de pesquisa cognitiva Azure que usam o Azure .NET SDK para Python ou REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6e0f3d318cc462b03151d5a4935ae318df46e2c5
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510561"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Amostras de código python para pesquisa cognitiva de Azure

Conheça as amostras de código Python que demonstram as funcionalidades e funcionalidades da Pesquisa Cognitiva Azure. Os repositórios primários são os seguintes:

| Repositório | Descrição |
|------------|-------------|
| [azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Amostras produzidas pela equipa da Azure SDK que envia com a biblioteca de clientes Azure.Search.Documents na SDK. Também pode rever [os testes de unidade](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) para a biblioteca do cliente para ver como são chamadas várias APIs. |
| [Amostras de Azure/azure-search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples) | Amostras de código que acompanham artigos como fazer, incluindo [Quickstart: Criar um índice de pesquisa em Python](search-get-started-python.md).|

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=python&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="python-sdk-samples"></a>Amostras do SDK de Python

O Azure SDK para Python inclui numerosas amostras e uma [página de início](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) que inclui pré-requisitos e instalação de pacotes. A página também contém links para as seguintes amostras, listadas aqui para sua conveniência.

| Amostras | Descrição |
|---------|-------------|
| [Autenticar](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Demonstra como configurar um cliente e autenticar o serviço. | 
| [Operações de Criação de Leitura-Leitura-Exclusão](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Demonstra como criar, atualizar, obter, listar e eliminar [índices de pesquisa](search-what-is-an-index.md). |
| [Indexer Criar-Ler-Actualizar-Eliminar operações](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Demonstra como criar, atualizar, obter, listar, redefinir e eliminar [indexadores](search-indexer-overview.md). |
| [Fontes de dados do indexante de pesquisa](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Demonstra como criar, atualizar, obter, listar e eliminar fontes de dados indexantes, necessárias para indexar fontes de [dados suportadas](search-indexer-overview.md#supported-data-sources)pelo indexante . |
| [Sinónimos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Demonstra como criar, atualizar, obter, listar e eliminar [mapas de sinónimos](search-synonyms.md).  |
| [Carregar documentos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Demonstra como carregar ou fundir documentos num índice numa operação [de importação de dados.](search-what-is-data-import.md) |
| [Consulta simples](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Demonstra como configurar uma [consulta básica.](search-query-overview.md) |
| [Consulta de filtro](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Demonstra a configuração de uma [expressão de filtro](search-filters.md). |
| [Consulta faceta](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Demonstra trabalhar com [facetas.](search-filters-facets.md) |

## <a name="documentation-samples"></a>Exemplos de documentação

As seguintes amostras têm um artigo associado na [documentação de Pesquisa Cognitiva Azure.](./index.yml)

| Amostras | Descrição | 
|---------|-------------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Código fonte para [Quickstart: Criar um índice de pesquisa em Python](search-get-started-python.md).  |
| [tutorial-ai-enriquecimento](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Código fonte para [Tutorial: Use Python e IA para gerar conteúdo pesmável a partir de bolhas Azure](cognitive-search-tutorial-blob-python.md).  |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Código fonte por [exemplo: Criar uma habilidade personalizada utilizando Python](cognitive-search-custom-skill-python.md).  |