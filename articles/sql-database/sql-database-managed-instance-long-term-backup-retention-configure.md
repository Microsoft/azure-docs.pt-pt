---
title: 'Caso gerido: Retenção de backup a longo prazo (PowerShell)'
description: Aprenda a armazenar e restaurar cópias automáticas em recipientes de armazenamento Azure Blob separados para uma base de dados Azure SQL gerida usando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/29/2020
ms.openlocfilehash: 5fa117729503a8a6a379643e7afb464aa9ed4c3e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83769943"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Gerir a Base de Dados Azure SQL gerida por exemplo retenção de backup a longo prazo (PowerShell)

Na base de dados Azure SQL gerida, pode configurar uma política de [retenção](sql-database-long-term-retention.md#managed-instance-support) de backup a longo prazo (LTR) como uma funcionalidade de pré-visualização pública limitada. Isto permite-lhe reter automaticamente cópias de dados em recipientes de armazenamento Azure Blob separados por um período de até 10 anos. Em seguida, pode recuperar uma base de dados utilizando estas cópias de segurança com o PowerShell.

   > [!IMPORTANT]
   > O LTR para casos geridos encontra-se atualmente em pré-visualização limitada e disponível para subscrições de EA e CSP caso a caso. Para solicitar a inscrição, por favor crie um bilhete de [apoio Azure.](https://azure.microsoft.com/support/create-ticket/) Para problemas de tipo de problema, escolha o caso de controlo de base de dados SQL e para o tipo de problema **selecione Backup, Restore e Business Continuity/Retenção de backup a longo prazo**. No seu pedido, por favor, diga que gostaria de estar inscrito na antevisão pública limitada do LTR para instância gerida.

As seguintes secções mostram-lhe como usar o PowerShell para configurar a retenção de cópiade cópias de segurança a longo prazo, ver backups no armazenamento Azure SQL e restaurar a partir de uma cópia de segurança no armazenamento Azure SQL.

## <a name="rbac-roles-to-manage-long-term-retention"></a>Funções rBAC para gerir a retenção a longo prazo

Para **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** e **Restore-AzSqlInstanceDatabase,** terá de ter uma das seguintes funções:

- Função do Proprietário da Subscrição ou
- Função de Colaborador de Instância Gerida ou
- Papel personalizado com as seguintes permissões:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Para **remover-AzSqlInstanceDatabaseLongTermRetentionBackup,** terá de ter uma das seguintes funções:

- Função do Proprietário da Subscrição ou
- Papel personalizado com a seguinte permissão:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> A função de Colaborador de Instância Gerida não tem permissão para eliminar cópias de segurança LTR.

As permissões RBAC podem ser concedidas no âmbito de *subscrição* ou de grupo de *recursos.* No entanto, para aceder a cópias de segurança LTR que pertencem a uma instância abandonada, a autorização deve ser concedida no âmbito de *subscrição* desse caso.

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

## <a name="view-ltr-policies"></a>Ver políticas LTR

Este exemplo mostra como listar as políticas LTR dentro de um caso

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Limpar uma política lTR

Este exemplo mostra como limpar uma política LTR a partir de uma base de dados

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Ver backups LTR

Este exemplo mostra como listar os backups LTR dentro de uma instância.

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

Este exemplo mostra como eliminar uma cópia de segurança LTR da lista de backups.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> A descontinuação do LTR não é reversível. Para eliminar uma cópia de segurança LTR depois de a instância ter sido eliminada, deve ter permissão de âmbito de subscrição. Pode configurar notificações sobre cada eliminação no Monitor Azure filtrando para operação 'Elimina uma cópia de segurança de retenção a longo prazo'. O registo de atividade contém informações sobre quem e quando fez o pedido. Consulte [criar alertas](../azure-monitor/platform/alerts-activity-log.md) de registo de atividade para obter instruções detalhadas.

## <a name="restore-from-ltr-backups"></a>Restaurar a partir de backups LTR

Este exemplo mostra como restaurar a partir de uma cópia de segurança LTR. Note que esta interface não mudou, mas o parâmetro id de recurso agora requer o id de recurso de reserva LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Para restaurar a partir de um backup LTR após a eliminação da instância, você deve ter permissões à aplicação da assinatura da instância e essa subscrição deve estar ativa. Também deve omitir o parâmetro opcional -ResourceGroupName.

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente. Ver [ponto no tempo restaurar](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md)
