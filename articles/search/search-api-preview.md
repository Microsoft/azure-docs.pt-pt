---
title: Funcionalidades de pré-visualização na REST API
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API Versão 2019-05-06-Preview inclui características experimentais, como loja de conhecimento e caching indexante para enriquecimento incremental.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 3fa67f6961b146d1dc7f5a4d1780e4060f1fdedc
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85512691"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funcionalidades de pré-visualização em Azure Cognitive Search

Este artigo lista funcionalidades atualmente em pré-visualização. As funcionalidades que a transição da pré-visualização para a disponibilidade geral são removidas desta lista. Pode consultar [as Atualizações](https://azure.microsoft.com/updates/?product=search) de Serviço ou [o Que Há De Novo](whats-new.md) para anúncios sobre disponibilidade geral.

Embora algumas funcionalidades de pré-visualização possam estar disponíveis no portal e .NET SDK, a API REST tem sempre funcionalidades de pré-visualização.

+ Para operações de pesquisa, [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) é a versão atual de pré-visualização.
+ Para operações de gestão, [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) é a versão atual de pré-visualização.

> [!IMPORTANT]
> A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Características de enriquecimento de IA

Explore as mais recentes melhorias para o enriquecimento de IA através da [pré-visualização Search API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Habilidade de procura de entidade personalizada (pré-visualização)](cognitive-search-skill-custom-entity-lookup.md ) | Uma habilidade cognitiva que procura texto a partir de uma lista personalizada, definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência difusa que pode ser aplicada para encontrar fósforos que são semelhantes, mas não exatamente exatos. | 
| [Habilidade de deteção pii (pré-visualização)](cognitive-search-skill-pii-detection.md) | Uma habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de mascar a partir desse texto de várias maneiras.| 
| [Enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md) | Adiciona o caching a um pipeline de enriquecimento, permitindo-lhe reutilizar a saída existente se uma modificação direcionada, como uma atualização a um skillset ou outro objeto, não alterar o conteúdo. O caching aplica-se apenas a documentos enriquecidos produzidos por um skillset.| 
| [Arquivo de dados de conhecimento (pré-visualização)](knowledge-store-concept-intro.md) | Um novo destino de um oleoduto de enriquecimento baseado em IA. A estrutura de dados físicos existe no armazenamento de Azure Blob e no armazenamento da Tabela Azure, e é criada e povoada quando você corre um indexante que tem uma habilidade cognitiva anexada. A definição de uma loja de conhecimento em si é especificada dentro de uma definição de skillset. Dentro da definição de loja de conhecimento, você controla as estruturas físicas dos seus dados através de elementos de *projeção* que determinam como os dados são moldados, se os dados são armazenados no armazenamento de mesa ou no armazenamento blob, e se existem múltiplas vistas.| 
| [Habilidade AML (pré-visualização)](cognitive-search-aml-skill.md) | Uma habilidade personalizada construída em Azure Machine Learning (AML) para enriquecer documentos durante a indexação. A habilidade Azure ML facilita a descoberta de habilidades, a autenticação e o mapeamento de esquemas.|

## <a name="indexing-and-query-features"></a>Características de indexação e consulta

As funcionalidades de pré-visualização do Indexer estão disponíveis na pré-visualização Search API. 

|||
|-|-|
| [Indexador do Cosmos DB](search-howto-index-cosmosdb.md) | Suporte para a MongoDB API (pré-visualização), Gremlin API (pré-visualização) e Cassandra API (pré-visualização) tipos de API. | 
|  [Indexante Azure Data Lake Storage Gen2 (pré-visualização)](search-howto-index-azure-data-lake-storage.md) | Indexe conteúdo e metadados da Data Lake Storage Gen2.| 
| [maisLikeThis parâmetro de consulta (pré-visualização)](search-more-like-this.md) | Encontra documentos relevantes para um documento específico. Esta funcionalidade já esteve em pré-visualizações anteriores. | 

## <a name="management-features"></a>Recursos de gestão

|||
|-|-|
| [Suporte private Endpoint](service-create-private-endpoint.md) | Pode criar uma rede virtual com um cliente seguro (como uma máquina virtual) e, em seguida, criar um serviço de pesquisa que utilize o Private Endpoint. |
| Restrição de acesso IP | Utilizando [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) a API Management REST, pode criar um serviço que tenha restrições sobre as quais os endereços IP são permitidos de acesso. |

## <a name="earlier-preview-features"></a>Funcionalidades de pré-visualização anteriores

As funcionalidades anunciadas em pré-visualizações anteriores, caso não tenham transitado para a disponibilidade geral, ainda estão em pré-visualização pública. Se estiver a ligar para uma API com uma versão api de pré-visualização anterior, pode continuar a usar essa versão ou mudar `2019-05-06-Preview` sem alterações ao comportamento esperado.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de pré-visualização

As pré-visualizações mais antigas ainda estão operacionais, mas ficam velhas com o tempo. Se o seu código ligar `api-version=2016-09-01-Preview` `api-version=2017-11-11-Preview` ou, essas chamadas ainda são válidas. No entanto, apenas a versão de pré-visualização mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão API de pré-visualização.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço Azure Cognitive Search está disponível em várias versões. Para mais informações, consulte as [versões API](search-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

Reveja a documentação de referência da API Search REST. Se encontrar problemas, peça-nos ajuda no [StackOverflow](https://stackoverflow.com/) ou [suporte de contacto.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Referência de API do serviço de pesquisa REST](https://docs.microsoft.com/rest/api/searchservice/)