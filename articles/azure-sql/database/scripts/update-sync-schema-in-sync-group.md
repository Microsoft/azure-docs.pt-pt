---
title: 'PowerShell: Atualizar o esquema de sincronização de sincronização de sincronização de dados SQL'
description: Script de exemplo do Azure PowerShell para atualizar o esquema de sincronização da Sincronização de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792723"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Utilizar o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo Azure PowerShell atualiza o esquema de sincronização num grupo de sincronização de sincronização de dados SQL existente. Este script ajuda-o a atualizar de forma eficiente o esquema de sincronização se estiver a sincronizar vários tablets. Este exemplo demonstra a utilização do script **UpdateSyncSchema** , que está disponível no GitHub como [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer az PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O SQL Data Sync não suporta a Azure SQL Managed Instance neste momento.

## <a name="examples"></a>Exemplos

### <a name="add-all-tables-to-the-sync-schema"></a>Adicione todas as tabelas ao esquema de sincronização

O exemplo seguinte atualiza o esquema da base de dados e adiciona todas as tabelas válidas na base de dados de hub ao esquema de sincronização.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Adicione e remova tabelas e colunas

O exemplo seguinte adiciona `[dbo].[Table1]` e `[dbo].[Table2].[Column1]` ao esquema de sincronização e remove `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parâmetros do script

O script **UpdateSyncSchema** tem os seguintes parâmetros:

| Parâmetro | Notas |
|---|---|
| $subscriptionId | A subscrição na qual o grupo de sincronização é criado. |
| $resourceGroupName | O grupo de recursos no qual o grupo de sincronização é criado.|
| $serverName | O nome do servidor da base de dados de hub.|
| $databaseName | O nome da base de dados de hub. |
| $syncGroupName | O nome do grupo de sincronização. |
| $memberName | Indique o nome do membro se quiser carregar o esquema da base de dados a partir do membro de sincronização em vez da base de dados de hub. Se pretender carregar o esquema da base de dados do hub, deixe este parâmetro em branco. |
| $timeoutInSeconds | O tempo limite para a atualização do esquema da base de dados por parte do script. A predefinição são 900 segundos. |
| $refreshDatabaseSchema | Especifique se o script tem de atualizar o esquema da base de dados. Se o seu esquema de base de dados mudou da configuração anterior (por exemplo, se tiver adicionado uma nova tabela ou uma nova coluna), terá de atualizar o esquema antes de o reconfigurar. A predefinição é falso. |
| $addAllTables | Se este valor for verdadeiro, todas as tabelas e colunas válidas são adicionadas ao esquema de sincronização. Os valores de $TablesAndColumnsToAdd e $TablesAndColumnsToRemove são ignorados. |
| $tablesAndColumnsToAdd | Especifique as tabelas ou colunas que vão ser adicionadas ao esquema de sincronização. O nome de cada tabela ou coluna tem de ser totalmente delimitado pelo nome do esquema. Por exemplo: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabelas ou colunas podem ser especificados e separados por uma vírgula (,). |
| $tablesAndColumnsToRemove | Especifique as tabelas ou colunas que vão ser removidas do esquema de sincronização. O nome de cada tabela ou coluna tem de ser totalmente delimitado pelo nome do esquema. Por exemplo: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabelas ou colunas podem ser especificados e separados por uma vírgula (,). |

## <a name="script-explanation"></a>Explicação do script

O script **UpdateSyncSchema** utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Grupo Get-AzSqlSync](/powershell/module/az.sql/get-azsqlsyncgroup) | Devolve informações sobre um grupo de sincronização. |
| [Atualização-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | Atualiza um grupo de sincronização. |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | Devolve informações sobre um membro sincronizado. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | Devolve informações sobre um esquema de sincronização. |
| [Atualização-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | Atualiza um esquema de sincronização. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../powershell-script-content-guide.md).

Para obter mais informações sobre o SQL Data Sync, consulte:

- Visão geral - [Sincronizar dados entre a Base de Dados Azure SQL e o SQL Server com SQL Data Sync em Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Configurar o Data Sync
    - Utilize o portal Azure - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o SqL Server](../sql-data-sync-sql-server-configure.md)
    - Utilizar o PowerShell
        -  [Utilize o PowerShell para sincronizar dados entre várias bases de dados na Base de Dados Azure SQL](sql-data-sync-sync-data-between-sql-databases.md)
        -  [Utilize o PowerShell para sincronizar dados entre a Base de Dados Azure SQL e o SqL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent - [Data Sync Agent for SQL Data Sync in Azure](../sql-data-sync-agent-overview.md)
- Melhores práticas - [Melhores práticas para SQL Data Sync em Azure](../sql-data-sync-best-practices.md)
- Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](../monitor-tune-overview.md)
- Resolução de problemas - [Problemas de resolução de problemas com SQL Data Sync em Azure](../sql-data-sync-troubleshoot.md)
- Atualizar o esquema de sincronização
    - Utilizar Transact-SQL - [Automatizar a replicação de alterações de esquema no SQL Data Sync em Azure](../sql-data-sync-update-sync-schema.md)

Para obter mais informações sobre a Base de Dados SQL, consulte:

- [Visão geral da base de dados sql](../sql-database-paas-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))