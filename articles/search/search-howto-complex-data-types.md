---
title: Como modelar tipos de dados complexos
titleSuffix: Azure Cognitive Search
description: Estruturas de dados aninhadas ou hierárquicas podem ser modeladas num índice de Pesquisa Cognitiva Azure utilizando tipos de dados ComplexType e Collections.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: ee1c0957761fc1c8b9ca80477defae8cef044827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91824472"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Como modelar tipos de dados complexos na Pesquisa Cognitiva Azure

Conjuntos de dados externos usados para povoar um índice de Pesquisa Cognitiva Azure podem vir em muitas formas. Por vezes, incluem subestruturas hierárquicas ou aninhadas. Exemplos podem incluir vários endereços para um único cliente, múltiplas cores e tamanhos para um único SKU, múltiplos autores de um único livro, e assim por diante. Em termos de modelação, pode ver estas estruturas referidas como *complexos,* *compostos,* *compostos*ou tipos de dados *agregados.* O termo Azure Cognitive Search usa para este conceito é **tipo complexo.** Na Pesquisa Cognitiva Azure, os tipos complexos são modelados utilizando **campos complexos.** Um campo complexo é um campo que contém crianças (sub-campos) que podem ser de qualquer tipo de dados, incluindo outros tipos complexos. Isto funciona de forma semelhante à estrutura dos tipos de dados numa linguagem de programação.

Os campos complexos representam um único objeto no documento, ou uma matriz de objetos, dependendo do tipo de dados. Os campos de tipo `Edm.ComplexType` representam objetos únicos, enquanto os campos de tipo `Collection(Edm.ComplexType)` representam matrizes de objetos.

A Azure Cognitive Search suporta nativo tipos e coleções complexas. Estes tipos permitem-lhe modelar quase todas as estruturas JSON num índice de Pesquisa Cognitiva Azure. Em versões anteriores de Azure Cognitive Search APIs, apenas conjuntos de linha achatados poderiam ser importados. Na versão mais recente, o seu índice pode agora corresponder mais de perto aos dados de origem. Por outras palavras, se os seus dados de origem tiverem tipos complexos, o seu índice também pode ter tipos complexos.

Para começar, recomendamos o [conjunto de dados do Hotels,](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)que pode carregar no assistente **de dados Import** no portal Azure. O assistente deteta tipos complexos na fonte e sugere um esquema de índice baseado nas estruturas detetadas.

> [!Note]
> O suporte para tipos complexos tornou-se geralmente disponível a partir de `api-version=2019-05-06` . 
>
> Se a sua solução de pesquisa for construída sobre soluções anteriores de conjuntos de dados achatados numa recolha, deverá alterar o seu índice para incluir tipos complexos como suportado na versão API mais recente. Para obter mais informações sobre a atualização das versões API, consulte [upgrade para a versão mais recente da API REST](search-api-migration.md) ou upgrade para a mais recente versão [.NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exemplo de uma estrutura complexa

O seguinte documento JSON é composto por campos simples e campos complexos. Campos complexos, tais como `Address` `Rooms` e, têm sub-campos. `Address` tem um único conjunto de valores para esses sub-campos, uma vez que é um único objeto no documento. Em contraste, `Rooms` tem vários conjuntos de valores para os seus sub-campos, um para cada objeto na coleção.


```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Tags": ["Free wifi", "on-site parking", "indoor pool", "continental breakfast"]
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "RoomNumber": 1105,
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    }
    . . .
  ]
}
```

<um nome="indexação-complexo-tipos></a>

## <a name="indexing-complex-types"></a>Indexação de tipos complexos

Durante a indexação, você pode ter um máximo de 3000 elementos em todas as coleções complexas dentro de um único documento. Um elemento de uma coleção complexa é um membro dessa coleção, por isso, no caso dos Quartos (a única coleção complexa no exemplo do Hotel), cada quarto é um elemento. No exemplo acima, se o "Motel Secret Point" tivesse 500 quartos, o documento do hotel teria 500 elementos de quarto. Para coleções complexas aninhadas, cada elemento aninhado também é contado, além do elemento exterior (progenitor).

Este limite aplica-se apenas a coleções complexas, e não a tipos complexos (como Address) ou coleções de cordas (como Tags).

## <a name="creating-complex-fields"></a>Criação de campos complexos

Como em qualquer definição de índice, pode utilizar o portal, [REST API,](/rest/api/searchservice/create-index)ou [.NET SDK](/dotnet/api/microsoft.azure.search.models.index) para criar um esquema que inclua tipos complexos. 

O exemplo a seguir mostra um esquema de índice JSON com campos simples, coleções e tipos complexos. Note que dentro de um tipo complexo, cada sub-campo tem um tipo e pode ter atributos, tal como os campos de alto nível têm. O esquema corresponde aos dados de exemplo acima. `Address` é um campo complexo que não é uma coleção (um hotel tem uma morada). `Rooms` é um campo de recolha complexo (um hotel tem muitos quartos).

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

Todas as [regras de reindexão](search-howto-reindex.md) aplicáveis aos campos em geral ainda se aplicam a campos complexos. Restaurar algumas das principais regras aqui, adicionar um campo a um tipo complexo não requer uma reconstrução de índice, mas a maioria das modificações sim.

### <a name="structural-updates-to-the-definition"></a>Atualizações estruturais da definição

Pode adicionar novos sub-campos a um campo complexo a qualquer momento sem a necessidade de uma reconstrução de índices. Por exemplo, é permitido adicionar "ZipCode" `Address` ou "Amenities", `Rooms` tal como adicionar um campo de alto nível a um índice. Os documentos existentes têm um valor nulo para novos campos até que você povoe explicitamente esses campos atualizando os seus dados.

Note que dentro de um tipo complexo, cada sub-campo tem um tipo e pode ter atributos, assim como os campos de alto nível fazem

### <a name="data-updates"></a>Atualizações de dados

A atualização dos documentos existentes num índice com a `upload` ação funciona da mesma forma para campos complexos e simples- todos os campos são substituídos. No entanto, `merge` (ou `mergeOrUpload` quando aplicado a um documento existente) não funciona da mesma forma em todos os campos. Especificamente, `merge` não suporta a fusão de elementos dentro de uma coleção. Esta limitação existe para coleções de tipos primitivos e coleções complexas. Para atualizar uma coleção, terá de recuperar o valor total da recolha, fazer alterações e, em seguida, incluir a nova coleção no pedido de API indexar.

## <a name="searching-complex-fields"></a>Pesquisa de campos complexos

Expressões de pesquisa em forma livre funcionam como esperado com tipos complexos. Se algum campo pes pescê-lo ou sub-campo em qualquer lugar de um documento corresponder, então o documento em si é uma correspondência.

As consultas ficam mais matizadas quando se tem vários termos e operadores, e alguns termos têm nomes de campo especificados, como é possível com a [sintaxe Lucene.](query-lucene-syntax.md) Por exemplo, esta consulta tenta corresponder a dois termos, "Portland" e "OR", contra dois sub-campos do campo Address:

> `search=Address/City:Portland AND Address/State:OR`

Consultas como esta não estão *relacionadas com* a pesquisa de texto completo, ao contrário dos filtros. Nos filtros, as consultas sobre sub-campos de uma coleção complexa são correlacionadas usando variáveis de gama em [ `any` ou `all` ](search-query-odata-collection-operators.md). A consulta de Lucene acima devolve documentos que contêm "Portland, Maine" e "Portland, Oregon", juntamente com outras cidades do Oregon. Isto acontece porque cada cláusula se aplica a todos os valores do seu campo em todo o documento, pelo que não existe noção de um "sub-documento atual". Para obter mais informações sobre este ponto, consulte [os filtros de recolha Understanding OData na Pesquisa Cognitiva Azure.](search-query-understand-collection-filters.md)

## <a name="selecting-complex-fields"></a>Selecionando campos complexos

O `$select` parâmetro é usado para escolher quais os campos devolvidos nos resultados de pesquisa. Para utilizar este parâmetro para selecionar sub-campos específicos de um campo complexo, inclua o campo-mãe e o sub-campo separados por um corte `/` ().

> `$select=HotelName, Address/City, Rooms/BaseRate`

Os campos devem ser marcados como Retrieváveis no índice se os quiser nos resultados de pesquisa. Apenas os campos marcados como Retrievable podem ser utilizados em `$select` comunicado.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrar, faceta e classificar campos complexos

A mesma [sintaxe do caminho OData](query-odata-filter-orderby-syntax.md) utilizada para filtragem e pesquisas de campo também pode ser usada para enfrentar, classificar e selecionar campos num pedido de pesquisa. Para tipos complexos, aplicam-se regras que regem quais os sub-campos que podem ser marcados como tributáveis ou facetable. Para obter mais informações sobre estas regras, consulte a [referência API do Índice de Criação](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Sub-campos de rosto

Qualquer sub-campo pode ser marcado como faceta, a menos que seja do tipo `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)` . .

As contagens de documento devolvidas nas facetas são calculadas para o documento-mãe (um hotel), e não para os sub-documentos numa coleção complexa (quartos). Por exemplo, suponha que um hotel tenha 20 quartos de tipo "suite". Dado este parâmetro `facet=Rooms/Type` faceta, a contagem de facetas será uma para o hotel, não 20 para os quartos.

### <a name="sorting-complex-fields"></a>Separar campos complexos

As operações de classificação aplicam-se a documentos (Hotéis) e não subd documentos (Quartos). Quando você tem uma coleção de tipo complexo, como quartos, é importante perceber que você não pode classificar em quartos em tudo. Na verdade, não se pode ordenar nenhuma coleção.

Ordenar operações funcionam quando os campos têm um único valor por documento, se o campo é um campo simples, ou um sub-campo em um tipo complexo. Por exemplo, `Address/City` é permitido ser ordenado porque há apenas um endereço por hotel, assim irá classificar `$orderby=Address/City` hotéis por cidade.

### <a name="filtering-on-complex-fields"></a>Filtragem em campos complexos

Pode consultar os sub-campos de um campo complexo numa expressão de filtro. Basta utilizar a mesma sintaxe do [caminho OData](query-odata-filter-orderby-syntax.md) que é usada para enfrentar, classificar e selecionar campos. Por exemplo, o seguinte filtro devolverá todos os hotéis no Canadá:

> `$filter=Address/Country eq 'Canada'`

Para filtrar num campo de recolha complexo, pode utilizar uma **expressão lambda** com os [ `any` operadores e `all` operadores.](search-query-odata-collection-operators.md) Nesse caso, a **variável** de alcance da expressão lambda é um objeto com sub-campos. Pode consultar esses sub-campos com a sintaxe padrão do caminho OData. Por exemplo, o seguinte filtro devolverá todos os hotéis com pelo menos um quarto de luxo e todos os quartos para não fumadores:

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

Tal como acontece com os campos simples de alto nível, os subtis simples de campos complexos só podem ser incluídos em filtros se tiverem o atributo **filtrado** definido `true` na definição de índice. Para obter mais informações, consulte a [referência API do Índice de Criação](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Passos seguintes

Experimente o [conjunto de dados do Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) no assistente de **dados de importação.** Você precisará da informação de ligação cosmos DB fornecida na leitura para aceder aos dados.

Com essa informação na mão, o seu primeiro passo no assistente é criar uma nova fonte de dados DB Azure Cosmos. Mais à frente no assistente, quando chegar à página do índice alvo, verá um índice com tipos complexos. Crie e carregue este índice e, em seguida, execute consultas para entender a nova estrutura.

> [!div class="nextstepaction"]
> [Quickstart: assistente do portal para importação, indexação e consultas](search-get-started-portal.md)
