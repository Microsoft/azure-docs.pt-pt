---
title: Atualizar versões REST API
titleSuffix: Azure Cognitive Search
description: Reveja as diferenças nas versões API e saiba quais as ações necessárias para migrar o código existente para a mais recente versão do serviço de pesquisa cognitiva Azure REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112166"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Upgrade para a mais recente versão do Serviço de Pesquisa Cognitiva Azure REST API

Se estiver a utilizar uma versão anterior da API search [REST,](https://docs.microsoft.com/rest/api/searchservice/)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a mais recente versão API geralmente disponível, 2019-05-06.

A versão 2019-05-06 do REST API contém algumas alterações em versões anteriores. Estes são maioritariamente compatíveis para trás, por isso a alteração do seu código deve exigir apenas um esforço mínimo, dependendo da versão que estava a usar antes. [Os passos para atualizar](#UpgradeSteps) descrevem as alterações de código necessárias para a utilização de novas funcionalidades.

> [!NOTE]
> Uma instância de serviço de pesquisa cognitiva Azure suporta uma gama de versões REST API, incluindo as anteriores. Pode continuar a utilizar essas versões API, mas recomendamos que o seu código migrapara a versão mais recente possa aceder a novas capacidades.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Novidades na versão 2019-05-06
A versão 2019-05-06 é o mais recente lançamento geralmente disponível da Rest API. As funcionalidades que transitaram para o estado geralmente disponível nesta versão API incluem:

* [A conclusão automática](index-add-suggesters.md) é uma característica tipoà letra que completa uma entrada de termo parcialmente especificada.

* [Tipos complexos](search-howto-complex-data-types.md) fornecem suporte nativo para dados estruturados de objetos no índice de pesquisa.

* [Os modos de análise JsonLines,](search-howto-index-json-blobs.md)parte da indexação do Azure Blob, criam um documento de pesquisa por entidade JSON que é separado por uma novalinha.

* [O enriquecimento de IA](cognitive-search-concept-intro.md) fornece indexação que aproveita os motores de enriquecimento de IA dos Serviços Cognitivos.

Várias versões de pré-visualização coincidem com esta atualização geralmente disponível. Para rever a lista de novas funcionalidades de pré-visualização, consulte [Search REST api-version 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Alterações interruptivas

O código existente que contenha a seguinte funcionalidade quebrará na versão api=2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexante para O BD Do MB - datasource é agora "tipo": "cosmosdb"

Se estiver a usar um [indexador Cosmos DB,](search-howto-index-cosmosdb.md )tem de mudar `"type": "documentdb"` para `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Os erros de resultados de execução do indexador já não têm estatuto

A estrutura de erro para a `status` execução do indexante anteriormente tinha um elemento. Este elemento foi removido porque não estava a fornecer informações úteis.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Fonte de dados indexador API já não devolve cordas de ligação

A partir das versões API 2019-05-06 e 2019-05-06-Preview em diante, a fonte de dados API já não devolve as cordas de ligação na resposta a qualquer operação REST. Em versões Anteriores da API, para fontes de dados criadas através do POST, a Azure Cognitive Search devolveu **201** seguido da resposta OData, que continha a cadeia de ligação em texto simples.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Denominada habilidade cognitiva de reconhecimento de entidades é agora descontinuada

Se chamar a habilidade de reconhecimento de [entidade de nome](cognitive-search-skill-named-entity-recognition.md) no seu código, a chamada falhará. A funcionalidade de substituição é [o Reconhecimento de Entidades.](cognitive-search-skill-entity-recognition.md) Deve ser capaz de substituir a referência de habilidade por nenhuma outra alteração. A assinatura API é a mesma para ambas as versões. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Se estiver a atualizar-se a partir de uma versão anterior da A, 2017-11-11 ou 2016-09-01, provavelmente não terá de fazer alterações no seu código, a não ser para alterar o número da versão. As únicas situações em que pode ter de alterar o código são quando:

* O seu código falha quando propriedades não reconhecidas são devolvidas numa resposta DaPI. Por defeito, a sua aplicação deve ignorar propriedades que não compreende.

* O seu código persiste pedidos de API e tenta reenviá-los para a nova versão API. Por exemplo, isto pode acontecer se a sua aplicação persistir em fichas de `@search.nextPageParameters` continuação devolvidas da API de pesquisa (para mais informações, procure na [Referência API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)de Pesquisa ).

Se alguma destas situações se aplicar a si, poderá ter de alterar o seu código em conformidade. Caso contrário, não devem ser necessárias alterações a não ser que pretenda começar a utilizar as [novas funcionalidades](#WhatsNew) da versão 2019-05-06.

Se estiver a atualizar a partir de uma versão API de pré-visualização, o acima também se aplica, mas também deve estar ciente de que algumas funcionalidades de pré-visualização não estão disponíveis na versão 2019-05-06:

* [Consultas "Mais como isto"](search-more-like-this.md)
* [Indexação de blob CSV](search-howto-index-csv-blobs.md)
* [Apoio da API mongoDB para indexadores de DB cosmos](search-howto-index-cosmosdb.md)

Se o seu código utilizar estas funcionalidades, não será capaz de fazer upgrade para a versão API 2019-05-06 sem remover o seu uso das mesmas.

> [!IMPORTANT]
> As APIs de pré-visualização destinam-se a testes e avaliações e não devem ser utilizadas em ambientes de produção.
> 

### <a name="upgrading-complex-types"></a>Atualizar tipos complexos

Se o seu código utilizar tipos complexos com as versões API de pré-visualização mais antigas 2017-11-11-Preview ou 2016-09-01-Preview, existem alguns novos e alterados limites na versão 2019-05-06 dos quais precisa de estar atento:

+ Os limites da profundidade dos subcampos e do número de coleções complexas por índice foram reduzidos. Se criou índices que excedam estes limites utilizando as versões api de pré-visualização, qualquer tentativa de atualizá-los ou recriá-los usando a versão API 2019-05-06 falhará. Se isto se aplicar a si, terá de redesenhar o seu esquema para se encaixar dentro dos novos limites e, em seguida, reconstruir o seu índice.

+ Existe um novo limite na versão api 2019-05-06 sobre o número de elementos de coleções complexas por documento. Se criou índices com documentos que excedam estes limites utilizando as versões api de pré-visualização, qualquer tentativa de reindexar esses dados utilizando a api-version 2019-05-06 falhará. Se isto se aplicar a si, terá de reduzir o número de elementos de recolha complexos por documento antes de reindexar os seus dados.

Para mais informações, consulte [os limites de serviço para a Pesquisa Cognitiva Azure](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Como atualizar uma estrutura de tipo complexo antigo

Se o seu código estiver a utilizar tipos complexos com uma das versões API de pré-visualização mais antigas, pode estar a utilizar um formato de definição de índice que se parece com este:

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

Um novo formato semelhante a árvore para definir campos de índice foi introduzido na versão API 2017-11-11-Preview. No novo formato, cada campo complexo tem uma coleção de campos onde os seus subcampos são definidos. Na versão API 2019-05-06, este novo formato é utilizado exclusivamente e tentar criar ou atualizar um índice utilizando o formato antigo falhará. Se tiver índices criados utilizando o formato antigo, terá de utilizar a versão API 2017-11-11-Preview para atualizá-los para o novo formato antes de poderem ser geridos através da versão API 2019-05-06.

Pode atualizar os índices "plano" para o novo formato com os seguintes passos utilizando a versão API 2017-11-11-Preview:

1. Execute um pedido GET para recuperar o seu índice. Se já está no novo formato, está feito.

2. Traduza o índice do formato "plano" para o novo formato. Terá de escrever código para isto, já que não há nenhum código de amostra disponível no momento da escrita.

3. Execute um pedido DE PUT para atualizar o índice para o novo formato. Certifique-se de não alterar quaisquer outros detalhes do índice, tais como a procurabilidade/filtrabilidade dos campos, uma vez que isso não é permitido pela API do Índice de Atualização.

> [!NOTE]
> Não é possível gerir os índices criados com o antigo formato "plano" do portal Azure. Por favor, atualize os seus índices da representação "plana" para a representação "árvore" o mais rapidamente possível.

## <a name="next-steps"></a>Passos seguintes

Reveja a documentação de referência da API do Search REST. Se encontrar problemas, peça-nos ajuda no [StackOverflow](https://stackoverflow.com/) ou [suporte de contato](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Serviço de pesquisa REFERÊNCIA REST API](https://docs.microsoft.com/rest/api/searchservice/)

