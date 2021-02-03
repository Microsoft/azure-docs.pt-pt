---
title: Listar contas de base de dados ressuráveis usando Azure Cosmos DB REST API
description: Lista todas as contas de base de dados DB do Azure Cosmos ressurráveis disponíveis numa subscrição.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 442f7e3abdf065377c78f71f003733ee295b312a
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527895"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Listar contas de base de dados ressuráveis usando Azure Cosmos DB REST API

Lista todas as contas de base de dados DB do Azure Cosmos ressurráveis disponíveis na subscrição. Esta chamada requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` permissão.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>Parâmetros URI

| Name | Em | Necessário | Tipo | Description |
| --- | --- | --- | --- | --- |
| **subscriptionId** | caminho | Verdadeiro | string| A identificação da assinatura do alvo. |
| **versão api** | consulta | Verdadeiro | string | A versão API a ser utilizada para esta operação. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Description |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| A operação foi concluída com sucesso. |
| Outros Códigos de Estado | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro descrevendo por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Pedido de amostra**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Resposta de amostra**

Código de estado: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

|Definição  | Description|
| --- | --- |
| [ApiType](#apitype) | Enum para indicar o tipo API da conta de base de dados restauradora. |
| [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. |
| [Resposta de erros](#errorresponse) | Resposta de erro. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Uma conta de base de dados Azure Cosmos DB. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | A resposta da operação List, que contém as contas de base de dados restauradoras e as suas propriedades. |
| [RestauradorLocationResource](#restorablelocationresource) | Propriedades da conta de restauração regional. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enum para indicar o tipo API da conta de base de dados restauradora.

| **Nome** | **Tipo** |
| --- | --- |
| Cassandra |string|
| Gremlin |string|
| GremlinV2 |string|
| MongoDB |string|
| SQL |string|
| Tabela |string|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Uma conta de base de dados Azure Cosmos DB.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| ID |string| O identificador de recursos único do recurso Azure Resource Manager. |
| localização |string| A localização do grupo de recursos a que pertence o recurso. |
| name |string| O nome do recurso Gestor de Recursos. |
| propriedades.contaName |string| O nome da conta global da base de dados |
| propriedades.apiType |[ApiType](#apitype)| O tipo API da conta de base de dados restauradora. |
| propriedades.creationTime |string| O tempo de criação da conta de base de dados restauradora (formato ISO-8601). |
| propriedades.exclusãoTime |string| O momento em que a conta de base de dados restauradora foi eliminada (formato ISO-8601). |
| propriedades.Restaurações restauradoras |[RestauradorLocationResource](#restorablelocationresource)[]| Lista das regiões a partir das quais a conta de base de dados pode ser restaurada. |
| tipo |string| O tipo de recurso Azure. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

A resposta de operação lista que contém as contas de base de dados ressoáveis e as suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| Lista de contas de base de dados restauradoras e suas propriedades. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestauradorLocationResource

Propriedades da conta de restauração regional.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| criaçãoTime |string| O tempo de criação da conta regional de bases de dados restauradora (formato ISO-8601). |
| eliminaçãoTime |string| O momento em que a conta regional de bases de dados restauradora foi eliminada (formato ISO-8601). |
| localizaçãoName |string| A localização da conta restauradora regional. |
| regionalDatabaseAccountInstanceId |string| A identificação da conta de restauro regional. |

## <a name="next-steps"></a>Passos seguintes

* [Contas de base de dados ressoáveis - Lista por localização.](restorable-database-accounts-list-by-location.md)
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.