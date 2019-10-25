---
title: Atualizar para a versão mais recente da API REST do serviço de Pesquisa Cognitiva do Azure
titleSuffix: Azure Cognitive Search
description: Examine as diferenças nas versões de API e saiba quais ações são necessárias para migrar o código existente para a versão mais recente da API REST do serviço de Pesquisa Cognitiva do Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9bffb41cce030b7a63e600e5ffaf65130261b4c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791164"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Atualizar para a versão mais recente da API REST do serviço de Pesquisa Cognitiva do Azure

Se você estiver usando uma versão anterior da [API REST de pesquisa](https://docs.microsoft.com/rest/api/searchservice/), este artigo o ajudará a atualizar seu aplicativo para usar a versão de API mais recente disponível, 2019-05-06.

A versão 2019-05-06 da API REST contém algumas alterações de versões anteriores. Esses são, na maioria das versões anteriores, compatíveis, portanto, alterar seu código deve exigir apenas um mínimo de esforço, dependendo da versão que você estava usando antes. [As etapas para atualizar](#UpgradeSteps) descreve as alterações de código necessárias para o uso de novos recursos.

> [!NOTE]
> Uma instância do serviço de Pesquisa Cognitiva do Azure dá suporte a uma variedade de versões da API REST, incluindo as anteriores. Você pode continuar a usar essas versões de API, mas é recomendável migrar seu código para a versão mais recente para que você possa acessar novos recursos.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>O que há de novo na versão 2019-05-06
A versão 2019-05-06 é a versão mais recente disponível da API REST. Os recursos que passaram por transição para o status de disponibilidade geral nesta versão de API incluem:

* O [preenchimento automático](index-add-suggesters.md) é um recurso typeahead que conclui uma entrada de termo parcialmente especificada.

* [Tipos complexos](search-howto-complex-data-types.md) fornecem suporte nativo para dados de objeto estruturado no índice de pesquisa.

* Os [modos de análise de JsonLines](search-howto-index-json-blobs.md), parte da indexação de BLOBs do Azure, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.

* O [enriquecimento de ia](cognitive-search-concept-intro.md) fornece indexação que aproveita os mecanismos de enriquecimento de ia de serviços cognitivas.

Várias versões do recurso de visualização coincidem com essa atualização disponível para o público em geral. Para examinar a lista de novos recursos de visualização, consulte [Pesquisar API REST – versão 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Alterações interruptivas

O código existente que contém a seguinte funcionalidade será interrompido na API-Version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>O indexador para Azure Cosmos DB-DataSource agora é "tipo": "cosmosdb"

Se você estiver usando um [indexador Cosmos DB](search-howto-index-cosmosdb.md ), deverá alterar `"type": "documentdb"` para `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Os erros de resultado da execução do indexador não têm mais status

A estrutura de erros para a execução do indexador anteriormente tinha um elemento `status`. Este elemento foi removido porque não estava fornecendo informações úteis.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>A API de fonte de dados do indexador não retorna mais cadeias de conexão

Nas versões de API 2019-05-06 e 2019-05-06-preview em diante, a API de fonte de dados não retorna mais cadeias de conexão na resposta de qualquer operação REST. Nas versões anteriores da API, para fontes de dados criadas usando POST, o Azure Pesquisa Cognitiva retornou **201** seguido pela resposta do OData, que continha a cadeia de conexão em texto sem formatação.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>A habilidade cognitiva de reconhecimento de entidade nomeada agora está descontinuada

Se você chamar a habilidade de [reconhecimento de entidade de nome](cognitive-search-skill-named-entity-recognition.md) em seu código, a chamada falhará. A funcionalidade de substituição é o [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md). Você deve ser capaz de substituir a referência de habilidades sem outras alterações. A assinatura de API é a mesma para ambas as versões. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas para atualizar
Se você estiver atualizando de uma versão de GA anterior, 2017-11-11 ou 2016-09-01, provavelmente não precisará fazer nenhuma alteração no código, a não ser alterar o número de versão. As únicas situações em que talvez você precise alterar o código são quando:

* O código falha quando propriedades não reconhecidas são retornadas em uma resposta de API. Por padrão, seu aplicativo deve ignorar as propriedades que ele não entende.

* O código persiste as solicitações de API e tenta reenviá-las para a nova versão de API. Por exemplo, isso pode acontecer se o aplicativo persistir tokens de continuação retornados da API de pesquisa (para obter mais informações, procure `@search.nextPageParameters` na [referência de API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Se qualquer uma dessas situações se aplicar a você, talvez seja necessário alterar seu código de acordo. Caso contrário, nenhuma alteração deve ser necessária, a menos que você queira começar a usar os [novos recursos](#WhatsNew) da versão 2019-05-06.

Se você estiver atualizando de uma versão de API de visualização, o acima também se aplicará, mas você também deve estar ciente de que alguns recursos de visualização não estão disponíveis na versão 2019-05-06:

* [Consultas "mais como esta"](search-more-like-this.md)
* [Indexação de blob de CSV](search-howto-index-csv-blobs.md)
* [Suporte à API do MongoDB para indexadores de Cosmos DB](search-howto-index-cosmosdb.md)

Se seu código usar esses recursos, você não poderá atualizar para a versão de API 2019-05-06 sem remover o uso deles.

> [!IMPORTANT]
> As APIs de visualização são destinadas a testes e avaliações e não devem ser usadas em ambientes de produção.
> 

### <a name="upgrading-complex-types"></a>Atualizando tipos complexos

Se seu código usa tipos complexos com as versões mais antigas da API de visualização 2017-11-11-Preview ou 2016-09-01-Preview, há alguns limites novos e alterados na versão 2019-05-06 dos quais você precisa estar ciente:

+ Os limites na profundidade de subcampos e o número de coleções complexas por índice foram reduzidos. Se você criou índices que excedem esses limites usando as versões de API de visualização, qualquer tentativa de atualizar ou recriá-los usando a versão de API 2019-05-06 falhará. Se isso se aplicar a você, você precisará recriar o esquema para se ajustar aos novos limites e, em seguida, recriar o índice.

+ Há um novo limite na versão de API 2019-05-06 no número de elementos de coleções complexas por documento. Se você criou índices com documentos que excedem esses limites usando as versões de API de visualização, qualquer tentativa de reindexar esses dados usando a versão de API 2019-05-06 falhará. Se isso se aplicar a você, você precisará reduzir o número de elementos de coleção complexos por documento antes de reindexar seus dados.

Para obter mais informações, consulte [limites de serviço para o Azure pesquisa cognitiva](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Como atualizar uma estrutura antiga de tipo complexo

Se o seu código estiver usando tipos complexos com uma das versões mais antigas da API de visualização, você poderá estar usando um formato de definição de índice parecido com este:

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

Um formato mais recente do tipo árvore para definir campos de índice foi introduzido na versão de API 2017-11-11-Preview. No novo formato, cada campo complexo tem uma coleção Fields onde seus subcampos são definidos. Na API versão 2019-05-06, esse novo formato é usado exclusivamente e a tentativa de criar ou atualizar um índice usando o formato antigo falhará. Se você tiver índices criados usando o formato antigo, precisará usar a versão de API 2017-11-11-Preview para atualizá-los para o novo formato antes que eles possam ser gerenciados usando a versão de API 2019-05-06.

Você pode atualizar índices "simples" para o novo formato com as seguintes etapas usando a versão de API 2017-11-11-Preview:

1. Execute uma solicitação GET para recuperar o índice. Se já estiver no novo formato, você terminará.

2. Traduza o índice do formato "flat" para o novo formato. Você precisará escrever código para isso, já que não há nenhum código de exemplo disponível no momento da redação deste artigo.

3. Execute uma solicitação PUT para atualizar o índice para o novo formato. Certifique-se de não alterar nenhum outro detalhe do índice, como a pesquisa/filtragem de campos, pois isso não é permitido pela API do índice de atualização.

> [!NOTE]
> Não é possível gerenciar índices criados com o antigo formato "plano" do portal do Azure. Atualize seus índices da representação "simples" para a representação de "árvore" da sua primeira conveniência.

## <a name="next-steps"></a>Passos seguintes

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)

