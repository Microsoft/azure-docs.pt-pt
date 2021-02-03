---
title: Listar recursos restauradores em Azure Cosmos DB API para MongoDB usando REST API
description: Devolva uma lista de dados de dados e combo de recolha que existam na conta no determinado horário e localização. Isto ajuda em cenários para validar que recursos existem em determinado horário de tempo e localização.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dc90bfb6325831276b3c6171b73aebfa2877cf68
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527921"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>Listar recursos restauradores em Azure Cosmos DB API para MongoDB usando REST API

Devolva uma lista de dados de dados e combo de recolha que existam na conta no determinado horário e localização. Isto ajuda em cenários para validar que recursos existem em determinado horário de tempo e localização. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

Com parâmetros opcionais:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Parâmetros URI

| Name | Em | Necessário | Tipo | Description |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | Verdadeiro |string| O exemploId GUID de uma conta de base de dados restauradora. |
| **localização** | caminho | Verdadeiro | string| Região DB Azure Cosmos, com espaços entre palavras e cada palavra capitalizada. |
| **subscriptionId** | caminho | Verdadeiro | string| A identificação da assinatura do alvo. |
| **versão api** | consulta | Verdadeiro | string | A versão API a ser utilizada para esta operação. |
| **restaurarLocalização** | consulta | |string| O local onde estão localizados os recursos restauradores. |
| **restaurarTimestampInUtc** | consulta | |string| A marca de tempo quando os recursos restauradores existiam. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Description |
| --- | --- | --- |
| 200 OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| A operação foi concluída com sucesso. |
| Outros Códigos de Estado | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro descrevendo por que a operação falhou. |


## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Pedido de amostra**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Resposta de amostra**

Código de estado: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição| | --- || --- | | [Data de dadosRestoreResource](#databaserestoreresource) | Bases de dados específicas para restaurar. | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [| de resposta de erros](#errorresponse) Resposta de erro. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | A resposta de operação list, que contém os recursos SQL restauradores. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>Base de dadosRestoreResource

Bases de dados específicas para restaurar.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| coleçõesAmmos |corda[]| Os nomes das coleções disponíveis para restauro. |
| base de dados Nome |string| O nome da base de dados disponível para restauro. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

A resposta de operação list, que contém o API API AZURE Cosmos ressarvel para recursos mongoDB.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[Base de dadosRestoreResource](#databaserestoreresource)[]| Lista de Azure Cosmos DB API restauradora para recursos mongoDB, incluindo a base de dados e nomes de recolha. |

## <a name="next-steps"></a>Passos seguintes

* [Liste bases de dados restauradoras](restorable-mongodb-databases-list.md)  em Azure Cosmos DB API para MongoDB usando REST API.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.