---
title: Restaurar um armazém de dados existente
description: Como orientar para restaurar um armazém de dados Azure SQL existente.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d0bcf9ca6373984989d24efd2af4ffbbb19c5548
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759691"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Restaurar um armazém de dados Azure SQL existente

Neste artigo, você aprende a restaurar um Armazém de Dados SQL existente através do portal Azure e PowerShell:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada Armazém de Dados SQL é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão. Verifique se o servidor SQL tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou para solicitar mais DTU, consulte [Solicite uma alteração](sql-data-warehouse-get-started-create-support-ticket.md)de quota DTU .

## <a name="before-you-begin"></a>Antes de começar

1. Certifique-se de instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Tenha um ponto de restauro existente que quer restaurar. Se quiser criar um novo restauro, consulte [o tutorial para criar um novo ponto de restauro definido pelo utilizador](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Restaurar um armazém de dados existente através da PowerShell

Para restaurar um armazém de dados existente a partir de um ponto de restauro, utilize o cmdlet De Base de Dados PowerShell [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)

1. Abra o PowerShell.

2. Ligue-se à sua conta Azure e enumere todas as subscrições associadas à sua conta.

3. Selecione a subscrição que contém a base de dados a restaurar.

4. Enumere os pontos de restauro para o armazém de dados.

5. Escolha o ponto de restauro desejado utilizando o RestorePointCreationDate.

6. Restaurar o armazém de dados até ao ponto de restauro desejado utilizando o [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell cmdlet.
        1. Para restaurar o Armazém de Dados SQL para um servidor lógico diferente, certifique-se de especificar o outro nome lógico do servidor.  Este servidor lógico também pode estar num grupo e região diferentes.
        2. Para restaurar uma subscrição diferente, utilize o botão 'Move' para mover o servidor lógico para outra subscrição.

7. Verifique se o armazém de dados restaurado está online.

8. Depois de concluída a restauração, pode configurar o seu armazém de dados recuperado seguindo a sua base de [dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Restaurar um armazém de dados existente através do portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Navegue até ao Armazém de Dados SQL que pretende restaurar.
3. Na parte superior da lâmina de visão geral, selecione **Restaurar**.

    ![ Descrição geral do Restauro](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecione pontos de **restauro automáticos** ou **pontos de restauro definidos pelo utilizador**. Se o armazém de dados não tiver pontos de restauro automáticos, aguarde algumas horas ou crie um ponto de restauro definido pelo utilizador antes de restaurar. Para pontos de restauro definidos pelo utilizador, selecione um existente ou crie um novo. Para **o Server,** pode escolher um servidor lógico num grupo e região diferentes ou criar um novo. Depois de fornecer todos os parâmetros, clique em **Rever + Restaurar**.

    ![Pontos de restauro automáticos](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Próximos Passos
- [Restaurar um armazém de dados eliminado](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar a partir de um armazém de dados de geo-backup](sql-data-warehouse-restore-from-geo-backup.md)

 