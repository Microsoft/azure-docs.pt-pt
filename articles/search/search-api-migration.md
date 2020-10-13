---
title: Atualizar versões API REST
titleSuffix: Azure Cognitive Search
description: Reveja as diferenças nas versões API e saiba quais as ações necessárias para migrar o código existente para a mais recente versão API do serviço de pesquisa cognitiva Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929647"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Upgrade para a mais recente API REST em Pesquisa Cognitiva Azure

Se estiver a utilizar uma versão anterior da [**API Search REST,**](/rest/api/searchservice/)este artigo irá ajudá-lo a atualizar a sua aplicação para a versão API mais recente, **2020-06-30**.

A versão 2020-06-30 inclui uma nova funcionalidade importante ([knowledge store](knowledge-store-concept-intro.md)), e introduz várias mudanças de comportamento menores. Como tal, esta versão é maioritariamente compatível com retrocesso, pelo que as alterações de código devem ser mínimas se estiver a atualizar a partir da versão anterior (2019-05-06).

> [!NOTE]
> Um serviço de pesquisa suporta uma série de versões REST API, incluindo as anteriores. Pode continuar a utilizar essas versões API, mas recomendamos a migração do seu código para a versão mais recente para que possa aceder a novas capacidades. Com o tempo, as versões mais desatualizadas da API REST serão depreciadas e [deixarão de ser suportadas.](search-api-versions.md#unsupported-versions)

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Como atualizar

Ao atualizar para uma nova versão, provavelmente não terá de fazer muitas alterações ao seu código, além de alterar o número da versão. As únicas situações em que poderá ter de alterar o código são quando:

* O seu código falha quando as propriedades não reconhecidas são devolvidas numa resposta API. Por predefinição, a sua aplicação deve ignorar propriedades que não compreende.

* O seu código persiste nos pedidos da API e tenta reencaendê-los para a nova versão API. Por exemplo, isto pode acontecer se a sua aplicação persistir em fichas de continuação devolvidas da API de pesquisa (para mais informações, procure `@search.nextPageParameters` na [Referência API de Pesquisa).](/rest/api/searchservice/Search-Documents)

* O seu código refere uma versão API que antecede 2019-05-06 e está sujeito a uma ou mais alterações de rutura nessa versão. A secção [Upgrade para 2019-05-06](#upgrade-to-2019-05-06) fornece mais detalhes. 

Se alguma destas situações se aplicar a si, poderá ter de alterar o seu código em conformidade. Caso contrário, não deverá ser necessário alterar as alterações, embora seja de esperar que se possa começar a utilizar funcionalidades adicionadas na nova versão.

## <a name="upgrade-to-2020-06-30"></a>Upgrade para 2020-06-30

Versão 2020-06-30 é o novo lançamento geralmente disponível da API REST. Há uma mudança de rutura e várias diferenças comportamentais. 

As funcionalidades estão agora geralmente disponíveis nesta versão API:

* [Loja de conhecimento,](knowledge-store-concept-intro.md)armazenamento persistente de conteúdos enriquecidos criados através de skillsets, criados para análise e processamento a jusante através de outras aplicações. Com esta capacidade, um oleoduto de enriquecimento de IA orientado por indexante pode povoar uma loja de conhecimento, além de um índice de pesquisa. Se utilizar a versão de pré-visualização desta funcionalidade, esta é equivalente à versão geralmente disponível. A única alteração de código necessária é modificar a versão api.

### <a name="breaking-change"></a>Mudança de rutura

O código existente escrito contra versões anteriores da API irá quebrar na versão api=2020-06-30 e, mais tarde, se o código contiver a seguinte funcionalidade:

* Quaisquer literais Edm.Date (data composta por um mês- dia, `2020-12-12` como) em expressões de filtro devem seguir o formato Edm.DateTimeOffset: `2020-12-12T00:00:00Z` . Esta alteração foi necessária para lidar com resultados de consultas erróneas ou inesperadas devido a diferenças de azono.

### <a name="behavior-changes"></a>Mudanças de comportamento

* [O algoritmo de classificação BM25](index-ranking-similarity.md) substitui o algoritmo de classificação anterior por tecnologia mais recente. Novos serviços usarão este algoritmo automaticamente. Para os serviços existentes, deve definir parâmetros para usar o novo algoritmo.

* Os resultados ordenados para valores nulos mudaram nesta versão, com os valores nulos a aparecerem primeiro se o tipo for `asc` e durar se o tipo for `desc` . Se escreveu código para lidar com a classificação dos valores nulos, esteja ciente desta alteração.

## <a name="upgrade-to-2019-05-06"></a>Upgrade para 2019-05-06

Versão 2019-05-06 é o lançamento geralmente disponível anterior da API REST. As funcionalidades que ficaram geralmente disponíveis nesta versão API incluem:

* [Autocomplete](index-add-suggesters.md) é uma função de tipa que completa uma entrada de termo parcialmente especificada.
* [Os tipos complexos](search-howto-complex-data-types.md) fornecem suporte nativo para dados estruturados de objetos no índice de pesquisa.
* [Os modos de análise JsonLines](search-howto-index-json-blobs.md), parte da indexação Azure Blob, criam um documento de pesquisa por entidade JSON que é separado por uma nova linha.
* [O enriquecimento de](cognitive-search-concept-intro.md) IA fornece indexação que alavanca os motores de enriquecimento de IA dos Serviços Cognitivos.

### <a name="breaking-changes"></a>Alterações interruptivas

O código existente escrito contra versões anteriores da API irá quebrar na versão api=2019-05-06 e mais tarde se o código contiver a seguinte funcionalidade:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer for Azure Cosmos DB - datasource é agora "type": "cosmosdb"

Se estiver a utilizar um [indexador Cosmos DB,](search-howto-index-cosmosdb.md )tem de mudar `"type": "documentdb"` para `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Os erros de resultado de execução do indexante já não têm estatuto

A estrutura de erro para a execução do indexante tinha anteriormente um `status` elemento. Este elemento foi removido porque não estava a fornecer informações úteis.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Fonte de dados indexante API já não devolve cadeias de ligação

Das versões API 2019-05-06 e 2019-05-06-Preview, a fonte de dados API já não devolve as cordas de ligação na resposta a qualquer operação REST. Nas versões anteriores da API, para fontes de dados criadas através do POST, a Azure Cognitive Search devolveu **201** seguida da resposta OData, que continha a cadeia de ligação em texto simples.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Denominada Competência cognitiva de Reconhecimento de Entidade é agora descontinuada

Se tiver chamado a competência [de Reconhecimento de Entidade de Nome](cognitive-search-skill-named-entity-recognition.md) no seu código, a chamada falhará. A funcionalidade de substituição é [o Reconhecimento de Entidades.](cognitive-search-skill-entity-recognition.md) Deverá ser capaz de substituir a referência de habilidade sem outras alterações. A assinatura API é a mesma para ambas as versões. 

### <a name="upgrading-complex-types"></a>Modernização de tipos complexos

A versão API 2019-05-06 acrescentou suporte formal para tipos complexos. Se o seu código implementou recomendações anteriores para equivalência de tipo complexo em 2017-11-11-Pré-visualização ou 2016-09-01-Preview, existem alguns limites novos e alterados a partir da versão 2019-05-06 dos quais precisa de estar ciente:

+ Os limites da profundidade dos sub-campos e o número de coleções complexas por índice foram reduzidos. Se criou índices que excedem estes limites utilizando as versões api de pré-visualização, qualquer tentativa de os atualizar ou recriar utilizando a versão API 2019-05-06 falhará. Se isto se aplicar a si, terá de redesenhar o seu esquema para se encaixar dentro dos novos limites e, em seguida, reconstruir o seu índice.

+ Há um novo limite a partir da versão API 2019-05-06 sobre o número de elementos de coleções complexas por documento. Se criou índices com documentos que excedem estes limites utilizando as versões api-pré-visualização, qualquer tentativa de reindexar esses dados utilizando a versão api 2019-05-06 falhará. Se isto se aplicar a si, terá de reduzir o número de elementos de recolha complexos por documento antes de reindexar os seus dados.

Para obter mais informações, consulte [os limites de serviço para a pesquisa cognitiva Azure](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Como atualizar uma estrutura de tipo complexo antigo

Se o seu código estiver a utilizar tipos complexos com uma das versões API de pré-visualização mais antigas, poderá estar a utilizar um formato de definição de índice que se parece com este:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Um novo formato semelhante a árvores para definir campos de índice foi introduzido na versão API 2017-11-11-Preview. No novo formato, cada campo complexo tem uma coleção de campos onde os seus sub-campos são definidos. Na versão API 2019-05-06, este novo formato é utilizado exclusivamente e tentar criar ou atualizar um índice utilizando o formato antigo falhará. Se tiver índices criados utilizando o formato antigo, terá de utilizar a versão API 2017-11-11-Preview para os atualizar para o novo formato antes de poder ser gerido através da versão API 2019-05-06.

Pode atualizar índices "plano" para o novo formato com os seguintes passos utilizando a versão API 2017-11-11-Preview:

1. Realize um pedido GET para recuperar o seu índice. Se já está no novo formato, está feito.

2. Traduza o índice do formato "plano" para o novo formato. Terá de escrever código para isto, uma vez que não existe um código de amostra disponível no momento desta escrita.

3. Execute um pedido DEP para atualizar o índice para o novo formato. Certifique-se de que não altera quaisquer outros detalhes do índice, como a capacidade de pesquisa/filtrabilidade dos campos, uma vez que tal não é permitido pela API do Índice de Atualização.

> [!NOTE]
> Não é possível gerir índices criados com o antigo formato "flat" do portal Azure. Por favor, atualize os seus índices da representação "plana" para a representação "árvore" o mais rapidamente possível.

## <a name="next-steps"></a>Passos seguintes

Reveja a documentação de referência da API Search REST. Se encontrar problemas, peça-nos ajuda no [Stack Overflow](https://stackoverflow.com/) ou [no suporte de contacto.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Referência de API do serviço de pesquisa REST](/rest/api/searchservice/)
