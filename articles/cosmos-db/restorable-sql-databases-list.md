---
title: Listar bases de dados de API API de SQL restauradoras em Azure Cosmos DB usando REST API
description: Mostre o feed do evento de todas as mutações feitas em todas as bases de dados Azure Cosmos DB SQL sob a conta restauradora. Isto ajuda num cenário em que a base de dados foi acidentalmente eliminada para obter o tempo de eliminação.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: d3d72cff5fcfeed17d60e2f856be4adc1a983819
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527774"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Listar bases de dados de API API de SQL restauradoras em Azure Cosmos DB usando REST API

Mostre o feed do evento de todas as mutações feitas em todas as bases de dados Azure Cosmos DB SQL sob a conta restauradora. Isto ajuda num cenário em que a base de dados foi acidentalmente eliminada para obter o tempo de eliminação. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| A operação foi concluída com sucesso. |
| Outros Códigos de Estado | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro descrevendo por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Pedido de Amostra**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Resposta de amostra**

Código de estado: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição| | --- || --- | | [| de base de dados](#database) Objeto de recurso de base de dados Azure Cosmos DB SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [| de resposta de erros](#errorresponse) Resposta de erro. | | [OperaçãoType](#operationtype) | Enum para indicar o tipo de operação do evento. | | [| de recursos](#resource) O recurso de um evento de base de dados Azure Cosmos DB SQL | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Um evento de base de dados Azure Cosmos DB SQL | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | As propriedades de um evento de base de dados Azure Cosmos DB SQL | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | A resposta de operação list, que contém os eventos de base de dados SQL e suas propriedades. |

### <a name="database"></a><a id="database"></a>Base de dados

Objeto de recurso de base de dados Azure Cosmos DB SQL

| **Nome**  |  **Tipo**  |  **Descrição** | | --- || --- | ---| | _colls || Uma propriedade gerada pelo sistema que especificou o caminho endereçado do recurso de recolha. | | _etag || Uma propriedade gerada pelo sistema que representa o etag de recursos necessário para um controlo otimista da concordância. | | _rid || Uma propriedade gerada pelo sistema. Um identificador único. | | _self || Uma propriedade gerada pelo sistema que especifica o caminho endereçado do recurso de base de dados. | | _ts | | Uma propriedade gerada pelo sistema que denota o último timetamp atualizado do recurso. | | _users || Uma propriedade gerada pelo sistema que especifica o caminho endereçado do recurso do utilizador. | | ID || Nome da base de dados Azure Cosmos DB SQL |

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
| Funcionação do Sistema |string|evento de modificação da base de dados desencadeado pelo sistema. Este evento não é iniciado pelo utilizador|

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um evento de base de dados Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador único. |
| base de dados |[Base de dados](#database)| Objeto de recurso de base de dados Azure Cosmos DB SQL |
| eventTimestamp |string| A hora em que este evento de base de dados aconteceu. |
| operationType |[Tipo de Operação](#operationtype)| O tipo de operação deste evento de base de dados. |
| proprietárioId |string| O nome da base de dados SQL. |
| proprietárioResourceId |string| O ID de recursos da base de dados SQL. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Um evento de base de dados Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recursos único do recurso Azure Resource Manager. |
| name |string| O nome do recurso Azure Resource Manager. |
| propriedades | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| As propriedades de um evento de base de dados SQL. |
| tipo |string| O tipo de recurso Azure. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

As propriedades de um evento de base de dados Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso |[Recurso](#resource)| O recurso de um evento de base de dados Azure Cosmos DB SQL |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

A resposta de operação list, que contém os eventos de base de dados SQL e suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Lista de eventos de base de dados SQL e suas propriedades. |

## <a name="next-steps"></a>Passos seguintes

* [Listar recipientes restauradores](restorable-sql-containers-list.md) em Azure Cosmos DB SQL API utilizando REST API.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.