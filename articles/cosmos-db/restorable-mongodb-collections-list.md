---
title: List coleções restauradoras em Azure Cosmos DB MongoDB API - REST API
description: Mostre o feed do evento de todas as mutações feitas em todas as coleções Azure Cosmos DB MongoDB sob uma base de dados específica. Isto ajuda no cenário em que o contentor foi acidentalmente apagado.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 83e1c7c27e8c5d179c4ec6aa4ba64b3367294a48
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527940"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Liste coleções restauradoras em Azure Cosmos DB API para MongoDB usando REST API

Mostre o feed do evento de todas as mutações feitas em todas as coleções Azure Cosmos DB para coleções MongoDB sob uma base de dados específica. Isto ajuda no cenário em que o contentor foi acidentalmente apagado. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Com parâmetros opcionais:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>Parâmetros URI

| Name | Em | Necessário | Tipo | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | Verdadeiro |string| O exemploId GUID de uma conta de base de dados restauradora. |
| **localização** | caminho | Verdadeiro | string| Região DB Azure Cosmos, com espaços entre palavras e cada palavra capitalizada. |
| **subscriptionId** | caminho | Verdadeiro | string| A identificação da assinatura do alvo. |
| **versão api** | consulta | Verdadeiro | string | A versão API a ser utilizada para esta operação. |
| **restauradorMongodbDatabaseRid** | consulta | |string| O ID de recursos da Azure Cosmos DB API para a base de dados MongoDB. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| A operação foi concluída com sucesso. |
| Outros Códigos de Estado | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro descrevendo por que a operação falhou.|

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Pedido de amostra**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Resposta de amostra**

Código de estado: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

|Definição  | Description|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. |
| [Resposta de erros](#errorresponse) | Resposta de erro. |
| [Tipo de Operação](#operationtype) | Enum para indicar o tipo de operação do evento. |
| [Recurso](#resource) | O recurso de um Azure Cosmos DB API para evento de coleção MongoDB |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Um Azure Cosmos DB API para evento de coleção MongoDB |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | As propriedades de um Azure Cosmos DB API para evento de coleção MongoDB |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | A resposta da operação List, que contém os eventos de recolha e as suas propriedades. |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Uma resposta de erro do serviço.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| erro | [Resposta de erros](#errorresponse)| Resposta de erro. |

### <a name="errorresponse"></a><a id="errorresponse"></a>Resposta de erros

Resposta de erro.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| code |string| Código de erro. |
| message |string| Mensagem de erro indicando por que a operação falhou. |

### <a name="operationtype"></a><a id="operationtype"></a>Tipo de Operação

Enum para indicar o tipo de operação do evento.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Criar |string|evento de criação de coleção|
| Eliminar |string|evento de eliminação de coleção|
| Substituir |string|evento de modificação de coleção|

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um evento de coleção Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador único. |
| eventTimestamp |string| A hora em que este evento de coleção aconteceu. |
| operationType |[Tipo de Operação](#operationtype)|  O tipo de operação deste evento de recolha. |
| proprietárioId |string| O nome da coleção MongoDB.|
| proprietárioResourceId |string| O ID de recursos da coleção MongoDB. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Um evento de coleção Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recursos único do recurso Azure Resource Manager. |
| name |string| O nome do recurso Gestor de Recursos. |
| propriedades |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| As propriedades de um evento de coleção. |
| tipo |string| O tipo de recurso Azure. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

As propriedades de um evento de coleção Azure Cosmos DB MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso | [Recurso](#resource)| O recurso de um Azure Cosmos DB API para evento de coleção MongoDB |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

A resposta da operação List, que contém os eventos de recolha e as suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Lista de Azure Cosmos DB API para eventos de recolha mongoDB e suas propriedades. |

## <a name="next-steps"></a>Passos seguintes

* [Liste bases de dados restauradoras](restorable-mongodb-databases-list.md)  em Azure Cosmos DB API para MongoDB usando REST API.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.