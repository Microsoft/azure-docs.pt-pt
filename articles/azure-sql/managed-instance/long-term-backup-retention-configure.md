---
title: 'Exemplo gerido aZure SQL: Retenção de backup a longo prazo (PowerShell)'
description: Aprenda a armazenar e restaurar cópias de segurança automatizadas em recipientes de armazenamento separados Azure Blob para uma Instância Gerida Azure SQL utilizando o PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: 4249e7df61c7f1f090b6377114bcab6ce1d40b2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619088"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Gerir a azure SQL Gestão de Casos de reserva a longo prazo (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Em Azure SQL Managed Instance, pode configurar uma política [de retenção de backup de longo prazo](../database/long-term-retention-overview.md#sql-managed-instance-support) (LTR) como uma funcionalidade de pré-visualização pública limitada. Isto permite-lhe reter automaticamente cópias de dados em recipientes de armazenamento separados Azure Blob por um prazo de até 10 anos. Em seguida, pode recuperar uma base de dados utilizando estas cópias de segurança com o PowerShell.

   > [!IMPORTANT]
   > A LTR para casos geridos encontra-se atualmente em pré-visualização limitada e está disponível para as assinaturas EA e CSP caso a caso. Para solicitar a inscrição, por favor crie um [bilhete de apoio Azure.](https://azure.microsoft.com/support/create-ticket/) Para o tipo de problema selecione problema técnico, para o serviço escolha SQL Database Managed Instance e para o tipo de problema **selecione Backup, Restore e Business Continuity/Long-term back retention**. No seu pedido, por favor, diga que gostaria de estar inscrito na antevisão pública limitada da LTR para instância gerida.

As seguintes secções mostram-lhe como usar o PowerShell para configurar a retenção de backup a longo prazo, ver cópias de segurança no armazenamento Azure SQL e restaurar a partir de uma cópia de segurança no armazenamento Azure SQL.

## <a name="azure-roles-to-manage-long-term-retention"></a>Funções azure para gerir a retenção a longo prazo

Para **a Seta-AzSqlInstanceDatabaseLongTermRetentionBackup** e **Restore-AzSqlInstanceDatabase,** terá de ter uma das seguintes funções:

- Função do Proprietário de Assinatura ou
- Papel de contribuinte de instância gerida ou
- Função personalizada com as seguintes permissões:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Para **remover-AzSqlInstanceDatabaseLongTermRetentionBackup,** terá de ter uma das seguintes funções:

- Função do Proprietário de Assinatura ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> A função contribuidora de casos geridos não tem permissão para eliminar cópias de segurança LTR.

As permissões do RBAC poderiam ser concedidas no âmbito *de subscrição* ou *grupo de recursos.* No entanto, para aceder a cópias de segurança LTR que pertençam a uma instância abandonada, a permissão deve ser concedida no âmbito de *subscrição* desse caso.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Criar uma política LTR

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>Ver as políticas LTR

Este exemplo mostra como listar as políticas LTR dentro de um caso

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Limpar uma política LTR

Este exemplo mostra como limpar uma política LTR de uma base de dados

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Ver backups LTR

Este exemplo mostra como listar as cópias de segurança LTR num caso.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>Eliminar backups LTR

Este exemplo mostra como eliminar uma cópia de segurança LTR da lista de cópias de segurança.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Eliminar a cópia de segurança LTR não é reversível. Para eliminar uma cópia de segurança LTR após a eliminação do caso, tem de ter permissão de âmbito de subscrição. Pode configurar notificações sobre cada exclusão no Azure Monitor filtrando para a operação "Elimina uma cópia de segurança de retenção a longo prazo". O registo de atividades contém informações sobre quem e quando foi feito o pedido. Consulte [Criar alertas de registo de atividade](../../azure-monitor/platform/alerts-activity-log.md) para obter instruções detalhadas.

## <a name="restore-from-ltr-backups"></a>Restauro a partir de backups LTR

Este exemplo mostra como restaurar a partir de uma cópia de segurança LTR. Nota: esta interface não mudou, mas o parâmetro de identificação de recursos requer agora o id de recursos de backup LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Para restaurar a partir de uma cópia de segurança LTR após a eliminação do caso, você deve ter permissões no âmbito da subscrição do caso e essa subscrição deve estar ativa. Também deve omitir o parâmetro opcional -ResourceGroupName.

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente. Ver [ponto no tempo restaurar](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](../database/automated-backups-overview.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](../database/long-term-retention-overview.md)
