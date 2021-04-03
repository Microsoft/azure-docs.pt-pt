---
title: Restaurar uma piscina SQL dedicada existente (anteriormente SQL DW)
description: Como guiar para restaurar uma piscina SQL dedicada existente em Azure Synapse Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591997"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Restaurar uma piscina SQL dedicada existente (anteriormente SQL DW)

Neste artigo, você aprende a restaurar uma piscina SQL dedicada existente (anteriormente SQL DW) usando o portal Azure e PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada piscina é hospedada por um [servidor lógico SQL](../../azure-sql/database/logical-servers.md) (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão. Verifique se o servidor tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou a solicitar mais DTU, consulte [Solicitar uma alteração de quota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Antes de começar

1. Certifique-se de [instalar a Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Tenha um ponto de restauro existente que queira restaurar. Se quiser criar um novo restauro, consulte [o tutorial para criar um novo ponto de restauro definido pelo utilizador](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Restaurar uma piscina SQL dedicada existente (anteriormente SQL DW) através da PowerShell

Para restaurar uma piscina SQL dedicada existente (anteriormente SQL DW) a partir de um ponto de restauração, utilize o [cmdlet Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell.

1. Abra o PowerShell.

2. Ligue-se à sua conta Azure e enuseça todas as subscrições associadas à sua conta.

3. Selecione a subscrição que contém a base de dados a ser restaurada.

4. Listar os pontos de restauro para a piscina SQL dedicada (anteriormente SQL DW).

5. Escolha o ponto de restauro pretendido utilizando o RestorePointCreationDate.

6. Restaurar a piscina SQL dedicada (anteriormente SQL DW) ao ponto de restauro pretendido utilizando o cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell.

    1. Para restaurar a piscina SQL dedicada (anteriormente SQL DW) para um servidor diferente, certifique-se de especificar o outro nome do servidor.  Este servidor também pode estar em um grupo de recursos diferente e região.
    2. Para restaurar uma subscrição diferente, utilize o botão 'Move' para mover o servidor para outra subscrição.

7. Verifique se a piscina SQL dedicada restaurada (anteriormente SQL DW) está online.

8. Após a restauração concluída, pode configurar a sua piscina SQL (anteriormente SQL DW) utilizando a [sua base de dados após a recuperação.](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Restaurar uma piscina SQL dedicada existente (anteriormente SQL DW) através do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para o dedicado que deseja restaurar.
3. Na parte superior da lâmina overview, **selecione Restaurar**.

    ![ Descrição geral do Restauro](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecione pontos de **restauro automáticos** ou **pontos de restauro definidos pelo utilizador**. Se a piscina SQL dedicada (anteriormente SQL DW) não tiver quaisquer pontos de restauro automáticos, aguarde algumas horas ou crie um ponto de restauro definido pelo utilizador antes de restaurar. Para User-Defined Restaurar Pontos, selecione um existente ou crie um novo. Para **o Servidor,** pode escolher um servidor num grupo e região de recursos diferentes ou criar um novo. Depois de fornecer todos os parâmetros, clique em **Rever + Restaurar.**

    ![Pontos de Restauro Automático](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Passos Seguintes

- [Restaurar uma piscina SQL dedicada eliminada (anteriormente SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar a partir de uma piscina SQL dedicada a geo-backup (anteriormente SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
