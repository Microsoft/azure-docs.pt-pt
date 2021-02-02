---
title: Pesquisa rumo às bolhas JSON
titleSuffix: Azure Cognitive Search
description: Crawl Azure JSON blobs para conteúdo de texto usando o indexante Azure Cognitive Search Blob. Os indexantes automatizam a ingestão de dados para fontes de dados selecionadas como o armazenamento de Azure Blob.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259055"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Como indexar as bolhas JSON usando um indexante Blob em Azure Cognitive Search

Este artigo mostra-lhe como [configurar um indexante blob](search-howto-indexing-azure-blob-storage.md) para bolhas que consistem em documentos JSON. As bolhas JSON no armazenamento Azure Blob geralmente assumem qualquer uma destas formas:

+ Um único documento JSON
+ Um documento JSON contendo uma matriz de elementos JSON bem formados
+ Um documento JSON contendo várias entidades, separado por uma nova linha

O indexante blob fornece um **`parsingMode`** parâmetro para otimizar a saída do documento de pesquisa com base nos modos de parsing da estrutura consistem nas seguintes opções:

| parsingMode | Documento JSON | Descrição |
|--------------|-------------|--------------|
| **`json`** | Um por bolha | (predefinição) Parses JSON blobs como um único pedaço de texto. Cada bolha JSON torna-se um único documento de pesquisa. |
| **`jsonArray`** | Múltiplos por bolha | Analisa uma matriz JSON na bolha, onde cada elemento da matriz torna-se um documento de pesquisa separado.  |
| **`jsonLines`** | Múltiplos por bolha | Analisa uma bolha que contém várias entidades JSON (também uma matriz), com elementos individuais separados por uma nova linha. O indexante inicia um novo documento de pesquisa após cada nova linha. |

Para ambos **`jsonArray`** e , você deve rever **`jsonLines`** [Indexing one blob para produzir muitos documentos de pesquisa](search-howto-index-one-to-many-blobs.md) para entender como o indexante blob lida com a desambiguação da chave de documento para vários documentos de pesquisa produzidos a partir da mesma bolha.

Dentro da definição do indexante, pode configurar opcionalmente [mapeamentos](search-indexer-field-mappings.md) de campo para escolher quais as propriedades do documento JSON de origem que são usadas para preencher o seu índice de pesquisa alvo. Por exemplo, ao utilizar o **`jsonArray`** modo de análise, se a matriz existir como uma propriedade de nível inferior, pode definir uma **`document root`** propriedade indicando onde a matriz é colocada dentro da bolha.

As seguintes secções descrevem cada modo com mais detalhes. Se não estiver familiarizado com clientes e conceitos indexantes, consulte [Criar um indexante de pesquisa](search-howto-create-indexers.md). Também deve estar familiarizado com os detalhes da configuração básica do [indexante blob](search-howto-indexing-azure-blob-storage.md), o que não se repete aqui.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Índice de documentos JSON únicos (um por blob)

Por predefinição, os indexantes blob analisam as bolhas JSON como um único pedaço de texto, um documento de pesquisa para cada bolha num recipiente. Se o JSON for estruturado, o documento de pesquisa pode refletir essa estrutura, com elementos individuais representados como campos individuais. Por exemplo, assuma que tem o seguinte documento JSON no armazenamento da Azure Blob:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

O indexante blob analisa o documento JSON num único documento de pesquisa, carregando um índice combinando "texto", "data publicada" e "tags" da fonte contra campos de índice de destino idênticos e dactilografados. Dado um índice com campos de "texto", "datas publicadas" e "tags", o indexante blob pode inferir o mapeamento correto sem um mapeamento de campo presente no pedido.

Embora o comportamento predefinido seja um documento de pesquisa por bolha JSON, definir o modo de análise 'json' altera os mapeamentos internos do campo para o conteúdo, promovendo campos dentro `content` de campos reais no índice de pesquisa. Uma definição de indexante de exemplo para o **`json`** modo de análise pode ser assim:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Tal como acontece com todos os indexantes, se os campos não corresponderem claramente, deverá esperar especificar explicitamente [os mapeamentos](search-indexer-field-mappings.md) de campo individuais, a menos que esteja a utilizar os mapeamentos de campos implícitos disponíveis para conteúdo blob e metadados, conforme descrito na [configuração básica do indexante blob](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>json exemplo (arquivos JSON de hotel único)

O [conjunto de dados de documentos JSON](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) do hotel no GitHub é útil para testar a análise do JSON, onde cada blob representa um ficheiro JSON estruturado. Pode fazer o upload dos ficheiros de dados para o armazenamento da Blob e utilizar o assistente **de dados Import** para avaliar rapidamente como este conteúdo é analisado em documentos de pesquisa individuais. 

O conjunto de dados é composto por cinco bolhas, cada uma contendo um documento hoteleiro com uma recolha de endereços e uma recolha de quartos. O indexante blob deteta ambas as coleções e reflete a estrutura dos documentos de entrada no esquema de índice.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Parse JSON arrays

Em alternativa, pode utilizar a opção de matriz JSON. Esta opção é útil quando as bolhas contêm uma variedade de objetos JSON bem formados, e deseja que cada elemento se torne um documento de pesquisa separado. Utilizando, **`jsonArrays`** a bolha JSON seguinte produz três documentos distintos, cada um com `"id"` e `"text"` campos.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

A **`parameters`** propriedade no indexante contém valores de modo de análise. Para uma matriz JSON, a definição do indexante deve ser semelhante ao exemplo seguinte.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays exemplo (dados da amostra de ensaios clínicos)

Os [ensaios clínicos conjunto de dados JSON](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) no GitHub são úteis para testar a análise da matriz JSON. Pode fazer o upload dos ficheiros de dados para o armazenamento da Blob e utilizar o assistente **de dados Import** para avaliar rapidamente como este conteúdo é analisado em documentos de pesquisa individuais. 

O conjunto de dados é composto por oito blobs, cada uma contendo um conjunto de entidades JSON, num total de 100 entidades. As entidades variam quanto aos campos que são povoados, mas o resultado final é um documento de pesquisa por entidade, de todas as matrizes, em todas as bolhas.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Parsing aninhado conjuntos JSON

Para as matrizes JSON com elementos aninhados, pode especificar uma **`documentRoot`** para indicar uma estrutura de vários níveis. Por exemplo, se as suas bolhas forem assim:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Utilize esta configuração para indexar a matriz contida na `level2` propriedade:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Entidades parse JSON separadas por newlines

Se a sua bolha contiver várias entidades JSON separadas por uma nova linha, e quiser que cada elemento se torne um documento de pesquisa separado, use **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Para as linhas JSON, a definição do indexante deve ser semelhante ao exemplo seguinte.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines exemplo (dados da amostra de caso)

O [conjunto de dados do Caselaw JSON](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) no GitHub é útil para testar a nova análise de linha json. Tal como acontece com outras amostras, pode fazer o upload destes dados para o armazenamento de Blob e utilizar o assistente **de dados Import** para avaliar rapidamente o impacto do modo de análise em blobs individuais.

O conjunto de dados consiste numa bolha contendo 10 entidades JSON separadas por uma nova linha, onde cada entidade descreve um único caso legal. O resultado final é um documento de pesquisa por entidade.

## <a name="map-json-fields-to-search-fields"></a>Mapear campos JSON para pesquisar campos

Os mapeamentos de campo são usados para associar um campo de origem a um campo de destino em situações em que os nomes e tipos de campo não são idênticos. Mas os mapeamentos de campo também podem ser usados para combinar partes de um documento JSON e "levantá-los" em campos de alto nível do documento de pesquisa.

O exemplo que se segue ilustra este cenário. Para obter mais informações sobre mapeamentos de campo em geral, consulte [os mapeamentos de campo](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Assuma um índice de pesquisa com os seguintes campos: `text` `Edm.String` tipo, `date` `Edm.DateTimeOffset` tipo, e `tags` tipo `Collection(Edm.String)` . Note a discrepância entre "data publicada" na fonte e `date` no campo no índice. Para mapear o seu JSON na forma desejada, utilize os seguintes mapeamentos de campo:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Os campos de origem são especificados utilizando a notação [do ponteiro JSON.](https://tools.ietf.org/html/rfc6901) Comece com um corte para a frente para se referir à raiz do seu documento JSON, em seguida, escolha a propriedade desejada (ao nível arbitrário de nidificação) utilizando o caminho separado do corte dianteiro.

Também pode consultar elementos de matriz individuais utilizando um índice baseado em zero. Por exemplo, para escolher o primeiro elemento da matriz "tags" do exemplo acima, utilize um mapeamento de campo como este:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Se **`sourceFieldName`** se refere a uma propriedade que não existe na bolha JSON, esse mapeamento é ignorado sem um erro. Este comportamento permite que a indexação continue para as bolhas JSON que têm um esquema diferente (que é um caso de uso comum). Como não há verificação de validação, verifique cuidadosamente os mapeamentos para que não perca documentos pela razão errada.
>

## <a name="next-steps"></a>Passos seguintes

+ [Configure os indexadores blob](search-howto-indexing-azure-blob-storage.md)
+ [Definir mapeamentos de campo](search-indexer-field-mappings.md)
+ [Descrição geral dos indexadores](search-indexer-overview.md)
+ [Como indexar as bolhas de CSV com um indexador blob](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semi-estruturados a partir do armazenamento do Azure Blob](search-semi-structured-data.md)