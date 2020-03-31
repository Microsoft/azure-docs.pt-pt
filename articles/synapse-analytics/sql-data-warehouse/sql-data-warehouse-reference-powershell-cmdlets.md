---
title: PowerShell & REST APIs
description: Encontre os cmdlets powerShell superiores para o pool Azure Synapse Analytics SQL, incluindo como parar e retomar uma base de dados.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351395"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & APIs DE REST para piscina Azure Synapse Analytics SQL

Muitas tarefas administrativas de piscina Azure Synapse Analytics SQL podem ser geridas usando cmdlets Azure PowerShell ou REST APIs.  Abaixo estão alguns exemplos de como usar comandos PowerShell para automatizar tarefas comuns na sua piscina SQL.  Para alguns bons exemplos de REST, consulte o artigo [Gerir a escalabilidade com O REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Começar com cmdlets Azure PowerShell

1. Abra o Windows PowerShell.
2. Na solicitação da PowerShell, execute estes comandos para iniciar sessão no Gestor de Recursos Do Azure e selecione a sua subscrição.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Pausa exemplo de armazém de dados
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

## <a name="start-data-warehouse-example"></a>Iniciar exemplo de armazém de dados

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
Estes cmdlets PowerShell são suportados com o armazém de dados Azure Synapse Analytics.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Nova AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remover-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restaurar-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Retomar-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspender-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Passos seguintes
Para mais exemplos powerShell, consulte:

* [Criar um armazém de dados usando o PowerShell](create-data-warehouse-powershell.md)
* [Restauro da base de dados](sql-data-warehouse-restore-points.md)

Para outras tarefas que podem ser automatizadas com powerShell, consulte os cmdlets de base de [dados Azure SQL](https://docs.microsoft.com/powershell/module/az.sql). Nem todos os cmdlets de base de dados Azure SQL são suportados para o armazém de dados Azure Synapse Analytics.  Para obter uma lista de tarefas que podem ser automatizadas com REST, consulte [Operações para Base de Dados SQL Azure](/rest/api/sql/).
