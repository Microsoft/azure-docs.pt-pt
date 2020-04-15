---
title: Exemplo PowerShell - Update SQL Data Sync schema
description: Script de exemplo do Azure PowerShell para atualizar o esquema de sincronização da Sincronização de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 00f9e217df65838e75f351dfe00fb60594acdcfc
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383499"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Utilizar o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente

Este exemplo do PowerShell atualiza o esquema de sincronização num grupo de sincronização da Sincronização de Dados SQL. Este script ajuda-o a atualizar de forma eficiente o esquema de sincronização se estiver a sincronizar vários tablets. Este exemplo demonstra a utilização do script **UpdateSyncSchema**, que está disponível no GitHub como [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer AZ PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](../sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure SQL Data Sync não suporta a instância gerida pela base de dados Azure SQL neste momento.

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
| $refreshDatabaseSchema | Especifique se o script tem de atualizar o esquema da base de dados. Se o esquema da base de dados tiver mudado da configuração anterior (se, por exemplo, tiver adicionado uma tabela ou uma coluna nova), tem de atualizá-lo antes de poder reconfigurá-lo. A predefinição é falso. |
| $addAllTables | Se este valor for verdadeiro, todas as tabelas e colunas válidas são adicionadas ao esquema de sincronização. Os valores de $TablesAndColumnsToAdd e $TablesAndColumnsToRemove são ignorados. |
| $tablesAndColumnsToAdd | Especifique as tabelas ou colunas que vão ser adicionadas ao esquema de sincronização. O nome de cada tabela ou coluna tem de ser totalmente delimitado pelo nome do esquema. Por exemplo: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Podem ser especificados vários nomes de tabelas ou colunas, separados por vírgulas (,). |
| $tablesAndColumnsToRemove | Especifique as tabelas ou colunas que vão ser removidas do esquema de sincronização. O nome de cada tabela ou coluna tem de ser totalmente delimitado pelo nome do esquema. Por exemplo: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Podem ser especificados vários nomes de tabelas ou colunas, separados por vírgulas (,). |

## <a name="script-explanation"></a>Explicação do script

O script **UpdateSyncSchema** utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Devolve informações sobre um grupo de sincronizações. |
| [Atualização-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Atualiza um grupo de sincronização. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Devolve informações sobre um membro de sincronização. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Devolve informações sobre um esquema de sincronização. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Atualiza um esquema de sincronização. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

As amostras adicionais de script powerShell da base de dados SQL podem ser encontradas em scripts PowerShell de base de [dados Azure SQL](../sql-database-powershell-samples.md).

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

- Visão geral - Sync dados em várias bases de dados de nuvem e no local com O Sincronizado de [Dados Azure SQL](../sql-database-sync-data.md)
- Configurar o Sincronizado de Dados
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o Servidor SQL no local](../sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        - [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](sql-database-sync-data-between-sql-databases.md)
        - [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](sql-database-sync-data-between-azure-onprem.md)
- Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](../sql-database-data-sync-agent.md)
- Boas práticas - [Boas práticas para O Sincronização de Dados Azure SQL](../sql-database-best-practices-data-sync.md)
- Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](../sql-database-sync-monitor-oms.md)
- Troubleshoot - [Problemas com O Sincronizado de Dados Azure SQL](../sql-database-troubleshoot-data-sync.md)
- Atualizar o esquema de sincronização
    - Com a Transact-SQL - [Automatizar a replicação de alterações de esquemas no Azure SQL Data Sync](../sql-database-update-sync-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

- [Descrição Geral da Base de Dados SQL](../sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
