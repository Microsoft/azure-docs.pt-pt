---
title: Pausa, retoma, escala com APIs REST
description: Gerencie a potência da computação no armazém de dados da Azure Synapse Analytics através de APIs REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4e3435fdaa505a73abf96b9463b061c623c192ad
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381066"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>APIs REST para Armazém de Dados Azure SQL
REST APIs para gestão de computação em Armazém de dados Azure Synapse Analytics.

## <a name="scale-compute"></a>Dimensionar computação
Para alterar as unidades de armazém de dados, utilize a API DE REPOUSAÇÃO ou Base de Dados de [ATUALIZAÇÃO ou ATUALIZAÇÃO.](/rest/api/sql/databases/createorupdate) O exemplo seguinte define as unidades de armazém de dados para DW1000 para a base de dados MySQLDW, que está hospedada no servidor MyServer. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

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

Para fazer uma pausa numa base de dados, utilize a Base de Dados de [Pausa](/rest/api/sql/databases/pause) REST API. O exemplo seguinte faz uma pausa numa base de dados chamada Database02 hospedada num servidor chamado Server01. O servidor está num grupo de recursos Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Retomar a computação

Para iniciar uma base de dados, utilize a Base de [Dados de Resumo](/rest/api/sql/databases/resume) REST API. O exemplo seguinte inicia uma base de dados chamada Database02 hospedada num servidor chamado Server01. O servidor está num grupo de recursos Azure chamado ResourceGroup1. 

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

