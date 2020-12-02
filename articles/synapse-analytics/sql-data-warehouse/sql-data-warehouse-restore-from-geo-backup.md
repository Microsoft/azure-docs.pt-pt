---
title: Restaurar uma piscina SQL dedicada a partir de um geo-backup
description: Como guiar para restaurar geo-restauração de uma piscina SQL dedicada em Azure Synapse Analytics
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
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449879"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Geo-restaurar uma piscina SQL dedicada em Azure Synapse Analytics

Neste artigo, você aprende a restaurar a sua piscina SQL dedicada (anteriormente SQL DW) a partir de um geo-backup através do portal Azure e PowerShell.

## <a name="before-you-begin"></a>Before you begin

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada piscina SQL dedicada (anteriormente SQL DW) é hospedada por um [servidor lógico SQL](../../azure-sql/database/logical-servers.md) (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão. Verifique se o servidor SQL tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou a solicitar mais DTU, consulte [Solicitar uma alteração de quota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurar de uma região geográfica azul através da PowerShell

Para restaurar a partir de uma cópia de segurança, utilize o [cmdlet Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

> [!NOTE]
> Você pode realizar um geo-restauro para a Gen2! Para tal, especifique um Nome Gen2 ServiceObjective (por exemplo, DW1000 **c**) como um parâmetro opcional.
>

1. Antes de começar, certifique-se de [instalar a Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra o PowerShell.
3. Ligue-se à sua conta Azure e enuseça todas as subscrições associadas à sua conta.
4. Selecione a subscrição que contém o armazém de dados a restaurar.
5. Pegue o armazém de dados que quer recuperar.
6. Crie o pedido de recuperação para o armazém de dados.
7. Verifique o estado do armazém de dados geo-restaurado.
8. Para configurar o seu armazém de dados após a restauração concluída, consulte [configurar a sua base de dados após a recuperação]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

A base de dados recuperada será ativada pelo TDE se a base de dados de origem estiver ativada pelo TDE.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Restaurar a partir de uma região geográfica azul através do portal Azure

Siga os passos descritos abaixo para restaurar uma piscina SQL dedicada (anteriormente SQL DW) a partir de um geo-backup:

1. Inscreva-se na sua conta [do portal Azure.](https://portal.azure.com/)
1. Pesquisa de **piscinas SQL dedicadas (anteriormente SQL DW)**.

   ![Novo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Clique em adicionar e preencha as informações **solicitadas** no separador Básicos e clique em **Seguinte: Definições adicionais**.

   ![Noções básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. Para utilizar o parâmetro **de dados existente,** selecione **Backup** e selecione a cópia de segurança adequada das opções de deslocamento para baixo. Clique em **Rever + Criar**.

   ![cópia de segurança](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. Uma vez restaurado o armazém de dados, verifique se o **Estado** está Online.

## <a name="next-steps"></a>Passos Seguintes

- [Restaurar uma piscina SQL dedicada existente (anteriormente SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar uma piscina SQL dedicada eliminada (anteriormente SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
