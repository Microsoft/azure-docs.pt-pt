---
title: Como modelar tipos de dados complexos - Azure Search
description: Estruturas de dados hierárquica ou aninhadas podem ser modeladas num índice da Azure Search utilizando o ComplexType e coleções de tipos de dados.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 61f2094449995e26c3d321aaf35deb3d60ff250c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056585"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como tipos de dados complexos de modelo no Azure Search

Conjuntos de dados externos utilizados para preencher um índice da Azure Search podem ter muitas formas. Por vezes, eles incluem substructures hierárquicas ou aninhados. Exemplos podem incluir vários endereços para um único cliente, várias cores e tamanhos para uma SKU único, vários autores de um livro único e assim por diante. Em termos de modelagem, poderá ver essas estruturas denominadas *complexos*, *composta*, *compostos*, ou *agregado* tipos de dados. O termo de pesquisa do Azure utiliza para esse conceito é **tipo complexo**. No Azure Search, tipos complexos são modelados com **campos complexos**. Um complexo é um campo que contém elementos subordinados (secundárias campos), que podem ser de qualquer tipo de dados, incluindo outros tipos complexos. Isso funciona de forma semelhante a como tipos de dados estruturados numa linguagem de programação.

Campos complexos representam a um único objeto no documento, ou uma matriz de objetos, dependendo do tipo de dados. Campos do tipo `Edm.ComplexType` representar objetos únicos, ao mesmo tempo os campos do tipo `Collection(Edm.ComplexType)` representam as matrizes de objetos.

O Azure Search suporte nativo para tipos complexos e coleções. Esses tipos permitem-lhe modelar quase qualquer estrutura JSON num índice da Azure Search. Nas versões anteriores de APIs de pesquisa do Azure, apenas Achatado linha não foi possível importar conjuntos. A versão mais recente, o índice pode agora mais de perto correspondem aos dados de origem. Em outras palavras, se os dados de origem tiverem tipos complexos, o índice pode ter tipos complexos também.

Para começar, recomendamos que o [conjunto de dados de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), que pode carregar no **importar dados** assistente no portal do Azure. O assistente Deteta tipos complexos na origem e sugere um esquema de índice com base nas estruturas de detetado.

> [!Note]
> Suporte para tipos complexos está disponível em geral em `api-version=2019-05-06`. 
>
> Se a sua solução de pesquisa baseia-se a soluções anteriores de conjuntos de dados bidimensional numa coleção, deve alterar o seu índice para incluir tipos complexos como suportado na versão mais recente do API. Para obter mais informações sobre como atualizar versões de API, consulte [atualizar para a versão mais recente da REST API](search-api-migration.md) ou [atualizar para a versão mais recente do SDK do .NET](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exemplo de uma estrutura complexo

O documento JSON seguinte é composto por campos simples e complexos campos. Complexo de campos, tal como `Address` e `Rooms`, ter subcampos. `Address` tem um único conjunto de valores para os campos secundárias, uma vez que é um único objeto no documento. Por outro lado, `Rooms` tem vários conjuntos de valores para seus campos secundárias, um para cada objeto da coleção.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Criar campos complexos

Como com qualquer definição de índice, pode utilizar o portal [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), ou [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) para criar um esquema que inclui tipos complexos. 

O exemplo seguinte mostra um esquema de índice JSON com campos simples, coleções e tipos complexos. Observe que dentro de um tipo complexo, cada campo secundárias tem um tipo e pode ter atributos, fazer campos apenas como nível superior. O esquema corresponde aos dados de exemplo acima. `Address` é um campo complexo que não é uma coleção (um hotel tem um endereço). `Rooms` é um campo de coleção complexa (um hotel tem muitos ambientes).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Atualizar campos complexos

Todos os [reindexação regras](search-howto-reindex.md) que se apliquem a campos em geral ainda se aplicam aos campos complexos. Refazendo algumas das regras principais aqui, adicionar um campo não requer uma recompilação de índice, mas a maioria das modificações faz.

### <a name="structural-updates-to-the-definition"></a>Atualizações estruturais a definição

Pode adicionar novos campos secundária a um campo complexo em qualquer altura, sem a necessidade de uma recompilação de índice. Por exemplo, adicionar "ZipCode" ao `Address` ou "Características" para `Rooms` é permitido, como adicionar um campo de nível superior para um índice. Documentos existentes tem um valor nulo para novos campos até que explicitamente preencha esses campos ao atualizar os seus dados.

Observe que dentro de um tipo complexo, cada campo secundárias tem um tipo e pode ter atributos, fazer campos apenas como nível superior

### <a name="data-updates"></a>Atualizações de dados

Atualização de documentos existentes num índice com o `upload` ação funciona da mesma forma para campos de simples e complexos – todos os campos são substituídos. No entanto, `merge` (ou `mergeOrUpload` quando aplicado a um documento existente) não funcionam da mesma em todos os campos. Especificamente, `merge` não suporta a mesclagem de elementos dentro de uma coleção. Esta limitação existe para coleções de tipos primitivos e coleções complexas. Para atualizar uma coleção, precisa para recuperar o valor de coleção completa, efetuar alterações e, em seguida, inclua a nova coleção no pedido de API de índice.

## <a name="searching-complex-fields"></a>Pesquisar campos complexos

As expressões de pesquisa de forma livre funcionam conforme esperado com tipos complexos. Caso corresponda a qualquer campo pesquisável ou campo secundária em qualquer lugar num documento, o próprio documento é uma correspondência.

Get de consultas mais variada quando tem vários termos e operadores e alguns termos têm nomes de campo especificados, como é possível fazer com o [sintaxe Lucene](query-lucene-syntax.md). Por exemplo, esta consulta tenta corresponder dois termos, "Portland" e "OR", em relação a dois campos secundárias do campo Endereço:

    search=Address/City:Portland AND Address/State:OR

Consultas como desta funcionalidade são *uncorrelated* para pesquisa em texto completo, ao contrário dos filtros. Em filtros, consultas sobre campos de frações de uma coleção complexa são correlacionadas utilizando variáveis de alcance na [ `any` ou `all` ](search-query-odata-collection-operators.md). A consulta de Lucene acima devolve documentos que contenham "Portland, Maine" e "Portland, Oregon", juntamente com outras cidades no Oregon. Isso acontece porque cada cláusula aplica-se a todos os valores do seu campo em todo o documento, portanto, não há nenhum conceito de um "documento secundárias atual". Para obter mais informações sobre isso, consulte [filtros de recolha de noções básicas sobre OData na Azure Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Selecionar campos complexos

O `$select` parâmetro é utilizado para escolher os campos que são devolvidos nos resultados da pesquisa. Para utilizar este parâmetro para selecionar campos específicos de frações de um campo complexo, inclua o campo de principal e secundária campo separados por uma barra (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Campos devem ser marcados como recuperáveis no índice se desejar nos resultados da pesquisa. Apenas os campos marcados como recuperáveis podem ser usados num `$select` instrução.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtro de faceta e campos complexas de ordenação

O mesmo [sintaxe de caminho de OData](query-odata-filter-orderby-syntax.md) utilizado para filtrar e respondidas pesquisas também podem ser utilizadas para facetamento, ordenação e selecionar campos numa solicitação de pesquisa. Para tipos complexos, as regras são aplicadas que regem os campos que secundárias podem ser marcados como ordenáveis ou facetável. Para obter mais informações sobre estas regras, consulte a [referência da API de índice criar](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

### <a name="faceting-sub-fields"></a>Campos de frações de facetamento

Qualquer campo secundárias pode ser marcado como facetável, a menos que seja do tipo `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)`.

As contagens de documentos nos resultados de faceta são calculadas para o documento principal (um hotel), não os documentos secundárias numa coleção complexa (salas). Por exemplo, suponha que um hotel tem 20 salas do tipo "suite". Tendo em conta este parâmetro de faceta `facet=Rooms/Type`, a contagem de faceta será um para o hotel, não 20 para os ambientes.

### <a name="sorting-complex-fields"></a>Campos complexos de ordenação

Operações de classificação se aplica a documentos (hotéis) e não documentos secundárias (salas). Quando tem uma coleção de tipo complexo, como salas, é importante perceber que não é possível ordenar a em salas. Na verdade, não é possível ordenar em qualquer coleção.

Operações de ordenação funcionam quando os campos têm um valor único por documento, se o campo é um campo simples ou um campo secundárias num tipo complexo. Por exemplo, `Address/City` tem permissão para ser ordenável porque existe apenas um endereço por hotel, então, `$orderby=Address/City` classificarão hotéis por cidade.

### <a name="filtering-on-complex-fields"></a>Filtrar em campos complexos

Pode consultar para campos de frações de um campo complexo numa expressão de filtro. Basta usar o mesmo [sintaxe de caminho de OData](query-odata-filter-orderby-syntax.md) que é utilizado para facetamento, ordenação e selecionar campos. Por exemplo, o filtro seguinte irá devolver todos os hotéis no Canadá:

    $filter=Address/Country eq 'Canada'

Para filtrar num campo coleção complexa, pode utilizar um **expressão lambda** com o [ `any` e `all` operadores](search-query-odata-collection-operators.md). Nesse caso, o **variável de alcance** do lambda expressão é um objeto com subcampos. Pode consultar esses campos secundárias com a sintaxe de caminho padrão do OData. Por exemplo, o filtro seguinte irá devolver todos os hotéis com, pelo menos, uma sala deluxe e salas todos os não-fumar:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Como com campos simples de nível superior, campos secundárias simples de campos complexos só podem ser incluídos em filtros se tiverem o **filtrável** atributo definido como `true` na definição do índice. Para obter mais informações, consulte a [referência da API de índice criar](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>Passos Seguintes

Experimente o [conjunto de dados de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) no **importar dados** assistente. Terá das informações de ligação do Cosmos DB fornecidas no Leiame para acessar os dados.

Com essas informações em mãos, o primeiro passo no assistente é criar uma nova origem de dados do Azure Cosmos DB. Ainda mais no assistente, quando chegar à página de índice de destino, poderá ver um índice com tipos complexos. Criar e carregar este índice e, em seguida, execute consultas para compreender a estrutura de novo.

> [!div class="nextstepaction"]
> [Início rápido: Assistente para importação, indexação e consultas do portal](search-get-started-portal.md)