---
title: Listar recipientes API SQL restauradores em Azure Cosmos DB usando REST API
description: Mostre o feed do evento de todas as mutações feitas em todos os recipientes Azure Cosmos DB SQL sob uma base de dados específica. Isto ajuda no cenário em que o contentor foi acidentalmente apagado.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: bba00b10d1a24dc29c1e986de1f0144c00350f5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537429"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Listar recipientes API SQL restauradores em Azure Cosmos DB usando REST API

> [!IMPORTANT]
> A funcionalidade de restauro pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mostre o feed do evento de todas as mutações feitas em todos os recipientes Azure Cosmos DB SQL sob uma base de dados específica. Isto ajuda no cenário em que o contentor foi acidentalmente apagado. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Com parâmetros opcionais:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>Parâmetros URI

| Name | Em | Necessário | Tipo | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | Verdadeiro |string| O exemploId GUID de uma conta de base de dados restauradora. |
| **localização** | caminho | Verdadeiro | string| Região DB Azure Cosmos, com espaços entre palavras e cada palavra capitalizada. |
| **subscriptionId** | caminho | Verdadeiro | string| A identificação da assinatura do alvo. |
| **versão api** | consulta | Verdadeiro | string | A versão API a ser utilizada para esta operação. |
| **restauradorSqlDatabaseRid** | consulta | |string| O ID de recursos da base de dados SQL. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Description |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| A operação foi concluída com sucesso. |
| Outros Códigos de Estado | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro descrevendo por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Pedido de amostra**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Resposta de amostra**

Código de estado: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição| | --- || --- | | [| de contentores](#container) Objeto de recurso de contentor Azure Cosmos DB SQL | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [| de resposta de erros](#errorresponse) Resposta de erro. | | [OperaçãoType](#operationtype) | Enum para indicar o tipo de operação do evento. | | [| de recursos](#resource) O recurso de um evento de contentores Azure Cosmos DB SQL | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Um evento de contentores Azure Cosmos DB SQL | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | As propriedades de um evento de contentores Azure Cosmos DB SQL| | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | A resposta de operação list, que contém os eventos de contentores SQL e suas propriedades. |

### <a name="container"></a><a id="container"></a>Contentor

Objeto de recurso de contentor Azure Cosmos DB SQL

| **Nome**  |  **Tipo**  |  **Descrição** | | --- || --- | ---| | _etag || Uma propriedade gerada pelo sistema que representa o etag de recursos necessário para um controlo otimista da concordância. | | _rid || Uma propriedade gerada pelo sistema. Um identificador único. | | _self || Uma propriedade gerada pelo sistema que especifica o caminho endereçado do recurso do contentor. | | _ts | | Uma propriedade gerada pelo sistema que denota o último timetamp atualizado do recurso. | | ID || Nome do recipiente Azure Cosmos DB SQL |

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
| Criar |string|evento de criação de contentores|
| Eliminar |string|evento de eliminação de contentores|
| Substituir |string|evento de modificação de contentores|
| Funcionação do Sistema |string|evento de modificação do contentor desencadeado pelo sistema. Este evento não é iniciado pelo utilizador|

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um evento de contentores Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador único. |
| contentor |[Container](#container) (Contentor)| Objeto de recurso de contentor Azure Cosmos DB SQL |
| eventTimestamp |string| A hora em que este evento de contentores aconteceu. |
| operationType |[Tipo de Operação](#operationtype)| O tipo de operação deste evento de contentores. |
| proprietárioId |string| O nome do contentor SQL. |
| proprietárioResourceId |string| O ID de recursos do contentor SQL. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Um evento de contentores Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | ---
| ID |string| O identificador de recursos único do recurso Azure Resource Manager. |
| name |string| O nome do recurso Azure Resource Manager. |
| propriedades |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| As propriedades de um evento de contentores SQL. |
| tipo |string| O tipo de recurso Azure. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

As propriedades de um evento de contentores Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso |[Recurso](#resource)| O recurso de um evento de contentores Azure Cosmos DB SQL |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

A resposta de operação list, que contém os eventos de contentores SQL e suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Lista de eventos de contentores SQL e suas propriedades. |

## <a name="next-steps"></a>Passos seguintes

* [Liste bases de dados restauradoras](restorable-mongodb-databases-list.md)  em Azure Cosmos DB API para MongoDB usando REST API.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.