---
title: Cmdlets Powershell
description: Encontre os principais cmdlets do PowerShell para o Azure SQL Data Warehouse incluindo como pausar e retomar um banco de dados.
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
ms.openlocfilehash: b36a64bb82449ace7acc1de0b3c2bc7c5efebe70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685552"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Cmdlets do PowerShell e APIs REST para SQL Data Warehouse
Muitas tarefas de administração de SQL Data Warehouse podem ser gerenciadas usando cmdlets Azure PowerShell ou APIs REST.  Abaixo estão alguns exemplos de como usar comandos do PowerShell para automatizar tarefas comuns em seu SQL Data Warehouse.  Para obter alguns bons exemplos de REST, consulte o artigo [gerenciar a escalabilidade com REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introdução aos cmdlets Azure PowerShell
1. Abra o Windows PowerShell.
2. No prompt do PowerShell, execute estes comandos para entrar no Azure Resource Manager e selecione sua assinatura.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Pausar SQL Data Warehouse exemplo
Pause um banco de dados chamado "Database02" hospedado em um servidor chamado "Server01".  O servidor está em um grupo de recursos do Azure chamado "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, este exemplo canaliza o objeto recuperado para [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase].  Como resultado, o banco de dados é pausado. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Iniciar SQL Data Warehouse exemplo
Retome a operação de um banco de dados chamado "Database02" hospedado em um servidor chamado "Server01". O servidor está contido em um grupo de recursos chamado "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo recupera um banco de dados chamado "Database02" de um servidor chamado "Server01" que está contido em um grupo de recursos chamado "ResourceGroup1". Ele canaliza o objeto recuperado para [resume-AzSqlDatabase][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Observe que, se o servidor for foo.database.windows.net, use "foo" como o-ServerName nos cmdlets do PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Outros cmdlets do PowerShell com suporte
Esses cmdlets do PowerShell têm suporte com o Azure SQL Data Warehouse.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoint][Get-AzSqlDatabaseRestorePoint]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Retomar-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspender-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Passos seguintes
Para obter mais exemplos do PowerShell, consulte:

* [Criar um SQL Data Warehouse usando o PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Restauração de banco de dados][Database restore]

Para outras tarefas que podem ser automatizadas com o PowerShell, consulte [cmdlets do banco de dados SQL do Azure][Azure SQL Database Cmdlets]. Observe que nem todos os cmdlets do banco de dados SQL do Azure têm suporte para o Azure SQL Data Warehouse.  Para obter uma lista de tarefas que podem ser automatizadas com o REST, consulte [operações para o banco de dados SQL do Azure][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
