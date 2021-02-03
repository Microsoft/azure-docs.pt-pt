---
title: Listar bases de dados restauradoras em Azure Cosmos DB API para MongoDB usando REST API
description: Mostre o feed do evento de todas as mutações feitas em todas as bases de dados Azure Cosmos DB MongoDB sob a conta resprédula. Isto ajuda num cenário em que a base de dados foi acidentalmente eliminada para obter o tempo de eliminação.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 64288f67728e59c32c662a6640d60daf52c53fe1
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527930"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Listar bases de dados restauradoras em Azure Cosmos DB API para MongoDB usando REST API

Mostre o feed do evento de todas as mutações feitas em todas as bases de dados Azure Cosmos DB MongoDB sob a conta resprédula. Isto ajuda num cenário em que a base de dados foi acidentalmente eliminada para obter o tempo de eliminação. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parâmetros URI

| Name | Em | Necessário | Tipo | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | Verdadeiro |string| O exemploId GUID de uma conta de base de dados restauradora. |
| **localização** | caminho | Verdadeiro | string| Região DB Azure Cosmos, com espaços entre palavras e cada palavra capitalizada. |
| **subscriptionId** | caminho | Verdadeiro | string| A identificação da assinatura do alvo. |
| **versão api** | consulta | Verdadeiro | string | A versão API a ser utilizada para esta operação. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| A operação foi concluída com sucesso. |
| Outros Códigos de Estado | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro descrevendo por que a operação falhou.|

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Pedido de amostra**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Resposta de amostra**

Código de estado: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição| | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [| de resposta de erros](#errorresponse) Resposta de erro. | | [OperaçãoType](#operationtype) | Enum para indicar o tipo de operação do evento. | | [| de recursos](#resource) O recurso de um Azure Cosmos DB API para o evento de base de dados mongoDB | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Um AZure Cosmos DB API para o evento de base de dados mongoDB | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | As propriedades de um Azure Cosmos DB API para o evento de base de dados MongoDB | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | A resposta de operação list, que contém a AZure Cosmos DB API para eventos de base de dados MongoDB e suas propriedades. |

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
| Criar |string|evento de criação de base de dados|
| Eliminar |string|evento de eliminação de bases de dados|
| Substituir |string|evento de modificação de base de dados|

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um Azure Cosmos DB API para o evento de base de dados mongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador único. |
| eventTimestamp |string| A hora em que este evento de base de dados aconteceu. |
| operationType |[Tipo de Operação](#operationtype)| O tipo de operação deste evento de base de dados.  |
| proprietárioId |string| O nome da Azure Cosmos DB API para a base de dados MongoDB.|
| proprietárioResourceId |string| O recurso ID Azure Cosmos DB API para a base de dados MongoDB. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Um API AZure Cosmos DB para o evento de base de dados mongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recursos único do recurso Azure Resource Manager. |
| name |string| O nome do recurso Gestor de Recursos. |
| propriedades |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| As propriedades de um Azure Cosmos DB API para evento de base de dados MongoDB. |
| tipo |string| O tipo de recurso Azure. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

As propriedades de um Azure Cosmos DB API para evento de base de dados MongoDB

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso |[Recurso](#resource)| O recurso de um Azure Cosmos DB API para o evento de base de dados mongoDB |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

A resposta da operação list, que contém os eventos da base de dados e as suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Lista de eventos de base de dados e suas propriedades. |

## <a name="next-steps"></a>Passos seguintes

* [Listar recursos restauradores](restorable-mongodb-resources-list.md)  em Azure Cosmos DB API para MongoDB usando REST API.
* [Liste coleções restauradoras](restorable-mongodb-collections-list.md)  em Azure Cosmos DB API para MongoDB usando REST API.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.