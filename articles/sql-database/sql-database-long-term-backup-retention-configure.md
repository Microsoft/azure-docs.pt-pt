---
title: Gerir a retenção de longa duração de cópia de segurança
description: Saiba como armazenar backups automatizados no armazenamento de SQL Azure e restaurá-los
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/21/2019
ms.openlocfilehash: ea9a1da775a64f8ee405ced52df01d0824836c42
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820026"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gerenciar retenção de backup de longo prazo do banco de dados SQL do Azure

No banco de dados SQL do Azure, você pode configurar um banco de dados único ou em pool com uma EPD (política de retenção de backup) de [longo prazo](sql-database-long-term-retention.md) para reter automaticamente os backups de banco de dados em contêineres de armazenamento de BLOBs do Azure separados por até 10 anos. Em seguida, você pode recuperar um banco de dados usando esses backups usando o portal do Azure ou o PowerShell.

> [!IMPORTANT]
> Atualmente, o [instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance.md) não oferece suporte à retenção de backup de longo prazo.

## <a name="use-the-azure-portal-to-manage-long-term-backups"></a>Usar o portal do Azure para gerenciar backups de longo prazo

As seções a seguir mostram como usar o portal do Azure para configurar a retenção de longo prazo, Exibir backups em retenção de longo prazo e restaurar o backup de retenção de longo prazo.

### <a name="configure-long-term-retention-policies"></a>Configurar políticas de retenção de longo prazo

Você pode configurar o banco de dados SQL para [manter backups automatizados](sql-database-long-term-retention.md) por um período maior que o período de retenção para sua camada de serviço. 

1. No portal do Azure, selecione o SQL Server e clique em **gerenciar backups**. Na guia **Configurar políticas** , *marque a caixa de seleção do banco de dados no qual você deseja definir ou modificar políticas de retenção de backup de longo prazo*. Se a caixa de seleção ao lado do banco de dados não estiver selecionada, as alterações da política não serão aplicadas a esse banco de dados.  

   ![link gerenciar backups](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. No painel **Configurar políticas** , selecione se deseja reter backups semanais, mensais ou anuais e especifique o período de retenção para cada um. 

   ![configurar políticas](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Ao concluir, clique em **aplicar**.

> [!IMPORTANT]
> Quando você habilita uma política de retenção de backup de longo prazo, pode levar até 7 dias para o primeiro backup ficar visível e disponível para restauração. Para obter detalhes sobre o Cadance de backup EPD, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Exibir backups e restaurar a partir de um backup usando portal do Azure

Exiba os backups que são retidos para um banco de dados específico com uma política EPD e restaure desses backups. 

1. No portal do Azure, selecione o SQL Server e clique em **gerenciar backups**. Na guia **backups disponíveis** , selecione o banco de dados para o qual você deseja ver os backups disponíveis.

   ![Selecionar Banco de dados](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. No painel **backups disponíveis** , examine os backups disponíveis. 

   ![Exibir backups](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Selecione o backup do qual você deseja restaurar e, em seguida, especifique o novo nome do banco de dados.

   ![restore](./media/sql-database-long-term-retention/ltr-restore.png)

5. Clique em **OK** para restaurar o banco de dados do backup no armazenamento SQL do Azure para o novo banco de dados.

6. Na barra de ferramentas, clique no ícone de notificação para ver o estado da tarefa de restauro.

   ![progresso da tarefa de restauro](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Quando o trabalho de restauração for concluído, abra a página bancos de dados **SQL** para exibir o banco de dados recém restaurado.

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-manage-long-term-backups"></a>Usar o PowerShell para gerenciar backups de longo prazo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

As seções a seguir mostram como usar o PowerShell para configurar a retenção de backup de longo prazo, Exibir backups no armazenamento do SQL Azure e restaurar de um backup no armazenamento SQL do Azure.


### <a name="rbac-roles-to-manage-long-term-retention"></a>Funções de RBAC para gerenciar a retenção de longo prazo

Para **Get-AzSqlDatabaseLongTermRetentionBackup** e **Restore-AzSqlDatabase**, você precisará ter uma das seguintes funções:

- Função de proprietário da assinatura ou
- SQL Server função colaborador ou
- Função personalizada com as seguintes permissões:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/leitura
 
Para **Remove-AzSqlDatabaseLongTermRetentionBackup**, você precisará ter uma das seguintes funções:

- Função de proprietário da assinatura ou
- Função personalizada com a seguinte permissão:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete


> [!NOTE]
> A função colaborador de SQL Server não tem permissão para excluir backups EPD.

As permissões de RBAC podem ser concedidas em escopo de *assinatura* ou *grupo de recursos* . No entanto, para acessar backups EPD que pertencem a um servidor descartado, a permissão deve ser concedida no escopo da *assinatura* desse servidor.


### <a name="create-an-ltr-policy"></a>Criar uma política EPD

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Exibir políticas EPD
Este exemplo mostra como listar as políticas EPD dentro de um servidor

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Limpar uma política EPD
Este exemplo mostra como limpar uma política EPD de um banco de dados

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Exibir backups EPD

Este exemplo mostra como listar os backups EPD em um servidor. 

```powershell
# List all LTR backups under the current subscription in a specific Azure region 
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# List the LTR backups under a specific resource group in a specific Azure region 
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup

# List the LTR backups under an existing server
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup -ServerName $serverName

# List the LTR backups for a specific database 
# The backups are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.  
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database under a server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Excluir backups EPD

Este exemplo mostra como excluir um backup EPD da lista de backups.

```powershell
# Remove the earliest backup from the list of backups
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> Excluir o backup EPD não é reversível. Para excluir um backup EPD depois que o servidor tiver sido excluído, você deverá ter a permissão de escopo da assinatura. Você pode configurar notificações sobre cada exclusão em Azure Monitor filtrando para a operação ' exclui um backup de retenção de longo prazo '. O log de atividades contém informações sobre quem e quando fez a solicitação. Consulte [criar alertas do log de atividades](../azure-monitor/platform/alerts-activity-log.md) para obter instruções detalhadas.
>

### <a name="restore-from-ltr-backups"></a>Restaurar de backups EPD
Este exemplo mostra como restaurar de um backup EPD. Observe que essa interface não foi alterada, mas o parâmetro de ID de recurso agora requer a ID de recurso de backup EPD. 

```powershell
# Restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup 
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Para restaurar de um backup EPD após a exclusão do servidor, você deve ter permissões com escopo para a assinatura do servidor e essa assinatura deve estar ativa. Você também deve omitir o parâmetro opcional-ResourceGroupName.  
>

> [!NOTE]
> A partir daqui, você pode se conectar ao banco de dados restaurado usando o SQL Server Management Studio para executar as tarefas necessárias, como para extrair um pouco de dado do banco de dados restaurado a fim de copiá-lo para o existente, ou para excluir o existente e renomear a restauração banco de dados para o nome do banco de dados existente. Confira a [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md)
