---
title: Restaurar um armazém de dados a partir de um geo-backup
description: Como orientar para a restauração de geo-restauração de um Armazém de Dados Azure SQL.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 69ba3ed981a27dfff41ea9ea52e1da769a9366c4
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759640"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Armazém de dados Azure SQL

Neste artigo, você aprende a restaurar o seu armazém de dados a partir de um geo-backup através do portal Azure e PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada Armazém de Dados SQL é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão. Verifique se o servidor SQL tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou para solicitar mais DTU, consulte [Solicite uma alteração](sql-data-warehouse-get-started-create-support-ticket.md)de quota DTU .

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurar de uma região geográfica azure através da PowerShell

Para restaurar a partir de uma geo-cópia, utilize o [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) e [o Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) cmdlet.

> [!NOTE]
> Pode realizar um geo-restauro para a Gen2! Para tal, especifique um Nome Objectivodo Serviço Gen2 (por exemplo, DW1000**c**) como parâmetro opcional.
>

1. Antes de começar, certifique-se de instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Abra o PowerShell.
2. Ligue-se à sua conta Azure e enumere todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém o armazém de dados a restaurar.
4. Pegue o armazém de dados que quer recuperar.
5. Crie o pedido de recuperação para o armazém de dados.
6. Verifique o estado do armazém de dados georestaurado.
7. Para configurar o seu armazém de dados depois de concluída a restauração, consulte [Configure a sua base de dados após a recuperação]( ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
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

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Restaurar de uma região geográfica de Azure através do portal Azure

Siga os passos descritos abaixo para restaurar um Armazém de Dados Azure SQL de uma geocópia:

1. Inscreva-se na sua conta [do portal Azure.](https://portal.azure.com/)
1. Clique **+ Criar um recurso** e procurar o SQL Data Warehouse e clique em **Criar**.

    ![Novo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Preencha as informações **solicitadas** no separador Basics e clique **em Seguinte: Definições adicionais**.

    ![Noções básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Para utilizar o parâmetro de **dados existente,** selecione **Backup** e selecione a cópia de segurança apropriada a partir das opções de deslocamento para baixo. Clique em **Rever + Criar**.
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Uma vez restaurado o armazém de dados, verifique se o **Estado** está Online.

## <a name="next-steps"></a>Próximos Passos
- [Restaurar um armazém de dados existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar um armazém de dados eliminado](sql-data-warehouse-restore-deleted-dw.md)