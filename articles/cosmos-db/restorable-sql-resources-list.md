---
title: Listar recursos de API SQL restauradores em Azure Cosmos DB usando REST API
description: Devolva uma lista de dados de dados e combinação de contentores que existam na conta no momento e localização. Isto ajuda em cenários para validar que recursos existem em determinado horário de tempo e localização.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: e31423ae5afd4955ebe7acf1d456496f15f14a6b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537294"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Listar recursos de API SQL restauradores em Azure Cosmos DB usando REST API

> [!IMPORTANT]
> A funcionalidade de restauro pontual (modo de backup contínuo) para Azure Cosmos DB está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Devolva uma lista de dados de dados e combinação de contentores que existam na conta no momento e localização. Isto ajuda em cenários para validar que recursos existem em determinado horário de tempo e localização. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Com parâmetros opcionais:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
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
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| A operação foi concluída com sucesso. |
| Outros Códigos de Estado | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro descrevendo por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Pedido de amostra**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Resposta de amostra**

Código de estado: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
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

| Definição | Descrição| | --- || --- | | [Data de dadosRestoreResource](#databaserestoreresource) | Bases de dados específicas para restaurar. | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [| de resposta de erros](#errorresponse) Resposta de erro. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | A resposta de operação list, que contém os recursos SQL restauradores. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

A resposta de operação list, que contém os recursos SQL restauradores.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[Base de dadosRestoreResource](#databaserestoreresource)[]| Lista de recursos SQL restauradores, incluindo a base de dados e nomes de recolha. |

## <a name="next-steps"></a>Passos seguintes

* [Listar bases de dados restauradoras](restorable-sql-databases-list.md) em Azure Cosmos SQL API usando REST API.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.