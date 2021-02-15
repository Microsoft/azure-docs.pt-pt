---
title: Lista de funcionalidades de pré-visualização
titleSuffix: Azure Cognitive Search
description: As funcionalidades de pré-visualização são lançadas para que os clientes possam fornecer feedback sobre o seu design e utilidade. Este artigo é uma lista completa de todas as funcionalidades atualmente em pré-visualização.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: bf2ff2fc20d3581a297881fbd9e6db15d825814d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519002"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funcionalidades de pré-visualização em Azure Cognitive Search

Este artigo é uma lista completa de todas as funcionalidades que estão em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As funcionalidades de pré-visualização de que a transição para a disponibilidade geral são removidas desta lista. Se uma funcionalidade não estiver listada abaixo, pode presumir que está geralmente disponível. Para anúncios sobre disponibilidade geral, consulte [Atualizações de Serviço](https://azure.microsoft.com/updates/?product=search) ou [Novidades](whats-new.md).

|Recurso&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categoria | Descrição | Disponibilidade  |
|---------|------------------|-------------|---------------|
| [**Habilidade de Aprendizagem automática Azure (AML)**](cognitive-search-aml-skill.md) | Melhoramento de IA| Um novo tipo de habilidade para integrar um ponto final inferencing da Azure Machine Learning. Começa com [este tutorial.](cognitive-search-tutorial-aml-custom-skill.md) | Use [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) ou 2019-05-06-Preview. Também disponível no portal, em design skillset, assumindo que os serviços de Pesquisa Cognitiva e Azure ML são implantados na mesma subscrição. |
| [**featuresOde parâmetro**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Relevância (pontuação) | Expansão da pontuação de relevância para incluir detalhes: por pontuação de semelhança de campo, por frequência de período de campo, e por número de campo de fichas únicas correspondidas. Pode consumir estes pontos de dados em [soluções de pontuação personalizadas.](https://github.com/Azure-Samples/search-ranking-tutorial) | Adicione este parâmetro de consulta utilizando [Documentos de Pesquisa (REST)](/rest/api/searchservice/preview-api/search-documents) com versão api=2020-06-30-Preview ou 2019-05-06-Preview. |
| [**Sessões de Debug**](cognitive-search-debug-session.md) | Portal, Enriquecimento de IA (skillset) | Um editor de skillset em sessão usado para investigar e resolver problemas com um skillset. As correções aplicadas durante uma sessão de depurar podem ser guardadas para um skillset no serviço. | Apenas portal, utilizando links de página média na página 'Vista Geral' para abrir uma sessão de depuração. |
| [**Mancha nativa soft delete**](search-howto-index-changed-deleted-blobs.md) | Indexantes, bolhas Azure| O indexante de armazenamento Azure Blob na Azure Cognitive Search reconhecerá as bolhas que estão em estado de eliminação suave e removerá o documento de pesquisa correspondente durante a indexação. | Adicione esta definição de configuração usando [Criar Indexer (REST)](/rest/api/searchservice/create-indexer) com versão api=2020-06-30-Preview ou versão api=2019-05-06-Preview. |
| [**Competência de procura de entidade personalizada**](cognitive-search-skill-custom-entity-lookup.md ) | Enriquecimento de IA (skillset) | Uma habilidade cognitiva que procura texto a partir de uma lista personalizada, definida pelo utilizador de palavras e frases. Utilizando esta lista, rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência difusa que pode ser aplicada para encontrar fósforos que são semelhantes, mas não exatamente exatos. | Consulte esta habilidade de pré-visualização utilizando o editor skillset no portal ou [Create Skillset (REST)](/rest/api/searchservice/create-skillset) com versão api=2020-06-30-Preview ou api-versão=2019-05-06-Preview. |
| [**Competência de deteção pii**](cognitive-search-skill-pii-detection.md) | Enriquecimento de IA (skillset) | Uma habilidade cognitiva usada durante a indexação que extrai informações pessoais de um texto de entrada e lhe dá a opção de mascar a partir desse texto de várias maneiras. | Consulte esta habilidade de pré-visualização utilizando o editor skillset no portal ou [Create Skillset (REST)](/rest/api/searchservice/create-skillset) com versão api=2020-06-30-Preview ou api-versão=2019-05-06-Preview. |
| [**Enriquecimento incremental**](cognitive-search-incremental-indexing-conceptual.md) | Configuração do indexante| Adiciona o caching a um pipeline de enriquecimento, permitindo-lhe reutilizar a saída existente se uma modificação direcionada, como uma atualização a um skillset ou outro objeto, não alterar o conteúdo. O caching aplica-se apenas a documentos enriquecidos produzidos por um skillset.| Adicione esta definição de configuração usando [Criar Indexer (REST)](/rest/api/searchservice/create-indexer) com versão api=2020-06-30-Preview ou versão api=2019-05-06-Preview. |
| [**Indexador Cosmos DB: MongoDB API, Gremlin API, Cassandra API**](search-howto-index-cosmosdb.md) | Fonte de dados indexante | Para cosmos DB, a SQL API está geralmente disponível, mas MongoDB, Gremlin e Cassandra APIs estão em pré-visualização. | Apenas para Gremlin e Cassandra, [inscreva-se primeiro](https://aka.ms/azure-cognitive-search/indexer-preview) para que o suporte possa ser ativado para a sua subscrição no backend. As fontes de dados mongoDB podem ser configuradas no portal. Caso contrário, a configuração da fonte de dados para as três APIs é suportada utilizando [criar fonte de dados (REST)](/rest/api/searchservice/create-data-source) com versão api=2020-06-30-Preview ou versão api=2019-05-06-Preview. |
|  [**Indexador Azure Data Lake Storage Gen2**](search-howto-index-azure-data-lake-storage.md) | Fonte de dados indexante | Indexe conteúdo e metadados da Data Lake Storage Gen2.| É necessário [inscrever-se](https://aka.ms/azure-cognitive-search/indexer-preview) para que o suporte possa ser ativado para a sua subscrição no backend. Aceda a esta fonte de dados utilizando Criar Fonte de [Dados (REST)](/rest/api/searchservice/create-data-source) com versão api=2020-06-30-Preview ou versão api=2019-05-06-Preview. |
| [**moreLikeThis**](search-more-like-this.md) | Consulta | Encontra documentos relevantes para um documento específico. Esta funcionalidade já esteve em pré-visualizações anteriores. | Adicione este parâmetro de consulta em chamadas [de Documentos de Busca (REST)](/rest/api/searchservice/search-documents) com versão api=2020-06-30-Preview, 2019-05-06-Preview, 2016-09-01-Preview ou 2017-11-11-Preview. |

## <a name="how-to-call-a-preview-rest-api"></a>Como chamar uma pré-visualização REST API

A Azure Cognitive Search sempre pré-lança funcionalidades experimentais através da API REST primeiro, depois através de versões pré-lançamento do .NET SDK.

As funcionalidades de pré-visualização estão disponíveis para testes e experimentação, com o objetivo de recolher feedback sobre o design e implementação de funcionalidades. Por esta razão, as funcionalidades de pré-visualização podem mudar ao longo do tempo, possivelmente de formas que rompem a retrocompatibilidade. Isto contrasta com as características de uma versão GA, que são estáveis e pouco prováveis de mudar, com exceção de pequenas correções e melhorias compatíveis com retro-retrocessições. Além disso, as funcionalidades de pré-visualização nem sempre o tornam num lançamento de GA.

Embora algumas funcionalidades de pré-visualização possam estar disponíveis no portal e .NET SDK, a API REST tem sempre funcionalidades de pré-visualização.

+ Para operações de pesquisa, [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) é a versão atual de pré-visualização.

+ Para operações de gestão, [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) é a versão atual de pré-visualização.

As pré-visualizações mais antigas ainda estão operacionais, mas ficam velhas com o tempo. Se o seu código ligar `api-version=2019-05-06-Preview` `api-version=2016-09-01-Preview` `api-version=2017-11-11-Preview` ou, essas chamadas ainda forem válidas. No entanto, apenas a versão de pré-visualização mais recente é atualizada com melhorias.

A sintaxe de exemplo a seguir ilustra uma chamada para a versão API de pré-visualização.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

O serviço Azure Cognitive Search está disponível em várias versões. Para mais informações, consulte as [versões API](search-api-versions.md).

## <a name="next-steps"></a>Passos seguintes

Reveja a documentação de referência da API de pré-visualização de pré-visualização de pesquisa. Se encontrar problemas, peça-nos ajuda no [Stack Overflow](https://stackoverflow.com/) ou [no suporte de contacto.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Referência de API do serviço de pesquisa REST (Pré-visualização)](/rest/api/searchservice/index-preview)