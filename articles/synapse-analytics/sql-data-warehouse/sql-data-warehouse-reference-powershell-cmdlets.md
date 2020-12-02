---
title: PowerShell & REST APIs para piscina SQL dedicada (anteriormente SQL DW)
description: Top PowerShell cmdlets para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics, incluindo como parar e retomar uma base de dados.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: bb26ac0071a50095f8e93ce0cc25da0055bdbac8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448227"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>PowerShell & REST APIs para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics 

Muitas tarefas administrativas dedicadas ao pool SQL podem ser geridas usando cmdlets Azure PowerShell ou REST APIs.  Abaixo estão alguns exemplos de como usar comandos PowerShell para automatizar tarefas comuns na sua piscina SQL dedicada (anteriormente SQL DW).  Para alguns bons exemplos DE REST, consulte o artigo [Gerir a escalabilidade com REST.](sql-data-warehouse-manage-compute-rest-api.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Começa com os cmdlets Azure PowerShell

1. Abra o Windows PowerShell.
2. Na solicitação do PowerShell, execute estes comandos para iniciar seduca no Azure Resource Manager e selecione a sua subscrição.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Pausa no exemplo do armazém de dados

Faça uma pausa numa base de dados chamada "Database02" hospedada num servidor chamado "Server01".  O servidor está num grupo de recursos Azure chamado "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Uma variação, este exemplo canaliza o objeto recuperado para [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Como resultado, a base de dados está em pausa. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Inicie o exemplo do armazém de dados

Retomar o funcionamento de uma base de dados chamada "Base de Dados02" hospedada num servidor chamado "Server01". O servidor está contido num grupo de recursos chamado "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo recupera uma base de dados chamada "Database02" de um servidor chamado "Server01" que está contido num grupo de recursos chamado "ResourceGroup1". Canaliza o objeto recuperado para [a Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Note que se o seu servidor for foo.database.windows.net, utilize "foo" como nome -Server nos cmdlets PowerShell.

## <a name="other-supported-powershell-cmdlets"></a>Outros cmdlets PowerShell suportados

Estes cmdlets PowerShell são suportados com armazém de dados Azure Synapse Analytics.

* [Base de Dados Get-AzSql](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Currículo-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Passos seguintes

Para mais exemplos powerShell, consulte:

* [Criar um armazém de dados usando o PowerShell](create-data-warehouse-powershell.md)
* [Restauro da base de dados](sql-data-warehouse-restore-points.md)

Para outras tarefas que possam ser automatizadas com powerShell, consulte [cmdlets da Base de Dados Azure SQL](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Nem todos os cmdlets da Base de Dados Azure SQL são suportados para o armazém de dados Azure Synapse Analytics. Para obter uma lista de tarefas que podem ser automatizadas com REST, consulte [Operações para Azure SQL Database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
