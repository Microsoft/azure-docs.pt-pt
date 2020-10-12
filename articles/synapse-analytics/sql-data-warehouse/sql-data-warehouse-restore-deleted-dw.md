---
title: Restaurar uma piscina SQL eliminada
description: Como orientar para restaurar uma piscina SQL eliminada.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 879844efdc5c2b40f69ee5f79305d4dfa596fd27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460733"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Restaurar uma piscina SQL eliminada usando a Azure Synapse Analytics

Neste artigo, aprende-se a restaurar um SQL utilizando o portal Azure ou o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada piscina SQL é hospedada por um [servidor lógico SQL](../../azure-sql/database/logical-servers.md) (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão.  Verifique se o servidor tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou a solicitar mais DTU, consulte [Solicitar uma alteração de quota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Restaurar um armazém de dados eliminado através do PowerShell

Para restaurar uma piscina SQL eliminada, utilize o [cmdlet Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Se o servidor correspondente também tiver sido eliminado, não é possível restaurar o armazém de dados.

1. Antes de começar, certifique-se de [instalar a Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra o PowerShell.
3. Ligue-se à sua conta Azure e enuseça todas as subscrições associadas à sua conta.
4. Selecione a subscrição que contém a piscina SQL eliminada a ser restaurada.
5. Obtenha o armazém de dados eliminado específico.
6. Restaurar a piscina SQL eliminada
    1. Para restaurar a piscina SQL eliminada para um servidor diferente, certifique-se de especificar o outro nome do servidor.  Este servidor também pode estar em um grupo de recursos diferente e região.
    1. Para restaurar uma subscrição diferente, utilize o botão [Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) para mover o servidor para outra subscrição.
7. Verifique se o armazém de dados restaurado está online.
8. Após a restauração concluída, pode configurar o seu armazém de dados recuperado seguindo a [configuração da sua base de dados após a recuperação](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Restaurar uma base de dados eliminada utilizando o portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para o servidor onde o seu armazém de dados apagado foi hospedado.
3. Selecione o ícone **de bases de dados eliminadas** na tabela de conteúdos.

    ![Bases de dados eliminadas](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selecione o Azure Synapse Analytics apagado que pretende restaurar.

    ![Selecionar Bases de dados eliminadas](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Especifique um novo **nome de base de dados** e clique em **OK**

    ![Especificar nome da base de dados](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Passos Seguintes

- [Restaurar uma piscina SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar a partir de uma piscina SQL de geo-backup](sql-data-warehouse-restore-from-geo-backup.md)
