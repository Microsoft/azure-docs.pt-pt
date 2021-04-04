---
title: Amostras de Python
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código python de pesquisa cognitiva Azure que usam o Azure .NET SDK para Python ou REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955127"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Amostras de código python para pesquisa cognitiva de Azure

Saiba mais sobre as amostras de código Python que demonstram a funcionalidade e fluxo de trabalho de uma solução de Pesquisa Cognitiva Azure. Estas amostras utilizam a biblioteca de [**clientes Azure Cognitive Search**](/python/api/overview/azure/search-documents-readme) para o [**Azure SDK for Python,**](/azure/developer/python/)que pode explorar através dos seguintes links.

| Destino | Ligação |
|--------|------|
| Download de pacote | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| Referência da API | [azure-search-documents](/python/api/azure-search-documents)  |
| Casos de teste de API | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Código de origem | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK samples (Amostras do SDK)

As amostras de código da equipa de desenvolvimento da Azure SDK demonstram o uso da API. Você pode encontrar estas amostras em [**azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) no GitHub.

| Amostras | Description |
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

## <a name="doc-samples"></a>Amostras de doc

As amostras de código da equipa de Pesquisa Cognitiva demonstram funcionalidades e fluxos de trabalho. Muitas destas amostras são referenciadas em tutoriais, quickstarts e artigos de como fazer. Pode encontrar estas amostras em [**Azure-Samples/azure-search-python-samples**](https://github.com/Azure-Samples/azure-search-python-samples) no GitHub.

| Amostras | Artigo |
|---------|---------|
| [arranque rápido](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Código fonte para [Quickstart: Criar um índice de pesquisa em Python](search-get-started-python.md). Este artigo abrange o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de amostra. |
| [tutorial-ai-enriquecimento](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Código fonte para [Tutorial: Use Python e IA para gerar conteúdo pesmável a partir de bolhas Azure](cognitive-search-tutorial-blob-python.md). Este artigo mostra como criar um indexante blob com uma habilidade cognitiva, onde o skillset cria e transforma conteúdo bruto para torná-lo pesmável ou consumível. |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Código fonte por [exemplo: Criar uma habilidade personalizada utilizando Python](cognitive-search-custom-skill-python.md). Este artigo demonstra a integração indexante e skillset com modelos de aprendizagem profunda em Azure Machine Learning. |

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?languages=python&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.