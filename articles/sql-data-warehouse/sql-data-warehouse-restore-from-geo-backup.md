---
title: Restaurar um armazém de dados a partir de um geo-backup
description: Como guiar para geo-restaurar uma piscina SQL.
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
ms.openlocfilehash: 624c6665e70802907be8a41015b78d36cca7df1c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198393"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-restauro para piscina SQL

Neste artigo, você aprende a restaurar a sua piscina SQL a partir de um geo-backup através do portal Azure e PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique a sua capacidade de DTU.** Cada piscina SQL é hospedada por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma quota DTU padrão. Verifique se o servidor SQL tem quota DTU suficiente para a base de dados ser restaurada. Para aprender a calcular o DTU necessário ou para solicitar mais DTU, consulte [Solicite uma alteração](sql-data-warehouse-get-started-create-support-ticket.md)de quota DTU .

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

Siga os passos descritos abaixo para restaurar uma piscina SQL a partir de um geo-backup:

1. Inscreva-se na sua conta [do portal Azure.](https://portal.azure.com/)
1. Clique **+ Criar um recurso**. 

![Novo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Clique em **Bases de Dados** e, em seguida, **Azure Synapse Analytics (anteriormente SQL DW) **.

![Novo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Preencha as informações **solicitadas** no separador Basics e clique **em Seguinte: Definições adicionais**.

![Noções básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Para utilizar o parâmetro de **dados existente,** selecione **Backup** e selecione a cópia de segurança apropriada a partir das opções de deslocamento para baixo. Clique em **Rever + Criar**.
 
![cópia de segurança](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Uma vez restaurado o armazém de dados, verifique se o **Estado** está Online.

## <a name="next-steps"></a>Passos Seguintes
- [Restaurar uma piscina SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar uma piscina SQL eliminada](sql-data-warehouse-restore-deleted-dw.md)