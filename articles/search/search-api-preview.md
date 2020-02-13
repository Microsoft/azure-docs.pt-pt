---
title: Funcionalidades de pré-visualização em REST API
titleSuffix: Azure Cognitive Search
description: O serviço de pesquisa cognitiva Azure REST API Versão 2019-05-06-Preview inclui funcionalidades experimentais como a loja de conhecimento e o encado do indexante para enriquecimento incremental.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162281"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funcionalidades de pré-visualização na Pesquisa Cognitiva Azure

Este artigo lista as funcionalidades atualmente em pré-visualização. As funcionalidades que transitam da pré-visualização para a disponibilidade geral são removidas desta lista. Pode consultar [atualizações](https://azure.microsoft.com/updates/?product=search) de serviço ou [O que há de novo](whats-new.md) para anúncios relativos à disponibilidade geral.

Embora algumas funcionalidades de pré-visualização possam estar disponíveis no portal e no .NET SDK, o REST API tem sempre funcionalidades de pré-visualização.

+ Para operações de pesquisa, [ **`2019-05-06-Preview`** ](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) é a versão de pré-visualização atual.
+ Para operações de gestão, [ **`2019-10-01-Preview`** ](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) é a versão de pré-visualização atual.

> [!IMPORTANT]
> A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Características de enriquecimento de IA

Explore as mais recentes melhorias no enriquecimento de IA através da [Pré-visualização Search API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Habilidade de procura de entidade personalizada (pré-visualização)](cognitive-search-skill-custom-entity-lookup.md ) | Uma habilidade cognitiva que procura texto a partir de uma lista personalizada e definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência fuzzy que pode ser aplicado para encontrar fósforos que são semelhantes, mas não exatamente. | 
| [Habilidade de deteção PII (pré-visualização)](cognitive-search-skill-pii-detection.md) | Uma habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de a mascarar desse texto de várias maneiras.| 
| [Enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) | Adiciona cache a um pipeline de enriquecimento, permitindo-lhe reutilizar a saída existente se uma modificação direcionada, como uma atualização a um skillset ou outro objeto, não alterar o conteúdo. O caching aplica-se apenas a documentos enriquecidos produzidos por uma habilidade.| 
| [Loja de conhecimento (pré-visualização)](knowledge-store-concept-intro.md) | Um novo destino de um oleoduto de enriquecimento baseado em IA. A estrutura de dados físicos existe no armazenamento de Azure Blob e no armazenamento da mesa Azure, e é criada e povoada quando executa um indexante que tem uma habilidade cognitiva anexada. A definição de uma loja de conhecimentos em si é especificada dentro de uma definição de skillset. Dentro da definição da loja de conhecimento, você controla as estruturas físicas dos seus dados através de elementos de *projeção* que determinam como os dados são moldados, se os dados são armazenados em armazenamento de tabela ou armazenamento blob, e se existem múltiplas vistas.| 

## <a name="indexing-and-query-features"></a>Características de indexação e consulta

As funcionalidades de pré-visualização do Indexer estão disponíveis na Pré-visualização Search API. 

|||
|-|-|
| [Indexador Cosmos DB](search-howto-index-cosmosdb.md) | Suporte para mongoDB API (pré-visualização), Gremlin API (pré-visualização) e Cassandra API (pré-visualização) tipos de API. | 
|  [Indexador de armazenamento de lagos Azure Data Gen2 (pré-visualização)](search-howto-index-azure-data-lake-storage.md) | Conteúdo de índice e metadados do Data Lake Storage Gen2.| 
| [maisLikeEste parâmetro de consulta (pré-visualização)](search-more-like-this.md) | Encontra documentos relevantes para um documento específico. Esta funcionalidade já esteve em pré-visualizações anteriores. | 

## <a name="management-features"></a>Características de gestão

|||
|-|-|
| [Suporte private Endpoint](service-create-private-endpoint.md) | Pode criar uma rede virtual com um cliente seguro (como uma máquina virtual) e, em seguida, criar um serviço de pesquisa que utilize o Private Endpoint. |
| Restrição de acesso IP | Utilizando [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) da API management REST, pode criar um serviço que tenha restrições sobre as quais os endereços IP são permitidos de acesso. |

## <a name="earlier-preview-features"></a>Funcionalidades de pré-visualização anteriores

As funcionalidades anunciadas em pré-visualizações anteriores, caso não tenham transitado para a disponibilidade geral, ainda estão em pré-visualização pública. Se estiver a chamar uma API com uma versão api de pré-visualização anterior, pode continuar a usar essa versão ou mudar para `2019-05-06-Preview` sem alterações ao comportamento esperado.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de pré-visualização

As pré-visualizações mais antigas ainda estão operacionais, mas tornam-se velhas com o tempo. Se o seu código ligar `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, essas chamadas ainda são válidas. No entanto, apenas a versão de pré-visualização mais recente é atualizada com melhorias. 

O seguinte exemplo de sintaxe ilustra uma chamada para a versão API de pré-visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de Pesquisa Cognitiva Azure está disponível em várias versões. Para mais informações, consulte [versões API](search-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

Reveja a documentação de referência da API do Search REST. Se encontrar problemas, peça-nos ajuda no [StackOverflow](https://stackoverflow.com/) ou [suporte de contato](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Serviço de pesquisa REFERÊNCIA REST API](https://docs.microsoft.com/rest/api/searchservice/)