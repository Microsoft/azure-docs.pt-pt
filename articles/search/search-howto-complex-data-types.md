---
title: Como modelar tipos de dados complexos-Azure Search
description: Estruturas de dados aninhadas ou hierárquicas podem ser modeladas em um índice de Azure Search usando tipos de dados complexType e Collections.
author: brjohnstmsft
manager: nitinme
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: b9c9b35adc0dde032723c3c60adedf5b2e7b4cb6
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183209"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como modelar tipos de dados complexos no Azure Search

Os conjuntos de valores externos usados para popular um índice de Azure Search podem vir em várias formas. Às vezes, eles incluem subestruturas hierárquicas ou aninhadas. Os exemplos podem incluir vários endereços para um único cliente, várias cores e tamanhos para uma única SKU, vários autores de um único livro e assim por diante. Em termos de modelagem, você pode ver essas estruturas referenciadas como tipos de dados *complexos*, *compostos*, *compostos*ou *agregados* . O termo Azure Search usos para esse conceito é **tipo complexo**. Em Azure Search, os tipos complexos são modelados usando **campos complexos**. Um campo complexo é um campo que contém filhos (subcampos) que podem ser de qualquer tipo de dados, incluindo outros tipos complexos. Isso funciona de forma semelhante à de tipos de dados estruturados em uma linguagem de programação.

Campos complexos representam um único objeto no documento ou uma matriz de objetos, dependendo do tipo de dados. Campos do tipo `Edm.ComplexType` representam objetos únicos, enquanto campos do tipo `Collection(Edm.ComplexType)` representam matrizes de objetos.

Azure Search nativamente dá suporte a tipos e coleções complexos. Esses tipos permitem modelar quase todas as estruturas JSON em um índice de Azure Search. Nas versões anteriores do Azure Search APIs, somente os conjuntos de linhas achatados podiam ser importados. Na versão mais recente, o índice agora pode corresponder mais de acordo com os dados de origem. Em outras palavras, se os dados de origem tiverem tipos complexos, o índice também poderá ter tipos complexos.

Para começar, recomendamos o [conjunto de dados de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), que pode ser carregado no assistente de importação de **dados** no portal do Azure. O assistente detecta tipos complexos na origem e sugere um esquema de índice baseado nas estruturas detectadas.

> [!Note]
> O suporte para tipos complexos está geralmente disponível `api-version=2019-05-06`no. 
>
> Se sua solução de pesquisa tiver sido criada em soluções alternativas anteriores de conjuntos de valores em uma coleção, você deverá alterar o índice para incluir tipos complexos com suporte na versão mais recente da API. Para obter mais informações sobre como atualizar versões de API, consulte [atualizar para a versão mais recente da API REST](search-api-migration.md) ou [atualizar para a versão mais recente do SDK do .net](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exemplo de uma estrutura complexa

O documento JSON a seguir é composto de campos simples e campos complexos. Campos complexos, `Address` como e `Rooms`, têm subcampos. `Address`tem um único conjunto de valores para esses subcampos, pois é um único objeto no documento. Por outro lado `Rooms` , o tem vários conjuntos de valores para seus subcampos, um para cada objeto na coleção.

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
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Criando campos complexos

Assim como ocorre com qualquer definição de índice, você pode usar o portal, a [API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)ou o [SDK do .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) para criar um esquema que inclua tipos complexos. 

O exemplo a seguir mostra um esquema de índice JSON com campos simples, coleções e tipos complexos. Observe que, em um tipo complexo, cada subcampo tem um tipo e pode ter atributos, assim como os campos de nível superior. O esquema corresponde aos dados de exemplo acima. `Address`é um campo complexo que não é uma coleção (um hotel tem um endereço). `Rooms`é um campo de coleção complexo (um hotel tem muitas salas).

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

## <a name="updating-complex-fields"></a>Atualizando campos complexos

Todas as [regras](search-howto-reindex.md) de reindexação que se aplicam a campos em geral ainda se aplicam a campos complexos. Reafirmando algumas das principais regras aqui, adicionar um campo não requer uma recompilação de índice, mas a maioria das modificações faz.

### <a name="structural-updates-to-the-definition"></a>Atualizações estruturais para a definição

Você pode adicionar novos subcampos a um campo complexo a qualquer momento sem a necessidade de uma recompilação de índice. Por exemplo, a `Address` `Rooms` adição de "ZipCode" ou "comodidades" é permitida, assim como a adição de um campo de nível superior a um índice. Os documentos existentes têm um valor nulo para novos campos até que você preencha explicitamente esses campos atualizando seus dados.

Observe que, em um tipo complexo, cada subcampo tem um tipo e pode ter atributos, assim como os campos de nível superior

### <a name="data-updates"></a>Atualizações de dados

A atualização de documentos existentes em um índice `upload` com a ação funciona da mesma maneira para campos complexos e simples – todos os campos são substituídos. No entanto `merge` , `mergeOrUpload` (ou quando aplicado a um documento existente) não funciona da mesma em todos os campos. Especificamente, `merge` o não dá suporte a elementos de mesclagem dentro de uma coleção. Essa limitação existe para coleções de tipos primitivos e coleções complexas. Para atualizar uma coleção, você precisará recuperar o valor completo da coleção, fazer alterações e, em seguida, incluir a nova coleção na solicitação da API do índice.

## <a name="searching-complex-fields"></a>Pesquisando campos complexos

As expressões de pesquisa de forma livre funcionam como esperado com tipos complexos. Se qualquer campo pesquisável ou subcampo em qualquer lugar de um documento corresponder, o próprio documento será uma correspondência.

As consultas são mais nuances quando você tem vários termos e operadores, e alguns termos têm nomes de campo especificados, como é possível com a [sintaxe Lucene](query-lucene-syntax.md). Por exemplo, essa consulta tenta corresponder a dois termos, "Portland" e "OR", em relação a dois subcampos do campo de endereço:

    search=Address/City:Portland AND Address/State:OR

Consultas como essa não são correlacionadas para pesquisa de texto completo, ao contrário de filtros. Em filtros, as consultas em subcampos de uma coleção complexa são correlacionadas usando variáveis de intervalo no [ `any` ou `all` ](search-query-odata-collection-operators.md)no. A consulta Lucene acima retorna documentos contendo "Portland, Maine" e "Portland, Oregon", juntamente com outras cidades no Oregon. Isso acontece porque cada cláusula se aplica a todos os valores de seu campo no documento inteiro, portanto, não há um conceito de "subdocumento atual". Para obter mais informações sobre isso, consulte Understanding [OData Collection Filters in Azure Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Selecionando campos complexos

O `$select` parâmetro é usado para escolher quais campos são retornados nos resultados da pesquisa. Para usar esse parâmetro para selecionar subcampos específicos de um campo complexo, inclua o campo pai e o subcampo separados por uma barra (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Os campos devem ser marcados como recuperáveis no índice, se você quiser nos resultados da pesquisa. Somente os campos marcados como recuperáveis podem ser usados em `$select` uma instrução.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrar, facetar e classificar campos complexos

A mesma [sintaxe de caminho OData](query-odata-filter-orderby-syntax.md) usada para filtragem e pesquisas em campo também pode ser usada para facetar, classificar e selecionar campos em uma solicitação de pesquisa. Para tipos complexos, as regras se aplicam que regem quais subcampos podem ser marcados como classificável ou facetable. Para obter mais informações sobre essas regras, consulte a [referência de API CREATE INDEX](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

### <a name="faceting-sub-fields"></a>Subcampos de faceta

Qualquer subcampo pode ser marcado como facetable, a menos que seja do `Edm.GeographyPoint` tipo `Collection(Edm.GeographyPoint)`ou.

As contagens de documentos retornadas nos resultados da faceta são calculadas para o documento pai (um hotel), não para os subdocumentos em uma coleção complexa (salas). Por exemplo, suponha que um hotel tenha 20 salas do tipo "Suite". Dado esse parâmetro `facet=Rooms/Type`de faceta, a contagem de faceta será uma para o Hotel, e não 20 para as salas.

### <a name="sorting-complex-fields"></a>Classificando campos complexos

As operações de classificação se aplicam a documentos (hotéis) e não a subdocumentos (salas). Quando você tem uma coleção de tipos complexos, como salas, é importante perceber que não é possível classificar em salas. Na verdade, você não pode classificar em nenhuma coleção.

As operações de classificação funcionam quando os campos têm um único valor por documento, se o campo é um campo simples ou um subcampo em um tipo complexo. Por exemplo, `Address/City` pode ser classificável, pois há apenas um endereço por Hotel, portanto `$orderby=Address/City` , classificará Hotéis por cidade.

### <a name="filtering-on-complex-fields"></a>Filtrando em campos complexos

Você pode se referir a subcampos de um campo complexo em uma expressão de filtro. Basta usar a mesma [sintaxe de caminho OData](query-odata-filter-orderby-syntax.md) usada para facetar, classificar e selecionar campos. Por exemplo, o seguinte filtro retornará todos os hotéis no Canadá:

    $filter=Address/Country eq 'Canada'

Para filtrar em um campo de coleção complexo, você pode usar uma **expressão lambda** com [ `any` os `all` operadores e](search-query-odata-collection-operators.md). Nesse caso, a **variável de intervalo** da expressão lambda é um objeto com subcampos. Você pode consultar esses subcampos com a sintaxe de caminho OData padrão. Por exemplo, o filtro a seguir retornará todos os hotéis com pelo menos uma sala de luxo e todos os quartos não fumantes:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Assim como acontece com campos simples de nível superior, subcampos simples de campos complexos só podem ser incluídos em filtros se eles tiverem o atributo filtrável `true` definido como na definição do índice. Para obter mais informações, consulte a [referência de API CREATE INDEX](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>Passos seguintes

Experimente o [conjunto de dados de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) no assistente de **importação de dados** . Você precisará do Cosmos DB informações de conexão fornecidas no Leiame para acessar os dados.

Com essas informações em mãos, sua primeira etapa do assistente é criar uma nova fonte de dados Azure Cosmos DB. Além disso, no assistente, quando chegar à página de índice de destino, você verá um índice com tipos complexos. Crie e carregue esse índice e, em seguida, execute consultas para entender a nova estrutura.

> [!div class="nextstepaction"]
> [Início rápido: assistente do portal para importação, indexação e consultas](search-get-started-portal.md)