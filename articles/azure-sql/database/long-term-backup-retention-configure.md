---
title: 'Base de Dados Azure SQL: Gerir a retenção de backup a longo prazo'
description: Saiba como armazenar e restaurar cópias de segurança automatizadas para a Base de Dados Azure SQL em armazenamento Azure (até 10 anos) utilizando o portal Azure e PowerShell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690566"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gerir a Azure SQL Database retenção de backup a longo prazo
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Com a Base de Dados Azure SQL, pode definir uma política [de retenção de backup de longo prazo](long-term-retention-overview.md) (LTR) para reter automaticamente cópias de segurança em recipientes de armazenamento separados Azure Blob por um período máximo de 10 anos. Em seguida, pode recuperar uma base de dados utilizando estas cópias de segurança utilizando o portal Azure ou o PowerShell. As políticas de retenção a longo prazo também são apoiadas para [a Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md).

## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

As secções seguintes mostram-lhe como usar o portal Azure para definir políticas de retenção a longo prazo, gerir backups de retenção disponíveis a longo prazo e restaurar a partir de uma cópia de segurança disponível.

### <a name="configure-long-term-retention-policies"></a>Configure políticas de retenção a longo prazo

Pode configurar a Base de Dados SQL para [reter cópias de segurança automatizadas](long-term-retention-overview.md) por um período superior ao período de retenção para o seu nível de serviço.

1. No portal Azure, navegue para o seu servidor e, em seguida, selecione **Backups**. Selecione o **separador 'Retenção'** para modificar as definições de retenção de cópias de segurança.

   ![experiência de políticas de retenção](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. No separador Políticas de Retenção, selecione a base de dados(s) na qual pretende definir ou modificar políticas de retenção de backup a longo prazo. As bases de dados não eleitas não serão afetadas.

   ![selecionar base de dados para configurar políticas de retenção de backup](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. No painel de **políticas de configuração,** especifique o período de retenção pretendido para backups semanais, mensais ou anual. Escolha um período de retenção de '0' para indicar que não deve ser definida qualquer retenção de backup a longo prazo.

   ![configure painel de políticas](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Selecione **Aplicar** para aplicar as definições de retenção escolhidas em todas as bases de dados selecionadas.

> [!IMPORTANT]
> Quando ativar uma política de retenção de backup a longo prazo, pode levar até 7 dias para que a primeira cópia de segurança fique visível e disponível para restaurar. Para obter detalhes sobre o cadance de backup LTR, consulte [a retenção de backup a longo prazo](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Ver backups e restaurar a partir de uma cópia de segurança

Veja as cópias de segurança que são mantidas para uma base de dados específica com uma política LTR e restaure a partir dessas cópias de segurança.

1. No portal Azure, navegue para o seu servidor e, em seguida, selecione **Backups**. Para visualizar as cópias de segurança LTR disponíveis para uma base de dados específica, **selecione Gerir** sob a coluna de backups LTR disponível. Aparecerá um painel com uma lista das cópias de segurança LTR disponíveis para a base de dados selecionada.

   ![experiência de backups disponíveis](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. No painel **de backups LTR disponível** que aparece, reveja as cópias de segurança disponíveis. Pode selecionar uma cópia de segurança para restaurar ou eliminar.

   ![ver backups LTR disponíveis](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Para restaurar a partir de uma cópia de segurança LTR disponível, selecione a cópia de segurança a partir da qual pretende restaurar e, em seguida, selecione **Restaurar**.

   ![restaurar a partir de backup LTR disponível](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Escolha um nome para a sua nova base de dados e, em seguida, selecione **Rever + Criar** para rever os detalhes da sua Restauração. Selecione **Criar** para restaurar a sua base de dados a partir da cópia de segurança escolhida.

   ![configurar detalhes de restauro](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Na barra de ferramentas, selecione o ícone de notificação para visualizar o estado da função de restauro.

   ![progresso da tarefa de restauro](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Quando o trabalho de restauro estiver concluído, abra a página **de bases de dados SQL** para ver a base de dados recentemente restaurada.

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As seguintes secções mostram-lhe como usar o PowerShell para configurar a retenção de backup a longo prazo, ver cópias de segurança no armazenamento Azure e restaurar a partir de uma cópia de segurança no armazenamento Azure.

### <a name="azure-roles-to-manage-long-term-retention"></a>Funções azure para gerir a retenção a longo prazo

Para **Get-AzSqlDatabaseLongTermRetentionBackup** e **Restore-AzSqlDatabase,** terá de ter uma das seguintes funções:

- Função do Proprietário de Assinatura ou
- Papel de contribuinte do servidor SQL ou
- Função personalizada com as seguintes permissões:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Para **remover-AzSqlDatabaseLongTermRetentionBackup,** terá de ter uma das seguintes funções:

- Função do Proprietário de Assinatura ou
- Função personalizada com a seguinte permissão:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> A função sql Server Contributor não tem permissão para eliminar cópias de segurança LTR.

As permissões Azure RBAC poderiam ser concedidas no âmbito de *subscrição* ou *grupo de recursos.* No entanto, para aceder a cópias de segurança LTR que pertencem a um servidor abandonado, a permissão deve ser concedida no âmbito de *subscrição* desse servidor.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Criar uma política LTR

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Ver as políticas LTR

Este exemplo mostra como listar as políticas LTR dentro de um servidor

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Limpar uma política LTR

Este exemplo mostra como limpar uma política LTR de uma base de dados

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Ver backups LTR

Este exemplo mostra como listar as cópias de segurança LTR dentro de um servidor.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Eliminar backups LTR

Este exemplo mostra como eliminar uma cópia de segurança LTR da lista de cópias de segurança.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Eliminar a cópia de segurança LTR não é reversível. Para eliminar uma cópia de segurança LTR depois de o servidor ter sido eliminado, tem de ter permissão de âmbito de subscrição. Pode configurar notificações sobre cada exclusão no Azure Monitor filtrando para a operação "Elimina uma cópia de segurança de retenção a longo prazo". O registo de atividades contém informações sobre quem e quando foi feito o pedido. Consulte [Criar alertas de registo de atividade](../../azure-monitor/alerts/alerts-activity-log.md) para obter instruções detalhadas.

### <a name="restore-from-ltr-backups"></a>Restauro a partir de backups LTR

Este exemplo mostra como restaurar a partir de uma cópia de segurança LTR. Nota: esta interface não mudou, mas o parâmetro de identificação de recursos requer agora o ID de recursos de backup LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Para restaurar a partir de uma cópia de segurança LTR após a eliminação do servidor ou grupo de recursos, tem de ter permissões na subscrição do servidor e essa subscrição deve estar ativa. Também deve omitir o parâmetro opcional -ResourceGroupName.

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente. Ver [ponto no tempo restaurar](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Limitações
- Ao restaurar a partir de uma cópia de segurança LTR, a propriedade de escala de leitura é desativada. Para ativar, leia a escala na base de dados restaurada, atualize a base de dados depois de criada.
- É necessário especificar o objetivo do nível de serviço alvo, ao restaurar a partir de uma cópia de segurança LTR, que foi criada quando a base de dados estava numa piscina elástica. 

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](automated-backups-overview.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](long-term-retention-overview.md)
