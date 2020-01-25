---
title: Cmdlets Powershell
description: Encontre os cmdlets powerShell superiores para o Azure SQL Data Warehouse, incluindo como parar e retomar uma base de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c5f85f102d72ac2e4a0315109748d48573f49407
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721188"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell cmdlets e APIs REST para Armazém de Dados SQL
Muitas tarefas de administração do SQL Data Warehouse podem ser geridas usando cmdlets Azure PowerShell ou REST APIs.  Abaixo estão alguns exemplos de como usar comandos PowerShell para automatizar tarefas comuns no seu Armazém de Dados SQL.  Para alguns bons exemplos de REST, consulte o artigo [Gerir a escalabilidade com O REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Começar com cmdlets Azure PowerShell
1. Abra o Windows PowerShell.
2. Na solicitação da PowerShell, execute estes comandos para iniciar sessão no Gestor de Recursos Do Azure e selecione a sua subscrição.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemplo de armazém de dados de pausa SQL
Faça uma pausa numa base de dados chamada "Database02" hospedada num servidor chamado "Server01".  O servidor está num grupo de recursos Azure chamado "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, este exemplo canaliza o objeto recuperado para [suspender a Base de Dados Suspend-AzSql](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Como resultado, a base de dados é interrompida. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Inicie o exemplo do Armazém de Dados SQL
Retomar a operação de uma base de dados chamada "Database02" hospedada num servidor chamado "Server01". O servidor está contido num grupo de recursos chamado "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo recupera uma base de dados chamada "Database02" de um servidor chamado "Server01" que está contido num grupo de recursos chamado "ResourceGroup1". Canaliza o objeto recuperado para [o Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Note que se o seu servidor estiver foo.database.windows.net, use "foo" como o nome do servidor nos cmdlets PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Outros cmdlets PowerShell suportados
Estes cmdlets PowerShell são suportados com o Azure SQL Data Warehouse.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Retomar-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspender-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Passos seguintes
Para mais exemplos powerShell, consulte:

* [Criar um Armazém de Dados SQL usando powerShell](create-data-warehouse-powershell.md)
* [Restauro da base de dados](sql-data-warehouse-restore-database-powershell.md)

Para outras tarefas que podem ser automatizadas com powerShell, consulte Os Cmdlets de Base de [Dados Azure SQL](https://docs.microsoft.com/powershell/module/az.sql). Nem todos os cmdlets de base de dados Azure SQL são suportados para o Azure SQL Data Warehouse.  Para obter uma lista de tarefas que podem ser automatizadas com REST, consulte [Operações para Base de Dados SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).
