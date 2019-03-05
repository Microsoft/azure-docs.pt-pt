---
title: Configurar retenção de cópia de segurança longa duração - base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como armazenar cópias de segurança automáticas no cofre dos serviços de recuperação do Azure e restaurar a partir do Cofre de serviços de recuperação do Azure
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma,carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: efdcc4c8424fc1c330ac901bb42ec89897241d79
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316613"
---
# <a name="configure-long-term-backup-retention-using-azure-recovery-services-vault"></a>Configurar retenção de cópia de segurança de longa duração com o Cofre dos serviços de recuperação do Azure

Pode configurar o Cofre de serviços de recuperação do Azure para armazenar cópias de segurança de base de dados de SQL do Azure e, em seguida, recuperar uma base de dados com cópias de segurança retidas no cofre com o portal do Azure ou o PowerShell.

> [!NOTE]
> Como parte da versão inicial de pré-visualização de retenção de cópia de segurança de longa duração, em Outubro de 2016, as cópias de segurança foram armazenadas no Cofre de serviços de recuperação do Azure. Esta atualização remove esta dependência, mas para compatibilidade com versões anteriores, a API original é suportada até 31 de Maio de 2018. Se precisar de interagir com as cópias de segurança no Cofre de recuperação de serviços do Azure, veja [retenção de cópia de segurança de longa duração com o Cofre de serviços de recuperação do Azure](sql-database-long-term-backup-retention-configure-vault.md).

## <a name="azure-portal"></a>Portal do Azure

As secções seguintes mostram como utilizar o portal do Azure para configurar o Cofre dos serviços de recuperação do Azure, veja as cópias de segurança no cofre e restauro do cofre.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>Configurar o cofre, registar o servidor e selecione bases de dados

Configurar um cofre de serviços de recuperação do Azure para [reter cópias de segurança automáticas](sql-database-long-term-retention.md) durante um período maior do que o período de retenção para a camada de serviço.

1. Abra o **SQL Server** página do seu servidor.

   ![página do SQL server](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Clique em **Retenção de longa duração de cópia de segurança**.

   ![ligação de retenção de longa duração de cópia de segurança](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Sobre o **retenção de cópia de segurança de longo prazo** página para o seu servidor, reveja e aceite os termos de pré-visualização (a menos que ainda tiver feito - ou esta funcionalidade já não está em pré-visualização).

   ![aceite os termos de pré-visualização](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Para configurar a retenção de cópia de segurança de longo prazo, selecione essa base de dados na grade e, em seguida, clique em **configurar** na barra de ferramentas.

   ![selecionar base de dados para retenção de longa duração de cópia de segurança](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Sobre o **configurar** página, clique em **configurar definições necessárias** sob **Cofre de serviço de recuperação**.

   ![configurar ligação ao cofre](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Sobre o **cofre dos Recovery services** , selecione um cofre existente, se houver. Caso contrário, se não for encontrado nenhum cofre dos serviços de recuperação para a sua subscrição, clique para sair do fluxo e criar um cofre dos serviços de recuperação.

   ![criar a ligação ao Cofre](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Sobre o **cofres dos serviços de recuperação** página, clique em **Add**.

   ![Adicionar ligação ao Cofre](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)

8. Sobre o **cofre dos serviços de recuperação** , indique um nome válido para o Cofre dos serviços de recuperação.

   ![nome do novo cofre](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Selecione a sua subscrição e o grupo de recursos e, em seguida, selecione a localização do cofre. Quando terminar, clique em **Guardar**.

   ![Criar Cofre](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > O Cofre tem de estar localizado na mesma região que o servidor de base de dados SQL e tem de utilizar o mesmo grupo de recursos que o servidor de base de dados SQL.

10. Depois de criar o novo cofre, execute os passos necessários para voltar para o **cofre dos Recovery services** página.

11. Sobre o **cofre dos serviços de recuperação** página, clique no cofre e, em seguida, clique em **selecione**.

   ![selecionar o cofre existente](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Sobre o **configurar** página, forneça um nome válido para a nova política de retenção, modificar a política de retenção predefinida conforme adequado e, em seguida, clique em **OK**.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

   > [!NOTE]
   > Os nomes da política de retenção não permitem alguns caracteres, incluindo espaços.

13. Na **retenção de cópia de segurança de longa duração** da base de dados, clique em **guardar** e, em seguida, clique em **OK** para aplicar a política de retenção de cópia de segurança de longo prazo para bases de dados de contas selecionadas.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Clique em **Guardar** para ativar a retenção de longa duração de cópia de segurança com esta nova política para o cofre dos Serviços de Recuperação do Azure que configurou.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> Depois de configuradas, as cópias de segurança aparecem no cofre nos próximos sete dias. Não continue este tutorial até que as cópias de segurança apareçam no cofre.

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Ver cópias de segurança em retenção de longo prazo através do portal do Azure

Ver informações sobre as cópias de segurança da base de dados no [retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md).

1. No portal do Azure, abra o Cofre de serviços de recuperação do Azure para as cópias de segurança da base de dados (aceda a **todos os recursos** e selecione-o na lista de recursos para a sua subscrição) para ver a quantidade de armazenamento utilizado pelas suas cópias de segurança da base de dados no cofre.

   ![ver o cofre dos serviços de recuperação com cópias de segurança](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Abra o **base de dados SQL** página da base de dados.

   ![nova página de BD de exemplo](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Na barra de ferramentas, clique em **Restaurar**.

   ![barra de ferramentas de restauro](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Na página de restauro, clique em **longo prazo**.

5. Nas cópias de segurança do cofre do Azure, clique em **Selecionar uma cópia de segurança** para ver as cópias de segurança da base de dados disponíveis na retenção de longa duração de cópia de segurança.

   ![cópias de segurança no cofre](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Restaurar uma base de dados a partir de uma cópia de segurança em retenção de cópia de segurança de longa duração com o portal do Azure

Restaurar a base de dados para uma nova base de dados a partir de uma cópia de segurança no cofre dos serviços de recuperação do Azure.

1. Sobre o **cópias de segurança do cofre do Azure** página, clique em cópia de segurança para restaurar e, em seguida, clique em **selecione**.

   ![Selecione a cópia de segurança no cofre](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Na caixa de texto **Nome da base de dados**, forneça o nome da base de dados restaurada.

   ![nome da nova base de dados](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Clique em **OK** para restaurar a base de dados a partir da cópia de segurança no cofre para a nova base de dados.

4. Na barra de ferramentas, clique no ícone de notificação para ver o estado da tarefa de restauro.

   ![progresso da tarefa de restauro a partir do cofre](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Quando a tarefa de restauro está concluída, abra a **bases de dados SQL** página para ver a base de dados recentemente restaurada.

   ![restaurar base de dados a partir do cofre](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As secções seguintes mostram como utilizar o PowerShell para configurar o Cofre dos serviços de recuperação do Azure, veja as cópias de segurança no cofre e restauro do cofre.

### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

Utilize o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar um cofre dos serviços de recuperação.

> [!IMPORTANT]
> O Cofre tem de estar localizado na mesma região que o servidor de base de dados SQL e tem de utilizar o mesmo grupo de recursos que o servidor de base de dados SQL.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation
Set-AzRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>Configure o servidor para utilizar o Cofre de recuperação para as suas cópias de segurança de retenção de longo prazo

Utilize o [Set-AzSqlServerBackupLongTermRetentionVault](/powershell/module/az.sql/set-azsqlserverbackuplongtermretentionvault) cmdlet para associar um cofre dos serviços de recuperação anteriormente criado com um servidor SQL do Azure específico.

```PowerShell
# Set your server to use the vault to for long-term backup retention

Set-AzSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Criar uma política de retenção

Uma política de retenção é onde define o período de tempo para manter uma cópia de segurança da base de dados. Utilizar o [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackupretentionpolicyobject) cmdlet para obter a política de retenção predefinido para utilizar como modelo para criar políticas. Neste modelo, o período de retenção é definido durante dois anos. Em seguida, execute o [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-Azrecoveryservicesbackupprotectionpolicy) finalmente criar a política.

> [!NOTE]
> Alguns cmdlets precisam que defina o contexto do cofre antes de executar ([Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)) para ver este cmdlet em alguns fragmentos relacionados. Definir o contexto porque a política faz parte do cofre. Pode criar várias políticas de retenção para cada cofre e, em seguida, aplicar a política pretendida para bases de dados específicas.

```PowerShell
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Configure uma base de dados para utilizar a política de retenção definida anteriormente

Utilize o [Set-AzSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/az.sql/set-azsqldatabasebackuplongtermretentionpolicy) cmdlet para aplicar a política nova a uma base de dados específico.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Ver informações da cópia de segurança e cópias de segurança em retenção de longa duração

Ver informações sobre as cópias de segurança da base de dados no [retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md).

Utilize os seguintes cmdlets para ver informações de cópia de segurança:

- [Get-AzRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackupcontainer)
- [Get-AzRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackupitem)
- [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-Azrecoveryservicesbackuprecoverypoint)

```PowerShell
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore

# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurar uma base de dados a partir de uma cópia de segurança em retenção de longa duração de cópia de segurança

Restaurar a partir de retenção de cópia de segurança de longo prazo utiliza a [restauro AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) cmdlet.

```PowerShell
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada com o SQL Server Management Studio para efetuar tarefas necessárias, como para extrair um pouco de dados da base de dados restaurada para copiar para a base de dados existente ou para eliminar o existente da base de dados e mudar o nome do restaurada base de dados para o nome de base de dados existente. Ver [ponto no tempo](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="how-to-cleanup-backups-in-recovery-services-vault"></a>Como as cópias de segurança de limpeza no cofre dos serviços de recuperação

A partir de 1 de Julho de 2018 foi preterida a API de V1 LTR e todas as suas cópias de segurança existentes em cofres de recuperação de serviço tiverem sido migradas para os contentores de armazenamento LTR geridos pela base de dados SQL. Para se certificar de que já não são cobrados para as cópias de segurança originais, eles foram removidos dos cofres após a migração. No entanto, se tiver colocado um bloqueio no seu Cofre de cópias de segurança serão mantida. Para evitar os custos desnecessários, pode remover manualmente as cópias de segurança antigas do cofre do serviço de recuperação com o seguinte script.

```PowerShell
<#
.EXAMPLE
    .\Drop-LtrV1Backup.ps1 -SubscriptionId “{vault_sub_id}” -ResourceGroup “{vault_resource_group}” -VaultName “{vault_name}”
#>
[CmdletBinding()]
Param (
    [Parameter(Mandatory = $true, HelpMessage="The vault subscription ID")]
    $SubscriptionId,

    [Parameter(Mandatory = $true, HelpMessage="The vault resource group name")]
    $ResourceGroup,

    [Parameter(Mandatory = $true, HelpMessage="The vault name")]
    $VaultName
)

Login-AzAccount

Select-AzSubscription -SubscriptionId $SubscriptionId

$vaults = Get-AzRecoveryServicesVault
$vault = $vaults | where { $_.Name -eq $VaultName }

Set-AzRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureSQL

ForEach ($container in $containers)
{
   $canDeleteContainer = $true
   $ItemCount = 0
   Write-Host "Working on container" $container.Name
   $items = Get-AzRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase
   ForEach ($item in $items)
   {
    write-host "Deleting item" $item.name
    Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints -item $item -Force
   }

   Write-Host "Deleting container" $container.Name
   Unregister-AzRecoveryServicesBackupContainer -Container $container
}
```

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md)
- Para saber mais sobre o restauro de cópias de segurança, veja [restaurar a partir de cópia de segurança](sql-database-recovery-using-backups.md)
