---
title: Faça o back up Azure Managed Disks usando Azure PowerShell
description: Aprenda a apoiar discos geridos Azure usando Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630496"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Faça o back up Azure Managed Disks usando Azure PowerShell

Este artigo explica como apoiar o [Disco Gerido Azure](../virtual-machines/managed-disks-overview.md) usando a Azure PowerShell.

Neste artigo, vai aprender a:

- Criar um cofre de reserva

- Criar uma política de cópias de segurança

- Configure uma cópia de segurança de um Disco Azure

- Executar um trabalho de reserva a pedido

Para obter informações sobre a disponibilidade da região de backup do Disco Azure, cenários e limitações apoiados, consulte a matriz de [suporte](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Criar um cofre de reserva

Um cofre de backup é uma entidade de armazenamento em Azure que contém dados de backup para várias cargas de trabalho mais recentes que o Azure Backup suporta, como a Azure Database para servidores PostgreSQL e Discos Azure. Os cofres de reserva facilitam a organização dos seus dados de backup, minimizando a sobrecarga de gestão. Os cofres de backup são baseados no modelo Azure Resource Manager da Azure, que fornece capacidades melhoradas para ajudar a proteger dados de backup.

Antes de criar um cofre de reserva, escolha a redundância de armazenamento dos dados dentro do cofre. Em seguida, continue a criar o cofre de reserva com a redundância de armazenamento e a localização. Neste artigo, vamos criar um cofre de reserva "TestBkpVault" na região "westus" sob o grupo de recursos "testBkpVaultRG". Utilize o comando [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) para criar um cofre de reserva. Saiba mais sobre [a criação de um cofre de reserva.](./backup-vault-overview.md#create-a-backup-vault)

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Depois da criação do cofre, vamos criar uma política de reserva para proteger os discos do Azure.

## <a name="create-a-backup-policy"></a>Criar uma política de backup

Para compreender os componentes internos de uma política de backup para a cópia de segurança do disco Azure, recupere o modelo de política utilizando o comando [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Este comando devolve um modelo de política predefinido para um determinado tipo de fonte de dados. Use este modelo de política para criar uma nova política.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

O modelo de política consiste num gatilho (que decide o que aciona a cópia de segurança) e um ciclo de vida (que decide quando eliminar/copiar/mover a cópia/ mover a cópia). Na cópia de segurança do disco Azure, o valor predefinido para o gatilho é um gatilho de hora a hora programado para cada 4 horas (PT4H) e para reter cada cópia de segurança durante 7 dias.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

O Azure Disk Backup oferece várias cópias de segurança por dia. Se necessitar de cópias de segurança mais frequentes, escolha a frequência de backup **hourly** com a capacidade de fazer backups com intervalos de cada 4, 6, 8 ou 12 horas. As cópias de segurança são programadas com base no intervalo **de tempo** selecionado. Por exemplo, se selecionar **De 4 em 4 horas,** as cópias de segurança são tomadas aproximadamente no intervalo de cada 4 horas para que as cópias de segurança sejam distribuídas igualmente ao longo do dia. Se uma cópia de segurança uma vez por dia for suficiente, então escolha a frequência de backup **Diária.** Na frequência de backup diária, pode especificar a hora do dia quando as suas cópias de segurança são tomadas. É importante notar que a hora do dia indica a hora de início de backup e não a hora em que a cópia de segurança termina. O tempo necessário para completar a operação de backup depende de vários fatores, incluindo o tamanho do disco, e a taxa de churn entre cópias de segurança consecutivas. No entanto, a cópia de segurança do Azure Disk é uma cópia de segurança sem agente que utiliza [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md), o que não afeta o desempenho da aplicação de produção.

   >[!NOTE]
   > Embora o cofre selecionado possa ter a definição global de redundância, atualmente a Azure Disk Backup suporta apenas a loja de dados instantânea. Todas as cópias de segurança são armazenadas num grupo de recursos na sua subscrição e não são copiadas para o armazenamento de cofre de reserva.

Para saber mais detalhes sobre a criação de políticas, consulte o documento [de política de backup do disco](backup-managed-disks.md#create-backup-policy) azul.

Se pretender editar a frequência horária ou o período de retenção, utilize os comandos [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) e/ou [Edit-AzDataProtectionPolicyRetentionRuleClientObject.](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) Uma vez que o objeto político tenha todos os valores desejados, proceda à criação de uma nova política a partir do objeto político utilizando a [Nova AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Configurar a cópia de segurança

Uma vez criado o cofre e a política, existem 3 pontos críticos que o utilizador precisa de considerar para proteger um disco Azure.

### <a name="key-entities-involved"></a>Principais entidades envolvidas

#### <a name="disk-to-be-protected"></a>Disco a proteger

Pegue a identificação do braço do disco para ser protegida. Isto servirá como o identificador do disco. Usaremos um exemplo de um disco chamado "PSTestDisk" sob um grupo de recursos "diskrg" sob uma subscrição diferente.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Grupo de recursos snapshot

As imagens do disco são armazenadas num grupo de recursos dentro da sua subscrição. Como orientação, é recomendado criar um grupo de recursos dedicado como uma loja de dados instantânea para ser usado pelo serviço Azure Backup. Ter um grupo de recursos dedicado permite restringir as permissões de acesso ao grupo de recursos, proporcionando segurança e facilidade de gestão dos dados de backup. Note o ID ARM para o grupo de recursos onde deseja colocar as fotos do disco

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Cofre de cópias de segurança

Os cofres de reserva requerem permissões no disco e no grupo de recursos instantâneos para poderem desencadear instantâneos e gerir o seu ciclo de vida. A identidade gerida do cofre atribuída pelo sistema é usada para atribuir tais permissões. Utilize o comando [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) para permitir a identidade gerida atribuída pelo sistema para o cofre dos serviços de recuperação.

### <a name="assign-permissions"></a>Atribuir permissões

O utilizador precisa de atribuir poucas permissões via RBAC ao cofre (representado pelo MSI do cofre) e ao disco relevante e/ou ao disco RG. Estes podem ser realizados através do Portal ou do PowerShell. Todas as permissões relacionadas são detalhadas nos pontos 1,2,3 [nesta secção.](backup-managed-disks.md#configure-backup)

### <a name="prepare-the-request"></a>Prepare o pedido

Uma vez definidas todas as permissões relevantes, a configuração da cópia de segurança é executada em 2 etapas. Em primeiro lugar, preparamos o pedido relevante utilizando o grupo de recursos de cofre, política, disco e instantâneo relevante utilizando o comando [Initialize-AzDataProtectionBackupInstance.](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) Em seguida, submetemos o pedido para proteger o disco utilizando o comando [New-AzDataProtectionBackupInstance.](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Executar uma cópia de segurança a pedido

Obtenha a instância de backup relevante em que o utilizador deseja desencadear uma cópia de segurança utilizando o [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Pode especificar uma regra de retenção enquanto ativa a cópia de segurança. Para ver as regras de retenção na política, navegue através do objeto político para regras de retenção. No exemplo abaixo, a regra com o nome 'predefinido' é apresentada e usaremos essa regra para a cópia de segurança a pedido

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Desencadeie uma cópia de segurança a pedido utilizando o comando [Backup-AzDataProtectionBackupInstanceAdhoc.](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Trabalhos de rastreio

Acompanhe todos os trabalhos utilizando o comando [Get-AzDataProtectionJob.](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) Pode listar todos os empregos e obter um determinado destacamento de emprego.

Também pode usar o Az.ResourceGraph para rastrear todos os trabalhos em todos os cofres de reserva. Utilize o comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) para obter o trabalho relevante que pode ser através de qualquer cofre de backup.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Passos seguintes

- [Restaurar discos geridos Azure usando Azure PowerShell](restore-managed-disks-ps.md)
