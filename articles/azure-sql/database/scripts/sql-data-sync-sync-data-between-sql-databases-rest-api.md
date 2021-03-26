---
title: 'REST API: Sincronização entre várias bases de dados'
description: Utilize um script de exemplo de API REST para sincronizar entre várias bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d3ff8114c11b224a0bdbb0bd2d0e5686a7e57b55
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565916"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Use a API REST para sincronizar dados entre várias bases de dados 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de API REST configura o SQL Data Sync para sincronizar dados entre várias bases de dados.

Para obter uma visão geral do SQL Data Sync, consulte [os dados do Sync em várias bases de dados de nuvem e no local com SQL Data Sync em Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O SQL Data Sync não suporta a Azure SQL Managed Instance neste momento.

## <a name="create-sync-group"></a>Criar grupo de sincronização

Utilize o modelo [de criação ou atualização](/rest/api/sql/syncgroups/createorupdate) para criar um grupo de sincronização.
 
Ao criar um grupo de sincronização, não passe no esquema de sincronização (tabela\coluna) e não passe no masterSyncMemberName, porque neste momento o grupo de sincronização ainda não tem informações sobre a coluna\coluna.

Pedido de amostra para a criação de um grupo de sincronização: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Resposta da amostra para criar um grupo de sincronização:

Código de estado: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Código de estado: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Criar membro sincronizado

Utilize o modelo [de criação ou atualização](/rest/api/sql/syncmembers/createorupdate) para criar um membro sincronizado.

Pedido de amostra para a criação de um membro sincronizado:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Resposta da amostra para criar um membro sincronizado:

Código de estado:200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Código de estado:201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Atualizar esquema

Uma vez criado o seu grupo de sincronização com sucesso, refresque o esquema utilizando os seguintes modelos.

Utilize o modelo [de esquema do hub de atualização](/rest/api/sql/syncgroups/refreshhubschema)  para refrescar o esquema para a base de dados do hub. 

Pedido de amostra para refrescar um esquema de base de dados de hub: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Resposta da amostra para refrescar um esquema de base de dados de hub: 

Código de estado: 200

Código de estado: 202

Utilize o modelo [de esquemas de hub de lista](/rest/api/sql/syncgroups/listhubschemas) para listar o esquema da base de dados do hub. 

Utilize o modelo [de esquema de membro de atualização](/rest/api/sql/syncmembers/refreshmemberschema) para atualizar o esquema da base de dados dos membros. 

Utilize o modelo [de esquema de membro](/rest/api/sql/syncmembers/listmemberschemas) da lista para listar o esquema da base de dados dos membros. 

Apenas avance para o próximo passo uma vez que o seu esquema refresque com sucesso. 

## <a name="update-sync-group"></a>Atualizar grupo de sincronização 

Utilize o modelo [de criação ou atualização](/rest/api/sql/syncgroups/createorupdate) para atualizar o seu grupo de sincronização.

Atualizar o grupo de sincronização especificando o esquema de sincronização. Inclua o seu esquema e masterSyncMemberName, que é o nome que detém o esquema que pretende usar. 

Pedido de amostra para atualização do grupo de sincronização: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Resposta da amostra para atualizar o grupo de sincronização: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Membro de sincronização de atualização

Utilize o modelo [de criação ou atualização](/rest/api/sql/syncmembers/createorupdate) para atualizar o seu membro sincronizado.

Pedido de amostra para atualizar um membro sincronizado: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Resposta da amostra para atualizar um membro sincronizado: 

Código de estado: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Código de estado: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Sincronização do gatilho

Utilize o modelo [de sincronização do gatilho](/rest/api/sql/syncgroups/triggersync) para desencadear uma operação de sincronização.

Pedido de amostra para desencadear a operação de sincronização: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Resposta da amostra para desencadear a operação de sincronização: 

Código de estado: 200

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../powershell-script-content-guide.md).

Para obter mais informações sobre o SQL Data Sync, consulte:

- Visão geral - [Sync dados em várias bases de dados de nuvem e no local com SQL Data Sync em Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Configurar o Data Sync
    - Utilize o portal Azure - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o SqL Server](../sql-data-sync-sql-server-configure.md)
    - Use PowerShell - [Use PowerShell para sincronizar dados entre uma base de dados na Base de Dados Azure SQL e no SqL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent - [Data Sync Agent for SQL Data Sync in Azure](../sql-data-sync-agent-overview.md)
- Melhores práticas - [Melhores práticas para SQL Data Sync em Azure](../sql-data-sync-best-practices.md)
- Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](../monitor-tune-overview.md)
- Resolução de problemas - [Problemas de resolução de problemas com SQL Data Sync em Azure](../sql-data-sync-troubleshoot.md)
- Atualizar o esquema de sincronização
    - Utilizar Transact-SQL - [Automatizar a replicação de alterações de esquema no SQL Data Sync em Azure](../sql-data-sync-update-sync-schema.md)
    - Use PowerShell - [Use PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](update-sync-schema-in-sync-group.md)

Para obter mais informações sobre a Base de Dados SQL, consulte:

- [Visão geral da base de dados sql](../sql-database-paas-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))