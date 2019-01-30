---
title: Restaurar um armazém de dados SQL do Azure (PowerShell) | Documentos da Microsoft
description: Tarefas do PowerShell para restaurar um armazém de dados SQL do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9ba1137c3727182fec4e5e90b0d561b055ef756d
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243628"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Restaurar um armazém de dados SQL do Azure (PowerShell)
> [!div class="op_single_selector"]
> * [Descrição geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Neste artigo, ficará a saber como restaurar um armazém de dados SQL do Azure com o PowerShell.

## <a name="before-you-begin"></a>Antes de começar
**Verifique a sua capacidade DTU.** Cada SQL Data Warehouse é hospedado por um SQL server (por exemplo, myserver.database.windows.net), que tem uma quota DTU predefinida.  Antes de pode restaurar um armazém de dados SQL, certifique-se de que o seu SQL server tem quota suficiente DTU restante para a base de dados a ser restaurada. Para saber como calcular a DTU necessária ou para pedir DTU mais, veja [solicitar uma alteração de quota DTU][Request a DTU quota change].

### <a name="install-powershell"></a>Instalar o PowerShell
Para utilizar o Azure PowerShell com o SQL Data Warehouse, terá de instalar o Azure PowerShell versão 1.0 ou superior.  Pode verificar a sua versão, executando **Get-Module - ListAvailable-Name AzureRM**.  Pode ser instalada a versão mais recente a partir [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Para mais informações sobre como instalar a versão mais recente, consulte [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Restaurar uma base de dados do Active Directory ou em pausa
Para restaurar uma base de dados a partir de uma utilização de instantâneos a [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet do PowerShell.

1. Abra o Windows PowerShell.
2. Ligue a sua conta do Azure e listar todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados para serem restaurados.
4. Liste os pontos de restauro da base de dados.
5. Escolha o ponto de restauro desejado usando o RestorePointCreationDate.
6. Restaure a base de dados para o ponto de restauro pretendido.
7. Certifique-se de que a base de dados restaurada está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Depois do restauro foi concluída, pode configurar a base de dados recuperada seguindo [configurar a sua base de dados após a recuperação][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada
Para restaurar uma base de dados eliminada, utilize o [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet.

1. Abra o Windows PowerShell.
2. Ligue a sua conta do Azure e listar todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados eliminada para serem restaurados.
4. Obtenha a base de dados eliminada específico.
5. Restaure a base de dados eliminada.
6. Certifique-se de que a base de dados restaurada está online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Depois do restauro foi concluída, pode configurar a base de dados recuperada seguindo [configurar a sua base de dados após a recuperação][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Restaurar a partir de uma região geográfica do Azure
Para recuperar uma base de dados, utilize o [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet.

> [!NOTE]
> Pode efetuar um georrestauro para otimizado para o escalão de desempenho de computação! Para tal, especifique um otimizado para computação ServiceObjectiveName como um parâmetro opcional. 
>
> 

1. Abra o Windows PowerShell.
2. Ligue a sua conta do Azure e listar todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados para serem restaurados.
4. Obtenha a base de dados que pretende recuperar.
5. Crie o pedido de recuperação para a base de dados.
6. Verifique se o estado da base de dados geo-restaurada.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar a sua base de dados após o restauro foi concluída, consulte [configurar a sua base de dados após a recuperação][Configure your database after recovery].
> 
> 

A base de dados recuperada serão habilitados para TDE se a base de dados de origem é habilitado para TDE.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as funcionalidades de continuidade de negócio das edições do SQL Database do Azure, leia os [descrição geral da continuidade de negócio da linha de base de dados do Azure SQL][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
