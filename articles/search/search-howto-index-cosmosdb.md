---
title: Índice de uma origem de dados do Azure Cosmos DB - Azure Search
description: Pesquise a uma origem de dados do Azure Cosmos DB e ingestão de dados num índice de texto completo pesquisável no Azure Search. Indexadores automatizam ingestão de dados para origens de dados selecionada, como o Azure Cosmos DB.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
robot: noindex
ms.custom: seodec2018
ms.openlocfilehash: d63fdbfd71e812e9b445fb0055cb9aee5876ecc1
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962150"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Ligar o Cosmos DB com o Azure Search utilizando indexadores

Neste artigo, saiba como:

> [!div class="checklist"]
> * Configurar [indexador de Azure Search](search-indexer-overview.md) que utiliza uma coleção do Azure Cosmos DB como uma origem de dados.
> * Crie um índice de pesquisa com tipos de dados compatíveis com JSON.
> * Configure um indexador para sob demanda e indexação periódica.
> * Atualização de forma incremental o índice com base nas alterações nos dados subjacentes.

> [!NOTE]
> O Azure Cosmos DB é a próxima geração do DocumentDB. Embora o nome do produto for alterado, o `documentdb` sintaxe em indexadores do Azure Search ainda existe para efeitos compatibilidade de APIs de pesquisa do Azure e páginas de portal. Ao configurar os indexadores, certifique-se de que especifique o `documentdb` sintaxe de acordo com as instruções neste artigo.

O vídeo seguinte, Andrew Liu gerente de programa do DB Cosmos Azure demonstra como adicionar um índice da Azure Search para um contentor do Azure Cosmos DB.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>Tipos de API suportados

Embora o Azure Cosmos DB suporta uma variedade de APIs e modelos de dados, estende o suporte de produção do indexador de Azure Search para apenas a API de SQL. Suporte para a API do Azure Cosmos DB para o MongoDB está atualmente em pré-visualização pública.  

Suporte para as APIs adicionais é próximo. Para nos ajudar a priorizar quais para suportar pela primeira vez, volte a converter o seu voto no web site a voz do utilizador:

* [Suporte de origem de dados de API de tabela](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Suporte de origem de dados da Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Suporte de origem de dados de Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Pré-requisitos

Além de uma conta do Cosmos DB, tem de ter uma [serviço Azure Search](search-create-service-portal.md). 

Na sua conta do Cosmos DB pode escolher se pretende que a coleção para indexar automaticamente todos os documentos. Por predefinição, todos os documentos são indexados automaticamente, mas pode desativar a indexação automática. Quando a indexação é desativada, documentos podem ser acedidos através de apenas seus auto-ligações ou por consultas utilizando o documento ID. O Azure Search necessita do Cosmos DB automática de indexação ativada na coleção que será indexada pelo Azure Search. 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Conceitos do indexador de pesquisa do Azure

R **origem de dados** Especifica os dados para o índice, credenciais e políticas para identificar alterações nos dados (como documentos modificados ou eliminados dentro da coleção). A origem de dados está definida como um recurso independente, para que possa ser utilizado por vários indexadores.

Uma **indexador** descreve a forma como os dados fluem da origem de dados para um índice de pesquisa de destino. Um indexador pode ser utilizado para:

* Efetue uma cópia única dos dados para preencher um índice.
* Um índice com alterações na origem de dados com base numa agenda de sincronização.
* Invoca atualizações sob demanda para um índice, conforme necessário.

Para configurar um indexador do Azure Cosmos DB, terá de criar um índice, origem de dados e, finalmente, o indexador. Pode criar esses objetos usando o [portal](search-import-data-portal.md), [.NET SDK](/dotnet/api/microsoft.azure.search), ou [REST API](/rest/api/searchservice/). 

Este artigo mostra como utilizar a API REST. Se optar pelo portal, certifique-se que a base de dados do Cosmos DB contém dados. O [Assistente de importação de dados](search-import-data-portal.md) lê metadados e realiza a amostragem de dados para inferir um esquema de índice, mas também cargas de dados do Cosmos DB. Se os dados estão em falta, o assistente será interrompido com este erro "esquema índice detectando de erro da origem de dados: Não foi possível criar um protótipo de índice porque a origem de dados "emptycollection" não devolveu dados. ".

> [!TIP]
> Pode iniciar o assistente **Importar dados** a partir do dashboard do Azure Cosmos DB, para simplificar a indexação dessa origem de dados. Na navegação à esquerda, aceda a **Coleções** > **Adicionar Azure Search** para começar.

> [!NOTE] 
> Por enquanto, não é possível criar ou editar **MongoDB** origens de dados com o Portal do Azure ou o SDK do .NET. No entanto, **pode** monitorizar o histórico de execução do MongoDB indexadores no portal.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Passo 1: Criar uma origem de dados
Para criar uma origem de dados, faça uma POSTAGEM:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

O corpo do pedido contém a definição de origem de dados, que deve incluir os seguintes campos:

* **name**: Escolha o nome para representar a sua base de dados.
* **type**: Tem de ser `documentdb`.
* **credenciais**:
  
  * **connectionString**: Necessário. Especifique as informações de ligação à base de dados do Azure Cosmos DB no seguinte formato: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` Para coleções de MongoDB, adicione **ApiKind = MongoDb** para a cadeia de ligação: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`
  Evite os números de porta no url do ponto final. Se incluir o número de porta, o Azure Search será não é possível indexar a base de dados do Azure Cosmos DB.
* **container**:
  
  * **name**: Necessário. Especifica o id da coleção da base de dados ser indexados.
  * **query**: Opcional. Pode especificar uma consulta para nivelamento um documento JSON arbitrário num esquema simples que o Azure Search pode indexar. Para coleções de MongoDB, as consultas não são suportadas. 
* **dataChangeDetectionPolicy**: Recomendado. Ver [indexar documentos alterados](#DataChangeDetectionPolicy) secção.
* **dataDeletionDetectionPolicy**: Opcional. Ver [indexar documentos eliminado](#DataDeletionDetectionPolicy) secção.

### <a name="using-queries-to-shape-indexed-data"></a>Através de consultas a forma indexou dados
Pode especificar uma consulta SQL para nivelamento propriedades aninhadas ou matrizes, as propriedades do projeto JSON e filtrar os dados a ser indexados. 

> [!WARNING]
> Consultas personalizadas não são suportadas para **MongoDB** coleções: `container.query` parâmetro tem de ser definido como nulo ou omitido. Se precisar de utilizar uma consulta personalizada, faça contato no [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Documento de exemplo:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Consulta de filtro:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Mesclar consulta:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de projeção:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta de mesclagem de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Passo 2: Criar um índice
Se ainda não tiver uma, crie um índice de pesquisa do Azure de destino. Pode criar um índice com o [portal do Azure da interface do Usuário](search-create-index-portal.md), o [criar o índice REST API](/rest/api/searchservice/create-index) ou [classe de índice](/dotnet/api/microsoft.azure.search.models.index).

O exemplo seguinte cria um índice com um campo de id e a descrição:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Certifique-se de que o esquema do seu índice de destino é compatível com o esquema dos documentos JSON de origem ou a saída da sua projecção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave do documento padrão é o Azure Cosmos DB `_rid` propriedade, que o Azure Search muda automaticamente o nome para `rid` porque os nomes de campo não podem começar com um caráter de undescore. Além disso, o Azure Cosmos DB `_rid` valores contêm carateres que são inválidas em chaves do Azure Search. Por esse motivo, o `_rid` valores são codificados em Base64.
> 
> Para coleções de MongoDB, Azure Search automaticamente muda o nome da `_id` propriedade `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados de pesquisa do Azure
| Tipo de dados JSON | Tipos de campo de índice de destino compatível |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Números que são semelhantes a números inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números entre aquela aparência como os pontos de vírgula flutuante |Edm.Double, Edm.String |
| String |Edm.String |
| Matrizes de tipos primitivos, por exemplo ["a", "b", "c"] |Coleção (Edm.String) |
| Cadeias de caracteres que se pareçam com datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo {"type": "Point", "coordenadas": [longo, lat]} |Edm.GeographyPoint |
| Outros objetos JSON |N/A |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Passo 3: Criar um indexador

Assim que tiver sido criado a origem de dados e índice, está pronto para criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Este indexador é executado a cada duas horas (intervalo de agendamento está definido para "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O mais curto intervalo suportado é de 5 minutos. A agenda é opcional - se for omitido, um indexador é executado apenas uma vez, quando é criado. No entanto, pode executar uma indexador sob demanda em qualquer altura.   

Para obter mais detalhes sobre a API de indexador criar, confira [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Executar indexador a pedido
Além de executar periodicamente com base numa agenda, um indexador também pode ser chamado a pedido:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> Quando executar a API devolve com êxito, a invocação de indexador foi agendada, mas o processamento real acontece de forma assíncrona. 

Pode monitorizar o estado do indexador no portal ou através da introdução indexador Estado API, que descrevemos, em seguida. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Obter estado do indexador
Pode recuperar o histórico de estado e a execução de um indexador:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

A resposta contém o estado geral do indexador, a invocação de indexador último (ou em curso) e o histórico de invocações de indexador recentes.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Histórico de execução contém até as 50 execuções concluídas mais recentes, que são classificadas em ordem cronológica reversa (para que a execução mais recente acontecer primeira na resposta).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indexar documentos alterados
O objetivo de uma política de deteção de alteração de dados é eficiente identificar itens de dados alterados. Atualmente, a política de suporte única é a `High Water Mark` política utilizando o `_ts` a propriedade (timestamp) fornecida pelo Azure Cosmos DB, que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Com esta política é altamente recomendado para garantir um desempenho bom indexador. 

Se estiver a utilizar uma consulta personalizada, certifique-se de que o `_ts` propriedade está projetada pela consulta.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas
Progresso incremental durante a indexação garante que se a execução do indexador é interrompida por falhas transitórias ou limite de tempo de execução, o indexador possam começar onde parou próxima vez que é executado, em vez de precisar reindexar toda a coleção do zero. Isto é especialmente importante quando grandes coleções de indexação. 

Para ativar o progresso incremental ao utilizar uma consulta personalizada, certifique-se de que a sua consulta ordena os resultados pelo `_ts` coluna. Isto permite que periódica verificação apontar que utiliza o Azure Search para fornecer o progresso incremental na presença de falhas.   

Em alguns casos, mesmo se sua consulta contém um `ORDER BY [collection alias]._ts` cláusula, o Azure Search pode não inferir que a consulta está ordenada pelo `_ts`. Pode dizer ao Azure Search que os resultados são ordenados, utilizando o `assumeOrderByHighWaterMarkColumn` propriedade de configuração. Para especificar esta sugestão, criar ou atualizar o indexador da seguinte forma: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indexar os documentos excluídos
Quando as linhas são eliminadas da coleção, normalmente pretende eliminar as linhas do também o índice de pesquisa. O objetivo de uma política de deteção de eliminação de dados é eficiente identificar itens de dados eliminados. Atualmente, a política de suporte única é a `Soft Delete` política (eliminação está marcada com um sinalizador de algum tipo), que é especificado da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se estiver a utilizar uma consulta personalizada, certifique-se de que a propriedade referenciados por `softDeleteColumnName` está projetada pela consulta.

O exemplo seguinte cria uma origem de dados com uma política de eliminação de forma recuperável:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Passos seguintes
Parabéns! Aprendeu como integrar o Azure Cosmos DB com o Azure Search com um indexador.

* Para saber mais sobre o Azure Cosmos DB, veja a [página do serviço do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para saber mais sobre o Azure Search, consulte a [página do serviço de pesquisa](https://azure.microsoft.com/services/search/).
