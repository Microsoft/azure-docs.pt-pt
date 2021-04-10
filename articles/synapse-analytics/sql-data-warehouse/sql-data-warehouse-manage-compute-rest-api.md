---
title: Pausa, currículo, escala com APIs REST para piscina SQL dedicada (anteriormente SQL DW)
description: Gerir a potência de computação para piscinas SQL dedicadas (anteriormente SQL DW) em Azure Synapse Analytics através de REST APIs.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375906"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>REST APIs para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

REST APIs para gerir o cálculo para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics.

## <a name="scale-compute"></a>Dimensionar computação

Para alterar as unidades de armazém de dados, utilize a API [de Base de Dados de Criação ou Atualização.](/rest/api/sql/databases/createorupdate) O exemplo a seguir define as unidades de armazém de dados para DW1000 para a base de dados MySQLDW, que está hospedada no servidor MyServer. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>Cálculo de pausa

Para fazer uma pausa numa base de dados, utilize a API [de Base de Dados de Pausa.](/rest/api/sql/databases/pause) O exemplo seguinte faz uma pausa numa base de dados chamada Database02 hospedada num servidor chamado Server01. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Retomar o cálculo

Para iniciar uma base de dados, utilize a API [de Resequim base de dados](/rest/api/sql/databases/resume) de resumo. O exemplo a seguir inicia uma base de dados chamada Database02 hospedada num servidor chamado Server01. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Verifique o estado da base de dados

> [!NOTE]
> Atualmente Verifique o estado da base de dados pode voltar ONLINE enquanto a base de dados estiver a completar o fluxo de trabalho online, resultando em erros de ligação. Pode ter de adicionar um atraso de 2 a 3 minutos no seu código de aplicação se estiver a utilizar esta chamada API para desencadear tentativas de ligação.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Obtenha horário de manutenção

Verifique o calendário de manutenção que foi definido para uma piscina SQL dedicada (anteriormente SQL DW).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Definir horário de manutenção

Para definir e atualizar um calendário de manutenção numa piscina SQL dedicada existente (anteriormente SQL DW).

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [Manage compute](sql-data-warehouse-manage-compute-overview.md).
