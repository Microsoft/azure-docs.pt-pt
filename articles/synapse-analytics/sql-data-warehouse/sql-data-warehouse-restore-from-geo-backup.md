---
title: Restaurar um armazém de dados a partir de um geo-backup
description: Como guiar para restaurar geo-restauração de uma piscina SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3c8d78c164cefbab03d9d3fa783c75ded9773d38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075822"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-restauro para piscina SQL

Neste artigo, aprende a restaurar a sua piscina SQL a partir de um geo-backup através do portal Azure e powerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada piscina SQL é hospedada por um [servidor lógico SQL](../../azure-sql/database/logical-servers.md) (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão. Verifique se o servidor SQL tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou a solicitar mais DTU, consulte [Solicitar uma alteração de quota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurar de uma região geográfica azul através da PowerShell

Para restaurar a partir de uma cópia de segurança, utilize o [cmdlet Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

> [!NOTE]
> Você pode realizar um geo-restauro para a Gen2! Para tal, especifique um Nome Gen2 ServiceObjective (por exemplo, DW1000**c**) como um parâmetro opcional.
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

Siga os passos descritos abaixo para restaurar uma piscina SQL a partir de um geo-backup:

1. Inscreva-se na sua conta [do portal Azure.](https://portal.azure.com/)
2. Clique em **+ Criar um recurso**.

   ![Novo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Clique **em Bases de Dados** e, em seguida, **Azure Synapse Analytics (anteriormente SQL DW)**.

   ![Novo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Preencha as informações **solicitadas** no separador Básicos e clique em **Seguinte: Definições adicionais**.

   ![Noções básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Para utilizar o parâmetro **de dados existente,** selecione **Backup** e selecione a cópia de segurança adequada das opções de deslocamento para baixo. Clique em **Rever + Criar**.

   ![cópia de segurança](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Uma vez restaurado o armazém de dados, verifique se o **Estado** está Online.

## <a name="next-steps"></a>Passos Seguintes

- [Restaurar uma piscina SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar uma piscina SQL eliminada](sql-data-warehouse-restore-deleted-dw.md)
