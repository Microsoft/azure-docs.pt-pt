---
title: Restaurar um armazém de dados existente
description: Como guiar para restaurar uma piscina SQL existente.
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
ms.openlocfilehash: bf83de481cf15711da45edf00e63262a05489247
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199543"
---
# <a name="restore-an-existing-sql-pool"></a>Restaurar uma piscina SQL existente

Neste artigo, você aprende como restaurar uma piscina SQL existente em Azure Synapse Analytics usando o portal Azure e PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada piscina é hospedada por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão. Verifique se o servidor SQL tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou para solicitar mais DTU, consulte [Solicite uma alteração](sql-data-warehouse-get-started-create-support-ticket.md)de quota DTU .

## <a name="before-you-begin"></a>Antes de começar

1. Certifique-se de instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Tenha um ponto de restauro existente que quer restaurar. Se quiser criar um novo restauro, consulte [o tutorial para criar um novo ponto de restauro definido pelo utilizador](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Restaurar uma piscina SQL existente através da PowerShell

Para restaurar uma piscina SQL existente a partir de um ponto de restauro, utilize o cmdlet De Base de Dados PowerShell [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)

1. Abra o PowerShell.

2. Ligue-se à sua conta Azure e enumere todas as subscrições associadas à sua conta.

3. Selecione a subscrição que contém a base de dados a restaurar.

4. Enumere os pontos de restauro para a piscina SQL.

5. Escolha o ponto de restauro desejado utilizando o RestorePointCreationDate.

6. Restaurar a piscina SQL até ao ponto de restauro desejado utilizando o cmdlet De Base de Dados PowerShell [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
        1. Para restaurar o pool SQL para um servidor lógico diferente, certifique-se de especificar o outro nome lógico do servidor.  Este servidor lógico também pode estar num grupo e região diferentes.
        2. Para restaurar uma subscrição diferente, utilize o botão 'Move' para mover o servidor lógico para outra subscrição.

7. Verifique se a piscina SQL restaurada está online.

8. Depois de concluída a restauração, pode configurar o seu pool SQL recuperado seguindo a [configuração da sua base de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Restaurar uma piscina SQL existente através do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue até à piscina SQL da sua vontade de restaurar.
3. Na parte superior da lâmina de visão geral, selecione **Restaurar**.

    ![ Descrição geral do Restauro](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecione pontos de **restauro automáticos** ou **pontos de restauro definidos pelo utilizador**. Se o pool SQL não tiver pontos de restauro automáticos, aguarde algumas horas ou crie um ponto de restauro definido pelo utilizador antes de restaurar. Para pontos de restauro definidos pelo utilizador, selecione um existente ou crie um novo. Para **o Server,** pode escolher um servidor lógico num grupo e região diferentes ou criar um novo. Depois de fornecer todos os parâmetros, clique em **Rever + Restaurar**.

    ![Pontos de restauro automáticos](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Passos Seguintes
- [Restaurar uma piscina SQL eliminada](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar a partir de uma piscina SQL geo-backup](sql-data-warehouse-restore-from-geo-backup.md)

 