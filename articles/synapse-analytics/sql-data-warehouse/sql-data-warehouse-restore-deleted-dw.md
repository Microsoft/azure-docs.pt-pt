---
title: Restaurar uma piscina SQL eliminada
description: Como orientar para restaurar uma piscina SQL eliminada.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f0432cafee07dbed071d24aa8c24ee9b2176967
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350184"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Restaurar uma piscina SQL eliminada usando azure Synapse Analytics

Neste artigo, aprende-se a restaurar um SQL utilizando o portal Azure ou o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada piscina SQL é hospedada por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão.  Verifique se o servidor SQL tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou para solicitar mais DTU, consulte [Solicite uma alteração](sql-data-warehouse-get-started-create-support-ticket.md)de quota DTU .

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Restaurar um armazém de dados eliminado através da PowerShell

Para restaurar uma piscina SQL eliminada, utilize o cmdlet [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) Se o servidor lógico correspondente também tiver sido eliminado, não pode restaurar o armazém de dados.

1. Antes de começar, certifique-se de instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Abra o PowerShell.
3. Ligue-se à sua conta Azure e enumere todas as subscrições associadas à sua conta.
4. Selecione a subscrição que contém o armazém de dados eliminado sabotado a ser restaurado.
5. Obtenha o armazém de dados eliminado específico.
6. Restaurar o armazém de dados eliminados
    1. Para restaurar o Depósito de Dados SQL eliminado para um servidor lógico diferente, certifique-se de especificar o outro nome lógico do servidor.  Este servidor lógico também pode estar num grupo e região diferentes.
    1. Para restaurar uma subscrição diferente, use o botão [Mover](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal) para mover o servidor lógico para outra subscrição.
1. Verifique se o armazém de dados restaurado está online.
1. Depois de concluída a restauração, pode configurar o seu armazém de dados recuperado seguindo a sua base de [dados após a recuperação](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Restaurar uma base de dados eliminada utilizando o portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Navegue para o servidor SQL onde o seu armazém de dados eliminado foi hospedado.
3. Selecione o ícone de bases de **dados Eliminado** na tabela de conteúdos.

    ![Bases de Dados Eliminadas](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selecione o Armazém de Dados SQL eliminado que pretende restaurar.

    ![Selecionar Bases de dados eliminadas](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Especifique um novo **nome base de dados** e clique em **OK**

    ![Especificar nome da base de dados](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Passos Seguintes
- [Restaurar uma piscina SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar a partir de uma piscina SQL geo-backup](sql-data-warehouse-restore-from-geo-backup.md)