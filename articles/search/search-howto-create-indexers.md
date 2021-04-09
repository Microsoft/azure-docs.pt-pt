---
title: Criar um indexador
titleSuffix: Azure Cognitive Search
description: Defina propriedades num indexante para determinar a origem dos dados e os destinos. Pode definir parâmetros para modificar comportamentos de tempo de execução.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 596eca0d73ffc4a590fae9b346658a2c31a1d68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676476"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Criação de indexantes na Pesquisa Cognitiva Azure

Um indexante de pesquisa fornece um fluxo de trabalho automatizado para transferir documentos e conteúdos de uma fonte de dados externa, para um índice de pesquisa no seu serviço de pesquisa. Como originalmente projetado, extrai texto e metadados de uma fonte de dados Azure, serializa documentos em JSON, e transmite os documentos resultantes para um motor de pesquisa para indexação. Desde então, foi estendida para apoiar o [enriquecimento de IA](cognitive-search-concept-intro.md) para o processamento profundo de conteúdos. 

A utilização de indexantes reduz significativamente a quantidade e a complexidade do código que precisa de escrever. Este artigo centra-se na mecânica da criação de um indexante como preparação para um trabalho mais avançado com indexadores e [skillsets específicos de origem.](cognitive-search-working-with-skillsets.md)

## <a name="whats-an-indexer-definition"></a>O que é uma definição indexante?

Os indexantes são usados para indexação baseada em texto que puxa o conteúdo alfanumérico dos campos de origem para campos de índice, ou o processamento baseado em IA que analisa texto indiferenciado para estrutura, ou analisa imagens para texto e informação, adicionando também esse conteúdo a um índice. As seguintes definições de índice são típicas do que pode criar para qualquer um dos cenários.

### <a name="indexers-for-text-content"></a>Indexantes para conteúdo de texto

O objetivo original de um indexante era simplificar o processo complexo de carregamento de um índice, fornecendo um mecanismo de ligação e leitura de texto e conteúdo numérico a partir de campos de uma fonte de dados, serializar esse conteúdo como documentos JSON, e entregar esses documentos ao motor de busca para indexação. Este ainda é um caso de uso primário, e para esta operação, você precisará criar um indexante com as propriedades definidas no exemplo seguinte.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

O **`name`** , e propriedades são **`dataSourceName`** **`targetIndexName`**  necessários, e dependendo da forma como cria o indexante, tanto a fonte de dados como o índice já devem existir no serviço antes de poder executar o indexante. 

A **`parameters`** propriedade modifica comportamentos de tempo de execução, tais como quantos erros aceitar antes de falhar todo o trabalho. Os parâmetros também são como você especificaria comportamentos específicos da fonte. Por exemplo, se a fonte for armazenamento Blob, pode definir um parâmetro que filtra nas extensões de ficheiros: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

A **`field mappings`** propriedade é usada para mapear explicitamente campos de origem para destino se esses campos diferem pelo nome ou tipo. Outras propriedades (não apresentadas), são usadas para [especificar um calendário](search-howto-schedule-indexers.md), criar o indexer em um estado desativado, ou especificar uma chave de [encriptação](search-security-manage-encryption-keys.md) para encriptação suplementar de dados em repouso.

### <a name="indexers-for-ai-indexing"></a>Indexantes para indexação de IA

Como os indexantes são o mecanismo pelo qual um serviço de pesquisa faz pedidos de saída, os indexantes foram alargados para suportar enriquecimentos de IA, adicionando infraestruturas e objetos para implementar este caso de uso.

Todas as propriedades e parâmetros acima aplicáveis aos indexantes que realizam o enriquecimento de IA. As seguintes propriedades são específicas do enriquecimento de IA: **`skillSets`** **`outputFieldMappings`** , , **`cache`** (apenas pré-visualização e REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

O enriquecimento de IA está fora do âmbito deste artigo. Para mais informações, comece com estes artigos: [Enriquecimento de IA,](cognitive-search-concept-intro.md) [Skillsets em Azure Cognitive Search,](cognitive-search-working-with-skillsets.md)e [Create Skillset (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Escolha um cliente indexante e crie o indexador

Quando estiver pronto para criar um indexante num serviço de pesquisa remota, necessitará de um cliente de pesquisa sob a forma de uma ferramenta, como o portal Azure ou o Carteiro, ou código que instantaneamente um cliente indexante. Recomendamos o portal Azure ou ASP DE REST para o desenvolvimento precoce e testes de prova de conceito.

### <a name="permissions"></a>Permissões

Todas as operações relacionadas com indexantes, incluindo pedidos get para o estado ou definições, requerem [uma chave api-administradora](search-security-api-keys.md) sobre o pedido.

### <a name="limits"></a>Limites

Todos os [níveis de serviço limitam](search-limits-quotas-capacity.md#indexer-limits) o número de objetos que pode criar. Se estiver a experimentar no nível Livre, só pode ter 3 objetos de cada tipo e 2 minutos de processamento do indexante (sem incluir o processamento de skillset).

### <a name="use-azure-portal-to-create-an-indexer"></a>Use o portal Azure para criar um indexador

O portal fornece duas opções para criar um indexante: [**assistente de dados de importação**](search-import-data-portal.md) e Novo **Indexer** que fornece campos para especificar uma definição de indexante. O assistente é único na medida em que cria todos os elementos necessários. Outras abordagens requerem que tenha predefinido uma fonte de dados e um índice.

A imagem que se segue mostra onde pode encontrar estas funcionalidades no portal. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="indexante de hotéis" border="true":::

### <a name="use-a-rest-client"></a>Use um cliente REST

Tanto o Código do Carteiro como o Código do Estúdio Visual (com uma extensão para Azure Cognitive Search) podem funcionar como um cliente indexante. Utilizando qualquer uma das ferramentas, pode ligar-se ao seu serviço de pesquisa e enviar pedidos [de Criar Indexer (REST).](/rest/api/searchservice/create-indexer) Existem inúmeros tutoriais e exemplos que demonstram clientes REST para criar objetos. 

Comece com qualquer um destes artigos para aprender sobre cada cliente:

+ [Criar um índice de pesquisa usando REST e Carteiro](search-get-started-rest.md)
+ [Começar com Visual Studio Code e Azure Cognitive Search](search-get-started-vs-code.md)

Consulte as operações do [Indexer (REST)](/rest/api/searchservice/Indexer-operations) para obter ajuda na formulação de pedidos de indexante.

### <a name="use-an-sdk"></a>Use um SDK

Para pesquisa cognitiva, os Azure SDKs implementam funcionalidades geralmente disponíveis. Como tal, pode utilizar qualquer um dos SDKs para criar objetos relacionados com indexante. Todos eles fornecem um **SearchIndexerClient** que tem métodos para criar indexadores e objetos relacionados, incluindo skillsets.

| Azure SDK | Cliente | Exemplos |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Executar o indexador

Um indexante funciona automaticamente quando cria o indexante no serviço. Este é o momento da verdade em que você vai descobrir se existem erros de ligação de fonte de dados, problemas de mapeamento de campo ou problemas de skillset. 

Há várias maneiras de executar um indexante:

+ Envie um pedido HTTP para [Criar Indexador](/rest/api/searchservice/create-indexer) ou [Indexante de Atualização](/rest/api/searchservice/update-indexer) para adicionar ou alterar a definição e executar o indexante.

+ Envie um pedido HTTP para [o Indexante de Execução](/rest/api/searchservice/run-indexer) para executar um indexante sem alterações na definição.

+ Executar um programa que chama métodos SearchIndexerClient para criar, atualizar ou executar.

> [!NOTE]
> Para evitar a execução imediata de um indexante após a criação, inclua **`disabled=true`** na definição do indexante.

Em alternativa, coloque o indexante [num horário](search-howto-schedule-indexers.md) para invocar o processamento a intervalos regulares. 

O processamento programado geralmente coincide com a necessidade de indexação incremental de conteúdo alterado. Mudar a lógica de deteção é uma capacidade que está incorporada nas plataformas de origem. As alterações num recipiente de bolhas são detetadas automaticamente pelo indexante. Para obter orientações sobre a utilização da deteção de alterações noutras fontes de dados, consulte os docs indexantes para fontes de dados específicas:

+ [Base de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2)
+ [Armazenamento de mesa Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="change-detection-and-indexer-state"></a>Alterar estado de deteção e indexante

Os indexantes podem detetar alterações nos dados subjacentes e apenas processar documentos novos ou atualizados em cada execução do indexante. Por exemplo, se o estado do indexante diz que uma corrida foi bem sucedida com `0/0` documentos processados, significa que o indexante não encontrou nenhuma linha nova ou alterada ou bolhas na fonte de dados subjacente.

A forma como um indexante suporta a deteção de alterações varia consoante a fonte de dados:

+ Azure Blob Storage, Azure Table Storage e Azure Data Lake Storage Gen2 carimbam cada atualização de blob ou linha com uma data e hora. Os vários indexantes utilizam esta informação para determinar quais os documentos a atualizar no índice. A deteção de alterações incorporadas significa que um indexante pode reconhecer documentos novos e atualizados, sem necessidade de configuração adicional da sua parte.

+ A Azure SQL e Cosmos DB fornecem funcionalidades de deteção de alterações nas suas plataformas. Pode especificar a política de deteção de alterações na definição de fonte de dados.

Para grandes cargas de indexação, um indexante também acompanha o último documento que processou através de uma "marca de água alta" interna. O marcador nunca é exposto na API, mas internamente o indexante mantém o local onde parou. Quando a indexação retoma, seja através de uma execução programada ou de uma invocação a pedido, o indexante faz referência à marca de água elevada para que possa retomar onde deixou de lado.

Se precisar de limpar a marca de água elevada para voltar a indexar na íntegra, pode utilizar [o Indexer reset](/rest/api/searchservice/reset-indexer). Para uma re indexação seletiva, utilize [competências de reposição](/rest/api/searchservice/preview-api/reset-skills) ou [documentos de reposição](/rest/api/searchservice/preview-api/reset-documents). Através das APIs de reset, pode limpar o estado interno e também lavar a cache se ativar o [enriquecimento incremental](search-howto-incremental-index.md). Para obter mais antecedentes e comparação de cada opção de reset, consulte [indexadores, competências e documentos de execução ou reposição](search-howto-run-reset-indexers.md).

## <a name="know-your-data"></a>Conheça os seus dados

Os indexantes esperam um conjunto de linha tabular, onde cada linha se torna um documento de pesquisa completo ou parcial no índice. Muitas vezes, há uma correspondência de um para um entre uma linha e o documento de pesquisa resultante, onde todos os campos da linha definidos povoam totalmente cada documento. Mas pode usar indexadores para gerar apenas uma parte de um documento, por exemplo, se estiver a usar vários indexadores ou abordagens para construir o índice. 

Para aplainar dados relacionais num conjunto de linhas, deve criar uma vista SQL, ou construir uma consulta que retorne os registos dos pais e dos filhos na mesma linha. Por exemplo, o conjunto de dados de amostras de hotéis incorporados é uma base de dados SQL que tem 50 registos (um para cada hotel), ligados aos registos de quartos numa tabela relacionada. A consulta que achata os dados coletivos num conjunto de linha incorpora toda a informação do quarto em documentos JSON em cada registo do hotel. A informação incorporada da sala é gerada por uma consulta que utiliza uma cláusula **FOR JSON AUTO.** Você pode aprender mais sobre esta técnica em [definir uma consulta que devolve JSON incorporado](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Este é apenas um exemplo; pode encontrar outras abordagens que produzirão o mesmo efeito.

Além dos dados achatados, é importante obter apenas dados pesmáveis. Os dados pesmáveis são alfanuméricos. A Pesquisa Cognitiva não pode pesquisar dados binários em qualquer formato, embora possa extrair e inferir descrições de texto de ficheiros de imagem (ver [enriquecimento de IA)](cognitive-search-concept-intro.md)para criar conteúdo pescável. Da mesma forma, usando o enriquecimento de IA, um grande texto pode ser analisado por modelos de linguagem natural para encontrar estrutura ou informação relevante, gerando novos conteúdos que pode adicionar a um documento de pesquisa.

Dado que os indexantes não resolvem problemas de dados, outras formas de limpeza ou manipulação de dados podem ser necessárias. Para mais informações, consulte a documentação do produto do seu produto de base de [dados Azure.](../index.yml?product=databases)

## <a name="know-your-index"></a>Conheça o seu índice

Lembre-se que os indexantes passam os documentos de pesquisa para o motor de pesquisa para indexação. Assim como os indexantes têm propriedades que determinam o comportamento da execução, um esquema de índice tem propriedades que afetam profundamente a forma como as cordas são indexadas (apenas as cordas são analisadas e tokenizadas). Dependendo das atribuições de analisadores, as cordas indexadas podem ser diferentes das que passaste. Pode avaliar os efeitos dos analisadores utilizando [o Texto de Análise (REST)](/rest/api/searchservice/test-analyzer). Para obter mais informações sobre os analisadores, consulte [os Analisadores para processamento de texto.](search-analyzers.md)

Em termos de como os indexantes interagem com um índice, um indexante apenas verifica nomes e tipos de campo. Não existe nenhum passo de validação que garanta que o conteúdo de entrada está correto para o campo de pesquisa correspondente no índice. Como etapa de verificação, pode executar consultas no índice povoado que devolvem documentos inteiros ou campos selecionados. Para obter mais informações sobre a consulta do conteúdo de um índice, consulte [Criar uma consulta básica.](search-query-create.md)

## <a name="next-steps"></a>Passos seguintes

+ [Indexadores de agenda](search-howto-schedule-indexers.md)
+ [Definir mapeamentos de campo](search-indexer-field-mappings.md)
+ [Monitorizar o estado do indexante](search-howto-monitor-indexers.md)
+ [Ligar utilizando as identidades geridas](search-howto-managed-identities-data-sources.md)