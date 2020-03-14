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
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283059"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Como modelar tipos de dados complexos em Pesquisa Cognitiva Azure

Conjuntos de dados externos usados para povoar um índice de pesquisa cognitiva Azure podem vir em muitas formas. Às vezes incluem subestruturas hierárquicas ou aninhadas. Exemplos podem incluir vários endereços para um único cliente, múltiplas cores e tamanhos para um único SKU, vários autores de um único livro, e assim por diante. Em termos de modelação, pode ver estas estruturas referidas como tipos de dados *complexos,* *compostos,* *compósitos*ou *agregados.* O termo Pesquisa Cognitiva Azure usa para este conceito é **tipo complexo.** Na Pesquisa Cognitiva Azure, os tipos complexos são modelados usando **campos complexos.** Um campo complexo é um campo que contém crianças (sub-campos) que podem ser de qualquer tipo de dados, incluindo outros tipos complexos. Isto funciona de forma semelhante aos tipos de dados estruturados numa linguagem de programação.

Campos complexos representam um único objeto no documento, ou uma série de objetos, dependendo do tipo de dados. Os campos de `Edm.ComplexType` de tipo representam objetos únicos, enquanto os campos de `Collection(Edm.ComplexType)` de tipo representam matrizes de objetos.

A Azure Cognitive Search apoia nativamente tipos e coleções complexos. Estes tipos permitem modelar quase qualquer estrutura JSON num índice de Pesquisa Cognitiva Azure. Em versões anteriores de APIs de pesquisa cognitiva azure, apenas conjuntos de linhas achatados poderiam ser importados. Na versão mais recente, o seu índice pode agora corresponder mais de perto aos dados de origem. Por outras palavras, se os seus dados de origem tiverem tipos complexos, o seu índice também pode ter tipos complexos.

Para começar, recomendamos o conjunto de [dados Hotéis,](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)que pode carregar no assistente de **dados de Importação** no portal Azure. O assistente deteta tipos complexos na fonte e sugere um esquema de índice baseado nas estruturas detetadas.

> [!Note]
> O suporte para tipos complexos está geralmente disponível em `api-version=2019-05-06`. 
>
> Se a sua solução de pesquisa for construída sobre soluções anteriores de conjuntos de dados achatados numa recolha, deverá alterar o seu índice para incluir tipos complexos como suportado na versão Mais recente da API. Para obter mais informações sobre a atualização das versões API, consulte [o Upgrade para a mais recente versão REST API](search-api-migration.md) ou upgrade para a versão mais recente [.NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exemplo de uma estrutura complexa

O seguinte documento JSON é composto por campos simples e campos complexos. Campos complexos, como `Address` e `Rooms`, têm sub-campos. `Address` tem um único conjunto de valores para esses subcampos, uma vez que é um único objeto no documento. Em contraste, `Rooms` tem múltiplos conjuntos de valores para os seus sub-campos, um para cada objeto da coleção.

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

## <a name="creating-complex-fields"></a>Criação de campos complexos

Como em qualquer definição de índice, pode utilizar o portal, [REST API,](https://docs.microsoft.com/rest/api/searchservice/create-index)ou [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) para criar um esquema que inclua tipos complexos. 

O exemplo seguinte mostra um esquema de índice JSON com campos simples, coleções e tipos complexos. Note que dentro de um tipo complexo, cada sub-campo tem um tipo e pode ter atributos, assim como os campos de alto nível têm. O esquema corresponde aos dados de exemplo acima. `Address` é um campo complexo que não é uma coleção (um hotel tem uma morada). `Rooms` é um complexo campo de recolha (um hotel tem muitos quartos).

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

Todas as regras de [reindexação](search-howto-reindex.md) aplicáveis aos campos em geral ainda se aplicam a campos complexos. Reafirmando algumas das principais regras aqui, adicionar um campo não requer uma reconstrução de índice, mas a maioria das modificações o fazem.

### <a name="structural-updates-to-the-definition"></a>Atualizações estruturais da definição

Pode adicionar novos sub-campos a um campo complexo a qualquer momento sem a necessidade de uma reconstrução do índice. Por exemplo, adicionar "ZipCode" a `Address` ou "Comodidades" a `Rooms` é permitido, tal como adicionar um campo de alto nível a um índice. Os documentos existentes têm um valor nulo para novos campos até que você povoe explicitamente esses campos atualizando os seus dados.

Note que dentro de um tipo complexo, cada sub-campo tem um tipo e pode ter atributos, assim como os campos de alto nível fazem

### <a name="data-updates"></a>Atualizações de dados

Atualizar os documentos existentes num índice com a ação `upload` funciona da mesma forma para campos complexos e simples - todos os campos são substituídos. No entanto, `merge` (ou `mergeOrUpload` quando aplicado a um documento existente) não funciona da mesma forma em todos os campos. Especificamente, `merge` não suporta a fusão de elementos dentro de uma coleção. Esta limitação existe para coleções de tipos primitivos e coleções complexas. Para atualizar uma coleção, terá de recuperar o valor total da recolha, fazer alterações e, em seguida, incluir a nova coleção no pedido de API indexado.

## <a name="searching-complex-fields"></a>Procurando campos complexos

Expressões de pesquisa de forma livre funcionam como esperado com tipos complexos. Se qualquer campo ou sub-campo pesquisável em qualquer lugar de um documento corresponder, então o documento em si é compatível.

As consultas ficam mais matizadas quando se tem vários termos e operadores, e alguns termos têm nomes de campo especificados, como é possível com a [sintaxe Lucene.](query-lucene-syntax.md) Por exemplo, esta consulta tenta corresponder a dois termos, "Portland" e "OR", contra dois sub-campos do campo Address:

    search=Address/City:Portland AND Address/State:OR

Consultas como esta não estão *correlacionadas* para pesquisa de texto completo, ao contrário dos filtros. Nos filtros, as consultas sobre sub-campos de uma coleção complexa estão correlacionadas utilizando variáveis de gama em [`any` ou `all`](search-query-odata-collection-operators.md). A consulta lucene acima devolve documentos que contêm "Portland, Maine" e "Portland, Oregon", juntamente com outras cidades do Oregon. Isto acontece porque cada cláusula se aplica a todos os valores do seu campo em todo o documento, pelo que não existe um conceito de "sub-documento atual". Para obter mais informações sobre este caso, consulte [a Understanding OData collection filters in Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Seleção de campos complexos

O parâmetro `$select` é utilizado para escolher quais os campos devolvidos nos resultados da pesquisa. Para utilizar este parâmetro para selecionar subcampos específicos de um campo complexo, inclua o campo-mãe e o subcampo separados por um corte (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Os campos devem ser marcados como Recuperáveis no índice se os quiser nos resultados da pesquisa. Apenas os campos marcados como Recuperáveis podem ser usados numa declaração `$select`.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtro, faceta e ordenar campos complexos

A mesma [sintaxe de percurso OData](query-odata-filter-orderby-syntax.md) utilizada para filtrar e procurar em campo também pode ser usada para enfrentar, separar e selecionar campos num pedido de pesquisa. Para tipos complexos, aplicam-se regras que regem quais os subcampos que podem ser marcados como classificados como classificativos ou facetáveis. Para obter mais informações sobre estas regras, consulte a [referência da Create Index API](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Sub-campos de faceta

Qualquer sub-campo pode ser marcado como facetable, a menos que seja de tipo `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)`.

As contagens de documento devolvidas nos resultados da faceta são calculadas para o documento-mãe (um hotel), e não os sub-documentos numa coleção complexa (quartos). Por exemplo, suponha que um hotel tem 20 quartos de tipo "suite". Dado este parâmetro de rosto `facet=Rooms/Type`, a contagem de facetas será uma para o hotel, não 20 para os quartos.

### <a name="sorting-complex-fields"></a>Classificação de campos complexos

As operações de triagem aplicam-se a documentos (Hotéis) e não a sub-documentos (Quartos). Quando se tem uma coleção de tipo complexo, como quartos, é importante perceber que não se pode classificar em quartos. Na verdade, não se pode classificar em nenhuma coleção.

Ordenar operações funcionam quando os campos têm um único valor por documento, quer o campo seja um campo simples, ou um subcampo num tipo complexo. Por exemplo, `Address/City` é permitido ser classificado porque só há uma morada por hotel, por isso `$orderby=Address/City` vai separar hotéis por cidade.

### <a name="filtering-on-complex-fields"></a>Filtragem em campos complexos

Pode referir-se a sub-campos de um campo complexo numa expressão de filtro. Basta usar a mesma [sintaxe de caminho OData](query-odata-filter-orderby-syntax.md) que é usada para enfrentar, separar e selecionar campos. Por exemplo, o filtro seguinte devolverá todos os hotéis no Canadá:

    $filter=Address/Country eq 'Canada'

Para filtrar num campo de recolha complexo, pode utilizar uma **expressão lambda** com os [operadores`any` e `all`.](search-query-odata-collection-operators.md) Nesse caso, a **variável** de gama da expressão lambda é um objeto com sub-campos. Pode consultar esses subcampos com a sintaxe padrão do caminho OData. Por exemplo, o filtro seguinte devolverá todos os hotéis com pelo menos um quarto de luxo e todos os quartos para não fumadores:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Tal como acontece com campos simples de alto nível, os subcampos simples de campos complexos só podem ser incluídos em filtros se tiverem o atributo **filtrado** definido para `true` na definição de índice. Para mais informações, consulte a [referência da Create Index API](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Passos seguintes

Experimente o [conjunto de dados hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) no assistente de **dados da Importação.** Você precisará da informação de conexão Cosmos DB fornecida no readme para aceder aos dados.

Com essa informação na mão, o seu primeiro passo no assistente é criar uma nova fonte de dados do Azure Cosmos DB. Mais à frente no assistente, quando chegar à página do índice de destino, verá um índice com tipos complexos. Crie e carregue este índice, e depois execute consultas para entender a nova estrutura.

> [!div class="nextstepaction"]
> [Quickstart: assistente de portal para importação, indexação e consultas](search-get-started-portal.md)
