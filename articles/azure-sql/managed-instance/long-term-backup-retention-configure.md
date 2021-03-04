---
title: 'Azure SQL Caso Gerido: Retenção de backup a longo prazo'
description: Aprenda a armazenar e restaurar cópias de segurança automatizadas em recipientes de armazenamento separados Azure Blob para uma Instância Gerida Azure SQL utilizando o PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052031"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Gerir a azure SQL Gestão de Casos de reserva a longo prazo (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Em Azure SQL Managed Instance, pode configurar uma política [de retenção de backup de longo prazo](../database/long-term-retention-overview.md) (LTR) como uma funcionalidade de pré-visualização pública. Isto permite-lhe reter automaticamente cópias de dados em recipientes de armazenamento separados Azure Blob por um prazo de até 10 anos. Em seguida, pode recuperar uma base de dados utilizando estas cópias de segurança com o PowerShell.

   > [!IMPORTANT]
   > A LTR para casos geridos está atualmente disponível em visualização pública nas regiões públicas de Azure. 

As seguintes secções mostram-lhe como usar o PowerShell para configurar a retenção de backup a longo prazo, ver cópias de segurança no armazenamento Azure SQL e restaurar a partir de uma cópia de segurança no armazenamento Azure SQL.


## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

As secções seguintes mostram-lhe como usar o portal Azure para definir políticas de retenção a longo prazo, gerir backups de retenção disponíveis a longo prazo e restaurar a partir de uma cópia de segurança disponível.

### <a name="configure-long-term-retention-policies"></a>Configure políticas de retenção a longo prazo

Pode configurar a SQL Managed Instance para [reter cópias de segurança automatizadas](../database/long-term-retention-overview.md) por um período superior ao período de retenção para o seu nível de serviço.

1. No portal Azure, selecione a sua instância gerida e, em seguida, clique em **Backups**. No separador **Políticas de Retenção,** selecione a base de dados(s) na qual pretende definir ou modificar políticas de retenção de backup a longo prazo. As alterações não se aplicarão a quaisquer bases de dados deixadas por seleção. 

   ![gerir ligação de backups](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. No painel de **políticas de configuração,** especifique o período de retenção pretendido para backups semanais, mensais ou anual. Escolha um período de retenção de '0' para indicar que não deve ser definida qualquer retenção de backup a longo prazo.

   ![configurar políticas](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Quando estiver concluído, clique **em Aplicar**.

> [!IMPORTANT]
> Quando ativar uma política de retenção de backup a longo prazo, pode levar até 7 dias para que a primeira cópia de segurança fique visível e disponível para restaurar. Para obter detalhes sobre o cadance de backup LTR, consulte [a retenção de backup a longo prazo](../database/long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Ver backups e restaurar a partir de uma cópia de segurança

Veja as cópias de segurança que são mantidas para uma base de dados específica com uma política LTR e restaure a partir dessas cópias de segurança.

1. No portal Azure, selecione a sua instância gerida e, em seguida, clique em **Backups**. No separador **de backups disponível,** selecione a base de dados para a qual deseja ver cópias de segurança disponíveis. Clique em **Gerir**.

   ![selecionar base de dados](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. No painel **de backups Manage,** reveja as cópias de segurança disponíveis.

   ![ver backups](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Selecione a cópia de segurança a partir da qual pretende restaurar, clique em **Restaurar** e, em seguida, na página de restauro especifique o nome da nova base de dados. A cópia de segurança e a fonte serão pré-povoadas nesta página. 

   ![selecione backup para restaurar](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![restore](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Clique **em Rever + Criar** para rever os seus detalhes de Restauro. Em seguida, clique em **Criar** para restaurar a sua base de dados a partir da cópia de segurança escolhida.

1. Na barra de ferramentas, clique no ícone de notificação para ver o estado da tarefa de restauro.

   ![progresso da tarefa de restauro](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Quando a função de restauro estiver concluída, abra a página **'Visão Geral de Instância Gerida'** para visualizar a base de dados recentemente restaurada.

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](../database/recovery-using-backups.md#point-in-time-restore).


## <a name="using-powershell"></a>Com o PowerShell
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, no entanto, o desenvolvimento futuro será feito no módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As seguintes secções mostram-lhe como usar o PowerShell para configurar a retenção de backup a longo prazo, ver cópias de segurança no armazenamento Azure e restaurar a partir de uma cópia de segurança no armazenamento Azure.

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>Funções do Azure RBAC para gerir a retenção a longo prazo

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

As permissões Azure RBAC poderiam ser concedidas no âmbito de *subscrição* ou *grupo de recursos.* No entanto, para aceder a cópias de segurança LTR que pertençam a uma instância abandonada, a permissão deve ser concedida no âmbito de *subscrição* desse caso.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>Criar uma política LTR

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
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>Ver as políticas LTR

Este exemplo mostra como listar as políticas LTR dentro de uma instância para uma única base de dados

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Este exemplo mostra como listar as políticas LTR para todas as bases de dados em um caso

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>Limpar uma política LTR

Este exemplo mostra como limpar uma política LTR de uma base de dados

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>Ver backups LTR

Este exemplo mostra como listar as cópias de segurança LTR num caso.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>Eliminar backups LTR

Este exemplo mostra como eliminar uma cópia de segurança LTR da lista de cópias de segurança.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Eliminar a cópia de segurança LTR não é reversível. Para eliminar uma cópia de segurança LTR após a eliminação do caso, tem de ter permissão de âmbito de subscrição. Pode configurar notificações sobre cada exclusão no Azure Monitor filtrando para a operação "Elimina uma cópia de segurança de retenção a longo prazo". O registo de atividades contém informações sobre quem e quando foi feito o pedido. Consulte [Criar alertas de registo de atividade](../../azure-monitor/alerts/alerts-activity-log.md) para obter instruções detalhadas.

### <a name="restore-from-ltr-backups"></a>Restauro a partir de backups LTR

Este exemplo mostra como restaurar a partir de uma cópia de segurança LTR. Nota: esta interface não mudou, mas o parâmetro de identificação de recursos requer agora o ID de recursos de backup LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Para restaurar a partir de uma cópia de segurança LTR após a eliminação do caso, você deve ter permissões no âmbito da subscrição do caso e essa subscrição deve estar ativa. Também deve omitir o parâmetro opcional -ResourceGroupName.

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente. Ver [ponto no tempo restaurar](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](../database/automated-backups-overview.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](../database/long-term-retention-overview.md)
