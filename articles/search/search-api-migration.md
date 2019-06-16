---
title: Atualizar para a versão mais recente do API de REST do serviço de pesquisa do Azure - Azure Search
description: Reveja as diferenças em versões de API e saiba quais ações são necessárias para migrar o código existente para a versão mais recente do API de REST do serviço de pesquisa do Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 85a8ddf4ce87d7ac8ce460c0aff56311a2ea4578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540701"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Atualizar para a versão mais recente do API de REST do serviço de pesquisa do Azure
Se estiver a utilizar uma versão anterior dos [API de REST do serviço do Azure Search](https://docs.microsoft.com/rest/api/searchservice/), este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão mais recente da API disponível em geral, 05-06 de 2019.

Versão de 2019 05 06 da REST API contém algumas alterações de versões anteriores. Estes são principalmente compatíveis com versões anteriores, para que alterar o seu código deve exigir um esforço mínimo apenas, dependendo da versão estava a utilizar antes. [Passos para atualizar](#UpgradeSteps) descreve as alterações de código necessárias para utilizar novas funcionalidades.

> [!NOTE]
> Uma instância de serviço do Azure Search oferece suporte a versões um intervalo de API do REST, incluindo os anteriores. Pode continuar a utilizar essas versões de API, mas recomendamos que migre seu código para a versão mais recente, para que pode acessar os novos recursos.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>O que há de novo na versão 05-06 de 2019
Versão de 2019-05-06 é a versão mais recente em disponibilidade geral da API de REST do serviço de pesquisa do Azure. As funcionalidades que a transição de estado de disponibilidade geral nesta versão de API incluem:

* [Preenchimento automático](index-add-suggesters.md) é uma funcionalidade de typeahead que conclui uma entrada de termo parcialmente especificado.

* [Tipos complexos](search-howto-complex-data-types.md) fornece suporte nativo para os dados de objeto estruturado num índice da Azure Search.

* [Modos de análise de JsonLines](search-howto-index-json-blobs.md), parte de Blobs do Azure, indexação, cria um documento de pesquisa por entidade JSON que é separado por um caractere de nova linha.

* [Pesquisa cognitiva](cognitive-search-concept-intro.md) fornece a indexação que aproveita os mecanismos de enriquecimento de IA dos serviços cognitivos.

Vários lançamentos de funcionalidades de pré-visualização coincidir com esta atualização em disponibilidade geral. Para rever a lista de novas funcionalidades de pré-visualização, veja [versão de api REST de pesquisa de 2019-05-06-pré-visualização](search-api-preview.md).

## <a name="breaking-changes"></a>Alterações interruptivas

Código existente que contém a seguinte funcionalidade será interrompida na api-version = 05-06 de 2019.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexador do Azure Cosmos DB - datasource é agora "de tipo": "cosmosdb"

Se estiver a utilizar um [indexador do Cosmos DB](search-howto-index-cosmosdb.md ), tem de alterar `"type": "documentdb"` para `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Erros de resultado de execução do indexador deixará de ter estado

A estrutura de erro de execução do indexador tinha anteriormente uma `status` elemento. Este elemento foi removido porque não fornecia informações úteis.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>API de origem de dados do indexador deixasse de devolver cadeias de ligação

A partir da API versões 2019-05-06 e 2019-05-06-pré-visualização e posteriores, a origem de dados API deixasse de devolver cadeias de ligação na resposta de qualquer operação REST. Nas versões anteriores de API, para origens de dados criadas a utilização do POST, o Azure Search devolvido **201** seguido a resposta de OData, que continha a cadeia de ligação em texto simples.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Com o nome reconhecimento de entidades habilidade cognitiva está agora descontinuada

Se chamar [reconhecimento de entidades de nome](cognitive-search-skill-named-entity-recognition.md) habilidade em seu código, a chamada falhará. É a funcionalidade de substituição [reconhecimento de entidades](cognitive-search-skill-entity-recognition.md). Deverá conseguir substitua a referência de habilidades sem nenhuma outra alteração. A assinatura de API é o mesmo para as duas versões. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar
Se estiver a atualizar de uma versão anterior do GA, 2017-11-11 ou 2016-09-01, provavelmente não terá que efetuar quaisquer alterações ao seu código, além de alterar o número de versão. As únicas situações em que poderá ter de alterar o código são quando:

* Seu código falha quando propriedades não reconhecidas são retornadas numa resposta de API. Por predefinição, a aplicação deve ignorar propriedades que não compreende.

* O código mantém os pedidos de API e tenta reenviá-los para a nova versão de API. Por exemplo, isto pode acontecer se a sua aplicação persistir tokens de continuação devolvidos a partir da API de pesquisa (para obter mais informações, procure `@search.nextPageParameters` no [referência da API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Se qualquer uma destas situações se aplicar a, poderá ter de alterar o seu código em conformidade. Caso contrário, não existem alterações não será necessárias, a menos que deseja começar a utilizar o [novos recursos](#WhatsNew) da versão 05-06 de 2019.

Se estiver a atualizar a partir de uma versão de API de pré-visualização, o procedimento acima também se aplica, mas também deve estar ciente de que algumas funcionalidades de pré-visualização não estão disponíveis na versão de 2019-05-06:

* [Consultas de "Mais semelhante à seguinte"](search-more-like-this.md)
* [A indexação de BLOBs CSV](search-howto-index-csv-blobs.md)
* [Suporte de API do MongoDB para indexadores do Cosmos DB](search-howto-index-cosmosdb.md)

Se o seu código Use esses recursos, não será capaz de atualizar para versão de 2019-05-06 de API, sem remover a sua utilização dos mesmos.

> [!IMPORTANT]
> As APIs de pré-visualização destinam-se para teste e avaliação e não devem ser usadas em ambientes de produção.
> 

### <a name="upgrading-complex-types"></a>Atualizar tipos complexos

Se o seu código utiliza tipos complexos com as versões de API de pré-visualização mais antigas 2017-11-11-pré-visualização ou 2016-09-01-pré-visualização, existem alguns limites de novas e alteradas na versão 06 de 05 de 2019 dos quais precisa saber:

+ Os limites na profundidade de subcampos e o número de coleções complexas por índice tem sido reduzidos. Se tiver criado os índices que excederem estes limites utilizando as versões de api de pré-visualização, qualquer tentativa atualizar ou recriá-las com a API versão 2019-05-06 irá falhar. Se isto se aplica a, terá de Refazer o design do esquema para caber dentro dos limites de novo e, em seguida, reconstrua o índice.

+ Existe um limite de novo na api-version 2019 05 06 no número de elementos de coleções complexas por documento. Se tiver criado índices com documentos que excederem estes limites utilizando as versões de api de pré-visualização, qualquer tentativa de reindexar esses dados usando a api-version 2019-05-06 irá falhar. Se isto se aplica a, precisará reduzir o número de elementos de coleção complexa por documento antes de reindexação seus dados.

Para obter mais informações, consulte [limites de serviço para o Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Como atualizar uma estrutura de tipo complexo antigo

Se o seu código está a utilizar tipos complexos com uma das versões mais antigas para a API de pré-visualização, pode estar a utilizar um formato de definição de índice que tem esta aparência:

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

Um formato de árvore mais recente para a definição de campos de índice foi introduzido na versão 2017-11-11-a pré-visualização da API. No novo formato, cada campo complexo tem uma coleção de campos onde seus campos secundárias são definidos. Na versão de 2019-05-06 de API, esse novo formato é usado exclusivamente e tentativa de criar ou atualizar um índice utilizando o formato antigo falhará. Se tiver de índices criados usando o formato antigo, terá de utilizar a versão 2017-11-11-a pré-visualização da API para atualizá-los para o novo formato, antes que possam ser geridos com a API versão 05-06 de 2019.

Pode atualizar índices "simples" para o novo formato a seguir os passos seguintes com a versão 2017-11-11-a pré-visualização da API:

1. Efetue um pedido GET para obter o seu índice. Se ainda estiver no novo formato, já está.

2. Traduza o índice do formato "simples" para o novo formato. Terá de escrever código para isso, uma vez que não existe nenhum código de exemplo disponível no momento da redação deste artigo.

3. Efetue um pedido PUT para atualizar o índice para o novo formato. Lembre-se de que não alterar quaisquer outros detalhes do índice, como a capacidade de pesquisa/filterability de campos, uma vez que isto não é permitido pela API do índice de atualização.

> [!NOTE]
> Não é possível gerir índices criados com o formato antigo "simples" do portal do Azure. Atualize os índices da representação "simples" para a representação de "árvore" o quanto.

## <a name="next-steps"></a>Passos Seguintes

Reveja a documentação de referência da API de REST do serviço de pesquisa do Azure. Se tiver problemas, peça-nos para obter ajuda no [Stack Overflow](https://stackoverflow.com/) ou [contacte o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)

