---
title: Exemplo do PowerShell - Atualizar o esquema de sincronização da Sincronização de Dados SQL | Microsoft Docs
description: Script de exemplo do Azure PowerShell para atualizar o esquema de sincronização da Sincronização de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 7a83153d4439fe25d92f149b7efe5231151b4dc4
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390729"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Utilizar o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente

Este exemplo do PowerShell atualiza o esquema de sincronização num grupo de sincronização da Sincronização de Dados SQL. Este script ajuda-o a atualizar de forma eficiente o esquema de sincronização se estiver a sincronizar vários tablets. Este exemplo demonstra a utilização do script **UpdateSyncSchema**, que está disponível no GitHub como [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo do Azure PowerShell versão 5.7.0 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzureRmAccount` para criar uma ligação com o Azure.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](../sql-database-sync-data.md).

> [!IMPORTANT]
> Sincronização de dados SQL do Azure faz **não** suporta a instância gerida da base de dados SQL do Azure neste momento.

## <a name="sample-script"></a>Script de exemplo

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Exemplo 1 - Adicionar todas as tabelas ao esquema de sincronização

O exemplo seguinte atualiza o esquema da base de dados e adiciona todas as tabelas válidas na base de dados de hub ao esquema de sincronização.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Exemplo 2 - Adicionar e remover tabelas e colunas

O exemplo seguinte adiciona `[dbo].[Table1]` e `[dbo].[Table2].[Column1]` ao esquema de sincronização e remove `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parâmetros do script

O script **UpdateSyncSchema** tem os seguintes parâmetros:

| Parâmetro | Notas |
|---|---|
| $SubscriptionId | A subscrição na qual o grupo de sincronização é criado. |
| $ResourceGroupName | O grupo de recursos no qual o grupo de sincronização é criado.|
| $ServerName | O nome do servidor da base de dados de hub.|
| $DatabaseName | O nome da base de dados de hub. |
| $SyncGroupName | O nome do grupo de sincronização. |
| $MemberName | Indique o nome do membro se quiser carregar o esquema da base de dados a partir do membro de sincronização em vez da base de dados de hub. Se pretender carregar o esquema da base de dados do hub, deixe este parâmetro em branco. |
| $TimeoutInSeconds | O tempo limite para a atualização do esquema da base de dados por parte do script. A predefinição são 900 segundos. |
| $RefreshDatabaseSchema | Especifique se o script tem de atualizar o esquema da base de dados. Se o esquema da base de dados tiver mudado da configuração anterior (se, por exemplo, tiver adicionado uma tabela ou uma coluna nova), tem de atualizá-lo antes de poder reconfigurá-lo. A predefinição é falso. |
| $AddAllTables | Se este valor for verdadeiro, todas as tabelas e colunas válidas são adicionadas ao esquema de sincronização. Os valores de $TablesAndColumnsToAdd e $TablesAndColumnsToRemove são ignorados. |
| $TablesAndColumnsToAdd | Especifique as tabelas ou colunas que vão ser adicionadas ao esquema de sincronização. O nome de cada tabela ou coluna tem de ser totalmente delimitado pelo nome do esquema. Por exemplo: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Podem ser especificados vários nomes de tabelas ou colunas, separados por vírgulas (,). |
| $TablesAndColumnsToRemove | Especifique as tabelas ou colunas que vão ser removidas do esquema de sincronização. O nome de cada tabela ou coluna tem de ser totalmente delimitado pelo nome do esquema. Por exemplo: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Podem ser especificados vários nomes de tabelas ou colunas, separados por vírgulas (,). |
|||

## <a name="script-explanation"></a>Explicação do script

O script **UpdateSyncSchema** utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Devolve informações sobre um grupo de sincronizações. |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Atualiza um grupo de sincronização. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Devolve informações sobre um membro de sincronização. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Devolve informações sobre um esquema de sincronização. |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Atualiza um esquema de sincronização. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../sql-database-powershell-samples.md).

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Descrição geral - [sincronizar dados em várias bases de dados na cloud e no local com sincronização de dados SQL do Azure](../sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal - [Tutorial: Configurar a sincronização de dados SQL para sincronizar dados entre a base de dados do Azure SQL e SQL Server no local](../sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](sql-database-sync-data-between-azure-onprem.md)
-   Agente de - de sincronização de dados [agente de sincronização de dados SQL do Azure de sincronização de dados](../sql-database-data-sync-agent.md)
-   Melhores práticas - [melhores práticas para a sincronização de dados SQL do Azure](../sql-database-best-practices-data-sync.md)
-   Monitor de - [monitorizar a sincronização de dados SQL com o Log Analytics](../sql-database-sync-monitor-oms.md)
-   Resolução de problemas - [solucionar problemas com a sincronização de dados SQL do Azure](../sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL - [automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure](../sql-database-update-sync-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

-   [Descrição Geral da Base de Dados SQL](../sql-database-technical-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
