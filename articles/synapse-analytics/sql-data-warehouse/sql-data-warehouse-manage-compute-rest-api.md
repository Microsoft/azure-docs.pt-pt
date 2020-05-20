---
title: Pausa, retoma, escala com APIs REST
description: Gerencie a potência da computação no armazém de dados da Azure Synapse Analytics através de APIs REST.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 65161915a63b58ad75ff2c01c16c95dec1aad2ff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652614"
---
# <a name="rest-apis-for-azure-synapse-analytics-data-warehouse"></a>REST APIs para Azure Synapse Analytics data warehouse

REST APIs para gestão de computação em Armazém de dados Azure Synapse Analytics.

## <a name="scale-compute"></a>Dimensionar computação

Para alterar as unidades de armazém de dados, utilize a API DE REPOUSAÇÃO ou Base de Dados de [ATUALIZAÇÃO ou ATUALIZAÇÃO.](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) O exemplo seguinte define as unidades de armazém de dados para DW1000 para a base de dados MySQLDW, que está hospedada no servidor MyServer. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Computação de pausa

Para fazer uma pausa numa base de dados, utilize a Base de Dados de [Pausa](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API. O exemplo seguinte faz uma pausa numa base de dados chamada Database02 hospedada num servidor chamado Server01. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Retomar a computação

Para iniciar uma base de dados, utilize a Base de [Dados de Resumo](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) REST API. O exemplo seguinte inicia uma base de dados chamada Database02 hospedada num servidor chamado Server01. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Verifique o estado da base de dados

> [!NOTE]
> Atualmente Verifique o estado da base de dados pode voltar ON-LINE enquanto a base de dados está a completar o fluxo de trabalho online, resultando em erros de ligação. Poderá ser necessário adicionar um atraso de 2 a 3 minutos no seu código de aplicação se estiver a utilizar esta chamada API para desencadear tentativas de ligação.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Obtenha horário de manutenção

Verifique o horário de manutenção que foi definido para um armazém de dados.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Definir o horário de manutenção

Para definir e atualizar um calendário de manutenção num armazém de dados existente.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [Gerir a computação](sql-data-warehouse-manage-compute-overview.md).
