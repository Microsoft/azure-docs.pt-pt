---
title: Restaurar um SQL Data Warehouse do Azure existente | Microsoft Docs
description: Guia de instruções para restaurar um SQL Data Warehouse do Azure existente.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7470dd96109823057a174d2ecf097583dcb51898
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575281"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Restaurar um SQL Data Warehouse do Azure existente

Neste artigo, você aprenderá a restaurar um SQL Data Warehouse existente por meio do portal do Azure e do PowerShell:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada SQL Data Warehouse é hospedada por um SQL Server (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão. Verifique se o SQL Server tem cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, consulte [solicitar uma alteração de cota de DTU][Request a DTU quota change].

## <a name="before-you-begin"></a>Antes de começar

1. Certifique-se de [instalar Azure PowerShell][Install Azure PowerShell].
2. Ter um ponto de restauração existente do qual você deseja restaurar. Se você quiser criar uma nova restauração, consulte [o tutorial para criar um novo ponto de restauração definido pelo usuário][the tutorial to create a new user-defined restore point].

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Restaurar um data warehouse existente por meio do PowerShell

Para restaurar um data warehouse existente de um ponto de restauração, use o cmdlet do PowerShell [Restore-AzSqlDatabase][Restore-AzSqlDatabase] .

1. Abra o PowerShell.

2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.

3. Selecione a assinatura que contém o banco de dados a ser restaurado.

4. Liste os pontos de restauração para o data warehouse.

5. Escolha o ponto de restauração desejado usando o RestorePointCreationDate.

6. Restaure o data warehouse para o ponto de restauração desejado usando o cmdlet do PowerShell [Restore-AzSqlDatabase][Restore-AzSqlDatabase] .
        1. Para restaurar o SQL Data Warehouse para um servidor lógico diferente, certifique-se de especificar o outro nome de servidor lógico.  Esse servidor lógico também pode estar em um grupo de recursos e região diferentes.
        2. Para restaurar para uma assinatura diferente, use o botão ' mover ' para mover o servidor lógico para outra assinatura.

7. Verifique se o data warehouse restaurado está online.

8. Depois que a restauração for concluída, você poderá configurar o data warehouse recuperado seguindo [configurar seu banco de dados após a recuperação][Configure your database after recovery].

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Restaurar um data warehouse existente por meio do portal do Azure

1. Inicie sessão no [portal do Azure][Azure portal].
2. Navegue até o SQL Data Warehouse do qual você deseja restaurar.
3. Na parte superior da folha visão geral, selecione **restaurar**.

    ![ Descrição geral do Restauro](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecione **pontos de restauração automáticos** ou **pontos de restauração definidos pelo usuário**. Se o data warehouse não tiver pontos de restauração automáticos, aguarde algumas horas ou crie um ponto de restauração definido pelo usuário antes de restaurar. Para pontos de restauração definidos pelo usuário, selecione um existente ou crie um novo. Para o **servidor**, você pode escolher um servidor lógico em um grupo de recursos e uma região diferentes ou criar um novo. Depois de fornecer todos os parâmetros, clique em revisar **+ restaurar**.

    ![Pontos de Restauro Automático](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Próximos Passos
- [Restaurar um data warehouse excluído][Restore a deleted data warehouse]
- [Restaurar de um data warehouse de backup geográfico][Restore from a geo-backup data warehouse]
 
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
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
