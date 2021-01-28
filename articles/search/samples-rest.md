---
title: Exemplos REST
titleSuffix: Azure Cognitive Search
description: Encontre amostras de código de demonstração de pesquisa cognitiva Azure que utilizam as APIs de Pesquisa ou Gestão.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956527"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Amostras de código REST para Azure Cognitive Search

Conheça as amostras de API REST que demonstram a funcionalidade e fluxo de trabalho de uma solução de Pesquisa Cognitiva Azure. Estas amostras utilizam as [**APIs de Search REST**](/rest/api/searchservice).

REST é a interface de programação definitiva para Azure Cognitive Search, e todas as operações que podem ser invocadas programáticamente estão disponíveis primeiro em REST e depois em SDKs. Por esta razão, a maioria dos exemplos na documentação aproveitam as APIs do REST para demonstrar ou explicar conceitos importantes.

As amostras REST são geralmente desenvolvidas e testadas no Carteiro, mas pode utilizar qualquer cliente que suporte chamadas HTTP:

+ Comece com [Quickstart: Crie um índice de pesquisa cognitiva Azure usando APIs REST](search-get-started-rest.md) para ajuda na formulação de chamadas HTTP.
+ Experimente [a extensão visual do Código estúdio para Azure Cognitive Search,](search-get-started-vs-code.md)atualmente em pré-visualização, se trabalhar no Código do Estúdio Visual.

## <a name="doc-samples"></a>Amostras de doc

As amostras de código da equipa de Pesquisa Cognitiva demonstram funcionalidades e fluxos de trabalho. Muitas destas amostras são referenciadas em tutoriais, quickstarts e artigos de como fazer. Pode encontrar estas amostras em [**Azure-Samples/azure-search-postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples) no GitHub.

| Amostras | Artigo |
|---------|---------|
| [Início rápido](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Código fonte para [Quickstart: Criar um índice de pesquisa utilizando APIs REST](search-get-started-rest.md). Este artigo abrange o fluxo de trabalho básico para criar, carregar e consultar um índice de pesquisa usando dados de amostra. |
| [Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Código fonte para [Tutorial: Use REST e IA para gerar conteúdo pesmável a partir de bolhas Azure](cognitive-search-tutorial-blob.md). Este artigo mostra-lhe como criar um skillset que itera sobre as bolhas de Azure para extrair informação e inferir estrutura.|
| [Debug-sessões](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Código fonte para [tutorial: Diagnosticar, reparar e comprometer alterações ao seu skillset](cognitive-search-tutorial-debug-sessions.md). Este artigo mostra-lhe como usar uma sessão de skillset debug no portal Azure. REST é utilizado para criar os objetos utilizados durante o depurar.|
| [analisadores personalizados](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Código-fonte para [Tutorial: Criar um analisador personalizado para números de telefone](tutorial-create-custom-analyzer.md). Este artigo explica como usar analisadores para preservar padrões e caracteres especiais em conteúdo pesquisável.|
| [loja de conhecimentos](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Código fonte para [criar uma loja de conhecimentos utilizando REST e Carteiro](knowledge-store-create-rest.md). Este artigo explica os passos necessários para a povoamento de uma loja de conhecimentos utilizada para fluxos de trabalho de mineração de conhecimento. |
| [projeções](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Código fonte [para como moldar e exportar enriquecimentos](knowledge-store-projections-examples.md). Este artigo explica como especificar as estruturas de dados físicos numa loja de conhecimento.|
| [blobs index-encriptados](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Código fonte [para como indexar bolhas encriptadas usando indexadores blob e skillsets](search-howto-index-encrypted-blobs.md). Este artigo mostra como indexar documentos no armazenamento Azure Blob que foram previamente encriptados usando o Cofre da Chave Azure. |

> [!Tip]
> Experimente o [navegador Samples](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) para procurar amostras de código da Microsoft no Github, filtradas por produto, serviço e idioma.

## <a name="other-samples"></a>Outras amostras

As seguintes amostras também são publicadas pela equipa de Pesquisa Cognitiva, mas não são referenciadas em documentação. Os ficheiros de leitura associados fornecem instruções de utilização.

| Amostras | Description |
|---------|-------------|
| [Exemplos de consultas](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Coleções de carteiros que demonstram as várias técnicas de consulta, incluindo pesquisa de fuzzy, pesquisa de RegEx e wildcard, preencha automaticamente, e assim por diante. |