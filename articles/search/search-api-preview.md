---
title: Funcionalidades de pré-visualização em REST API
titleSuffix: Azure Cognitive Search
description: O serviço de pesquisa cognitiva Azure REST API Versão 2019-05-06-Preview inclui funcionalidades experimentais como a loja de conhecimento e o encado do indexante para enriquecimento incremental.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 9985e7ac70c5851699839a95d1e23af4dcca35e7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935106"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funcionalidades de pré-visualização na Pesquisa Cognitiva Azure

Este artigo lista as funcionalidades atualmente em pré-visualização. As funcionalidades que transitam da pré-visualização para a disponibilidade geral são removidas desta lista. Pode consultar [atualizações](https://azure.microsoft.com/updates/?product=search) de serviço ou [O que há de novo](whats-new.md) para anúncios relativos à disponibilidade geral.

Embora algumas funcionalidades de pré-visualização possam estar disponíveis no portal e no .NET SDK, o REST API tem sempre funcionalidades de pré-visualização. A versão API de pré-visualização atual é `2019-05-06-Preview`.

> [!IMPORTANT]
> A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features-in-public-preview"></a>Características na pré-visualização pública

+ [O Lookup de EntidadePersonalizada (pré-visualização)](cognitive-search-skill-custom-entity-lookup.md ) procura texto a partir de uma lista personalizada e definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência fuzzy que pode ser aplicado para encontrar fósforos que são semelhantes, mas não exatamente. 

+ [PiI Deteção (pré-visualização)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de a mascarar desse texto de várias formas.

+ [O enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona cacheching a um pipeline de enriquecimento, permitindo-lhe reutilizar a saída existente se uma modificação direcionada, como uma atualização para um skillset ou outro objeto, não alterar o conteúdo. O Caching aplica-se somente a documentos aprimorados produzidos por um configurador de qualificações.

+ [Cosmos DB indexador](search-howto-index-cosmosdb.md) dá suporte à API do MongoDB (versão prévia), API Gremlin (versão prévia) e API do Cassandra (versão prévia).

+ [Azure data Lake Storage Gen2 indexador (versão prévia)](search-howto-index-azure-data-lake-storage.md) pode indexar conteúdo e metadados de data Lake Storage Gen2.

+ A loja de [conhecimento (pré-visualização)](knowledge-store-concept-intro.md) é um novo destino de um oleoduto de enriquecimento baseado em IA. A estrutura de dados física existe no armazenamento de BLOBs do Azure e no armazenamento de tabelas do Azure, e é criada e populada quando você executa um indexador que tem um associador cognitiva. A definição de uma loja de conhecimento é especificada em uma definição de contratação de habilidades. Na definição da loja de conhecimento, você controla as estruturas físicas de seus dados por meio de elementos de *projeção* que determinam como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

+ [moreLikeThis consulta parameter (pré-visualização)](search-more-like-this.md) encontra documentos relevantes para um documento específico. Este recurso está em versões anteriores. 

## <a name="earlier-preview-features"></a>Recursos anteriores de visualização

As funcionalidades anunciadas em pré-visualizações anteriores, caso não tenham transitado para a disponibilidade geral, ainda estão em pré-visualização pública. Se você estiver chamando uma API com uma versão de API de visualização anterior, poderá continuar a usar essa versão ou alternar para `2019-05-06-Preview` sem alterações no comportamento esperado.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de visualização

As visualizações mais antigas ainda são operacionais, mas tornam-se obsoletas ao longo do tempo. Se seu código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, essas chamadas ainda são válidas. No entanto, somente a versão de visualização mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão da API de visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de Pesquisa Cognitiva do Azure está disponível em várias versões. Para obter mais informações, consulte [versões de API](search-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)