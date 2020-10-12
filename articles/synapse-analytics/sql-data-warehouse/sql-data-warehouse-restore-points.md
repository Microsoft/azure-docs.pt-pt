---
title: Pontos de restauro definidos pelo utilizador
description: Como criar um ponto de restauração para piscina SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 66a2dad9396e8bf7c8ef49db529f7a5486cc8a8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87089212"
---
# <a name="user-defined-restore-points"></a>Pontos de restauro definidos pelo utilizador

Neste artigo, você aprenderá a criar um novo ponto de restauro definido pelo utilizador para uma piscina SQL em Azure Synapse Analytics usando PowerShell e o portal Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Criar pontos de restauro definidos pelo utilizador através do PowerShell

Para criar um ponto de restauro definido pelo utilizador, utilize o cmdlet [PowerShell New-AzSqlDataBaseRestorePoint.](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

1. Antes de começar, certifique-se de [instalar a Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra o PowerShell.
3. Ligue-se à sua conta Azure e enuseça todas as subscrições associadas à sua conta.
4. Selecione a subscrição que contém a base de dados a ser restaurada.
5. Crie um ponto de restauro para uma cópia imediata do seu armazém de dados.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Consulte a lista de todos os pontos de restauro existentes.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Criar pontos de restauro definidos pelo utilizador através do portal Azure

Os pontos de restauro definidos pelo utilizador também podem ser criados através do portal Azure.

1. Inscreva-se na sua conta [do portal Azure.](https://portal.azure.com/)

2. Navegue até à piscina SQL para a que pretende criar um ponto de restauração.

3. Selecione **visão geral** do painel esquerdo, selecione **+ Novo Ponto de Restauro**. Se o botão New Restore Point não estiver ativado, certifique-se de que a piscina SQL não está em pausa.

    ![Novo ponto de restauro](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Especifique um nome para o seu ponto de restauro definido pelo utilizador e clique **em Aplicar**. Os pontos de restauro definidos pelo utilizador têm um período de retenção predefinido de sete dias.

    ![Nome do Ponto de Restauro](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Passos seguintes

- [Restaurar uma piscina SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar uma piscina SQL eliminada](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar a partir de uma piscina SQL de geo-backup](sql-data-warehouse-restore-from-geo-backup.md)

