---
title: Cmdlets do PowerShell para o Azure SQL Data Warehouse
description: Encontre os principais cmdlets do PowerShell para o Azure SQL Data Warehouse, incluindo como colocar em pausa e retomar uma base de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0310a74986f86e072e3ee0fa81df36a03d2e5117
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409946"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Cmdlets do PowerShell e APIs REST para o SQL Data Warehouse
Muitas tarefas de administração do SQL Data Warehouse podem ser geridas através de cmdlets do Azure PowerShell ou REST APIs.  Seguem-se alguns exemplos de como utilizar comandos do PowerShell para automatizar tarefas comuns no seu armazém de dados SQL.  Para alguns bons exemplos REST, consulte o artigo [gerir escalabilidade com REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introdução aos cmdlets do PowerShell do Azure
1. Abra o Windows PowerShell.
2. Na linha de comandos do PowerShell, execute estes comandos para iniciar sessão para o Azure Resource Manager e selecione a sua subscrição.
   
    ```PowerShell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemplo de armazém de dados SQL de colocar em pausa
Colocar em pausa uma base de dados com o nome "Database02" alojada num servidor com o nome "Servidor01."  O servidor está num grupo de recursos do Azure com o nome "ResourceGroup1."

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, neste exemplo de objeto recuperado para canaliza [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase].  Como resultado, a base de dados está em pausa. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Inicie o exemplo de armazém de dados SQL
Retomar a operação de uma base de dados com o nome "Database02" alojada num servidor com o nome "Servidor01." O servidor está contido num grupo de recursos com o nome "ResourceGroup1."

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo obtém uma base de dados com o nome "Database02" de um servidor com o nome "Servidor01" que está contido num grupo de recursos com o nome "ResourceGroup1." Ele canaliza o objeto obtido [AzSqlDatabase retomar][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Tenha em atenção que se o servidor for foo.database.windows.net, utilize "foo" como ServerName - os cmdlets do PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Suporte dos outros cmdlets do PowerShell
Estes cmdlets do PowerShell são suportados com o Azure SQL Data Warehouse.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Passos Seguintes
Para obter mais exemplos do PowerShell, consulte:

* [Criar um SQL Data Warehouse com o PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Restauro de base de dados][Database restore]

Para outras tarefas que podem ser automatizadas com o PowerShell, consulte [Cmdlets de base de dados SQL do Azure][Azure SQL Database Cmdlets]. Tenha em atenção que nem todos os cmdlets da SQL Database do Azure são suportados para o Azure SQL Data Warehouse.  Para obter uma lista de tarefas que pode ser automatizada com REST, consulte [operações para a base de dados do Azure SQL][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoints
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
