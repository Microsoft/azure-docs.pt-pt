---
title: Fazer backup e recuperar VMs do Azure usando o backup do Azure com o PowerShell
description: Descreve como fazer backup e recuperar VMs do Azure usando o backup do Azure com o PowerShell
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: dacurwin
ms.openlocfilehash: 91e71e2ab4c028e44f667133237cefb2263ae49a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969066"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Fazer backup e restaurar VMs do Azure com o PowerShell

Este artigo explica como fazer backup e restaurar uma VM do Azure em um cofre de serviços de recuperação de [backup do Azure](backup-overview.md) usando cmdlets do PowerShell.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie um cofre dos serviços de recuperação e defina o contexto do cofre.
> * Definir uma política de cópias de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Disparar um trabalho de backup sob demanda para as máquinas virtuais protegidas antes de poder fazer backup (ou proteger) uma máquina virtual, você deve concluir os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar seu ambiente para proteger suas VMs.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos serviços de recuperação.
* [Examine](backup-architecture.md#architecture-direct-backup-of-azure-vms) a arquitetura do backup de VM do Azure, [saiba mais sobre](backup-azure-vms-introduction.md) o processo de backup e [examine](backup-support-matrix-iaas.md) o suporte, as limitações e os pré-requisitos.
* Examine a hierarquia de objetos do PowerShell para serviços de recuperação.

## <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos serviços de recuperação

A hierarquia de objetos é resumida no diagrama a seguir.

![Hierarquia de objetos dos serviços de recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Examine a referência de [referência do cmdlet](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) **AZ. recoveryservices** na biblioteca do Azure.

## <a name="set-up-and-register"></a>Configurar e registrar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar:

1. [Baixe a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Encontre os cmdlets do PowerShell de backup do Azure disponíveis digitando o seguinte comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Os aliases e cmdlets para o backup do Azure, Azure Site Recovery e o cofre dos serviços de recuperação são exibidos. A imagem a seguir é um exemplo do que você verá. Não é a lista completa de cmdlets.

    ![lista de serviços de recuperação](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Entre em sua conta do Azure usando **Connect-AzAccount**. Esse cmdlet abre uma página da Web que solicita suas credenciais de conta:

    * Como alternativa, você pode incluir suas credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount** , usando o parâmetro **-Credential** .
    * Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Por exemplo: **Connect-AzAccount-Tenant "fabrikam.com"**

4. Associe a assinatura que você deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se você estiver usando o backup do Azure pela primeira vez, deverá usar o cmdlet **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** para registrar o provedor de serviços de recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Você pode verificar se os provedores foram registrados com êxito, usando os seguintes comandos:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Na saída do comando, o **registrostate** deve mudar para **registrado**. Caso contrário, basta executar o cmdlet **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** novamente.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

As etapas a seguir orientarão você na criação de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é diferente de um cofre de backup.

1. O cofre dos serviços de recuperação é um recurso do Resource Manager, portanto, você precisa colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** . Ao criar um grupo de recursos, especifique o nome e o local do grupo de recursos.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Use o cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) para criar o cofre dos serviços de recuperação. Certifique-se de especificar o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância de armazenamento a ser usado; Você pode usar o [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy-lrs.md) ou o [grs (armazenamento com redundância geográfica)](../storage/common/storage-redundancy-grs.md). O exemplo a seguir mostra a opção-BackupStorageRedundancy para testvault é definida como georedundante.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres na assinatura, use [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante ao exemplo a seguir, observe que o ResourceGroupName e o local associados são fornecidos.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Fazer uma cópia de segurança de VMs do Azure

Use um cofre dos serviços de recuperação para proteger suas máquinas virtuais. Antes de aplicar a proteção, defina o contexto do cofre (o tipo de dados protegidos no cofre) e verifique a política de proteção. A política de proteção é a agenda quando os trabalhos de backup são executados e por quanto tempo cada instantâneo de backup é retido.

### <a name="set-vault-context"></a>Definir contexto do cofre

Antes de habilitar a proteção em uma VM, use [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) para definir o contexto do cofre. Quando o contexto do cofre estiver definido, é aplicado a todos os cmdlets subsequentes. O exemplo a seguir define o contexto do cofre para o cofre, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Buscar a ID do cofre

Planejamos substituir a configuração de contexto do cofre de acordo com as diretrizes de Azure PowerShell. Em vez disso, você pode armazenar ou buscar a ID do cofre e passá-la para comandos relevantes. Portanto, se você não tiver definido o contexto do cofre ou quiser especificar o comando a ser executado para um determinado cofre, passe a ID do cofre como "-vaultid" para todo o comando relevante, da seguinte maneira:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Ou

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Modificando configurações de replicação de armazenamento

Use o comando [set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) para definir a configuração de replicação de armazenamento do cofre como LRS/grs

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> A redundância de armazenamento só poderá ser modificada se não houver itens de backup protegidos ao cofre.

### <a name="create-a-protection-policy"></a>Criar uma política de proteção

Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas. A política de proteção predefinida aciona um trabalho de cópia de segurança todos os dias a uma hora especificada. A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias. Você pode usar a política padrão para proteger rapidamente sua VM e editar a política mais tarde com detalhes diferentes.

Use **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** para exibir as políticas de proteção disponíveis no cofre. Você pode usar este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho. O exemplo a seguir obtém as políticas para o tipo de carga de trabalho, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

O resultado é semelhante ao seguinte exemplo:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> O fuso horário do campo Backuptime no PowerShell é UTC. No entanto, quando o horário do backup é mostrado na portal do Azure, a hora é ajustada para o fuso horário local.
>
>

Uma política de proteção de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido antes de ser excluído.

* Use [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) para exibir a política de retenção padrão.
* Da mesma forma, você pode usar [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) para obter a política de agendamento padrão.
* O cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cria um objeto do PowerShell que mantém as informações da política de backup.
* Os objetos de política de retenção e agendamento são usados como entradas para o cmdlet New-AzRecoveryServicesBackupProtectionPolicy.

Por padrão, uma hora de início é definida no objeto de política de agenda. Use o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada também deve estar em UTC. O exemplo abaixo pressupõe que a hora de início desejada seja 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Você precisa fornecer a hora de início apenas em 30 minutos. No exemplo acima, ele pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00"

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. O exemplo usa essas variáveis para definir os parâmetros ao criar uma política de proteção, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

O resultado é semelhante ao seguinte exemplo:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Ativar a proteção

Depois de definir a política de proteção, você ainda deve habilitar a política para um item. Use [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) para habilitar a proteção. A habilitação da proteção requer dois objetos – o item e a política. Depois que a política tiver sido associada ao cofre, o fluxo de trabalho de backup será disparado no momento definido no agendamento da política.

> [!IMPORTANT]
> Ao usar o PS para habilitar o backup para várias VMs de uma vez, verifique se uma única política não tem mais de 100 VMs associadas a ela. Essa é uma [prática](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy)recomendada. Atualmente, o cliente PS não bloqueia explicitamente se há mais de 100 VMs, mas a verificação está planejada para ser adicionada no futuro.

Os exemplos a seguir habilitam a proteção para o item, V2VM, usando a política, NewPolicy. Os exemplos diferem com base em se a VM está criptografada e qual tipo de criptografia.

Para habilitar a proteção em **VMs do Resource Manager não criptografadas**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para habilitar a proteção em VMs criptografadas (criptografadas usando BEK e KEK), você deve dar permissão ao serviço de backup do Azure para ler chaves e segredos do cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para habilitar a proteção em **VMs criptografadas (criptografadas usando apenas Bek)** , você deve dar permissão ao serviço de backup do Azure para ler segredos do cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Se você estiver usando a nuvem do Azure governamental, use o valor ff281ffe-705c-4f53-9f37-a40e6f2c68f3 para o parâmetro servicePrincipalName no cmdlet [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .
>

## <a name="monitoring-a-backup-job"></a>Monitorando um trabalho de backup

Você pode monitorar operações de execução longa, como trabalhos de backup, sem usar o portal do Azure. Para obter o status de um trabalho em andamento, use o cmdlet [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) . Esse cmdlet obtém os trabalhos de backup para um cofre específico e esse cofre é especificado no contexto do cofre. O exemplo a seguir obtém o status de um trabalho em andamento como uma matriz e armazena o status na variável $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

O resultado é semelhante ao seguinte exemplo:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Em vez de sondar esses trabalhos para conclusão – que é um código adicional desnecessário, use o cmdlet [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) . Esse cmdlet pausa a execução até que o trabalho seja concluído ou o valor de tempo limite especificado seja atingido.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Gerir as cópias de segurança de VMs do Azure

### <a name="modify-a-protection-policy"></a>Modificar uma política de proteção

Para modificar a política de proteção, use [set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) para modificar os objetos SchedulePolicy ou RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Modificando a hora agendada

Quando você cria uma política de proteção, ela recebe uma hora de início por padrão. Os exemplos a seguir mostram como modificar a hora de início de uma política de proteção.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Modificando a retenção

O exemplo a seguir altera a retenção do ponto de recuperação para 365 dias.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Configurando a retenção de instantâneo de restauração instantânea

> [!NOTE]
> De AZ PS versão 1.6.0 em diante, é possível atualizar o período de retenção do instantâneo de restauração instantânea na política usando o PowerShell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

O valor padrão será 2, o usuário poderá definir o valor com um mínimo de 1 e o máximo de 5. Para políticas de backup semanais, o período é definido como 5 e não pode ser alterado.

### <a name="trigger-a-backup"></a>Disparar um backup

Use [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) para disparar um trabalho de backup. Se for o backup inicial, ele será um backup completo. Os backups subsequentes usam uma cópia incremental. O exemplo a seguir usa um backup de VM para ser retido por 60 dias.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

O resultado é semelhante ao seguinte exemplo:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> O fuso horário dos campos StartTime e EndTime no PowerShell é UTC. No entanto, quando a hora é mostrada na portal do Azure, a hora é ajustada para o fuso horário local.
>
>

### <a name="change-policy-for-backup-items"></a>Alterar política para itens de backup

O usuário pode modificar a política existente ou alterar a política do item de backup de Policy1 para Policy2. Para alternar políticas para um item de backup, busque a política relevante e faça backup do item e use o comando [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) com o item de backup como o parâmetro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

O comando aguarda até que o backup de configuração seja concluído e retorna a saída a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Interromper proteção

#### <a name="retain-data"></a>Reter dados

Se o usuário quiser interromper a proteção, ele poderá usar o cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) do PS. Isso interromperá os backups agendados, mas os dados submetidos a backup até agora são mantidos para sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Para remover completamente os dados de backup armazenados no cofre, basta adicionar o sinalizador de '-RemoveRecoveryPoints '/alternar para o [comando de proteção ' Disable '](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Restaurar uma VM do Azure

Há uma diferença importante entre a restauração de uma VM usando o portal do Azure e a restauração de uma VM usando o PowerShell. Com o PowerShell, a operação de restauração é concluída quando os discos e as informações de configuração do ponto de recuperação são criados. A operação de restauração não cria a máquina virtual. Para criar uma máquina virtual do disco, consulte a seção [criar a VM de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se você não quiser restaurar toda a VM, mas desejar restaurar ou recuperar alguns arquivos de um backup de VM do Azure, consulte a [seção recuperação de arquivo](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A operação de restauração não cria a máquina virtual.
>
>

O gráfico a seguir mostra a hierarquia de objetos do RecoveryServicesVault até o BackupRecoveryPoint.

![Hierarquia de objetos dos serviços de recuperação mostrando BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar os dados de backup, identifique o item de backup e o ponto de recuperação que contém os dados pontuais. Use [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar dados do cofre para sua conta.

As etapas básicas para restaurar uma VM do Azure são:

* Selecione a VM.
* Escolha um ponto de recuperação.
* Restaure os discos.
* Crie a VM de discos armazenados.

### <a name="select-the-vm"></a>Selecionar a VM

Para obter o objeto do PowerShell que identifica o item de backup correto, inicie no contêiner no cofre e faça o seu sentido para baixo na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e redirecione-o para o cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Escolher um ponto de recuperação

Use o cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação do item de backup. Em seguida, escolha o ponto de recuperação a ser restaurado. Se você não tiver certeza de qual ponto de recuperação usar, é uma boa prática escolher o ponto RecoveryPointType = AppConsistent mais recente na lista.

No script a seguir, a variável, **$RP**, é uma matriz de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para escolher o ponto de recuperação. No exemplo, $rp [0] seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

O resultado é semelhante ao seguinte exemplo:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Restaurar os discos

Use o cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar os dados e a configuração de um item de backup para um ponto de recuperação. Depois de identificar um ponto de recuperação, use-o como o valor para o parâmetro **-RecoveryPoint** . No exemplo acima, **$RP [0]** foi o ponto de recuperação a ser usado. No código de exemplo a seguir, **$RP [0]** é o ponto de recuperação a ser usado para restaurar o disco.

Para restaurar os discos e as informações de configuração:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Restaurar discos gerenciados

> [!NOTE]
> Se a VM com suporte tiver discos gerenciados e você quiser restaurá-los como discos gerenciados, apresentamos a capacidade de Azure PowerShell módulo do RM v 6.7.0. em diante
>
>

Forneça um parâmetro adicional **TargetResourceGroupName** para especificar o RG para o qual os discos gerenciados serão restaurados.

> [!NOTE]
> É altamente recomendável usar o parâmetro **TargetResourceGroupName** para restaurar discos gerenciados, pois isso resulta em melhorias significativas de desempenho. Além disso, do Azure PowerShell AZ Module 1,0 em diante, esse parâmetro é obrigatório no caso de uma restauração com discos gerenciados
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

O arquivo **VMConfig. JSON** será restaurado para a conta de armazenamento e os discos gerenciados serão restaurados para o RG de destino especificado.

O resultado é semelhante ao seguinte exemplo:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Use o cmdlet [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) para aguardar a conclusão do trabalho de restauração.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Depois que o trabalho de restauração for concluído, use o cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) para obter os detalhes da operação de restauração. A propriedade JobDetails tem as informações necessárias para recriar a VM.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Depois de restaurar os discos, vá para a próxima seção para criar a VM.

## <a name="replace-disks-in-azure-vm"></a>Substituir discos na VM do Azure

Para substituir os discos e as informações de configuração, execute as etapas a seguir:

* Etapa 1: [restaurar os discos](backup-azure-vms-automation.md#restore-the-disks)
* Etapa 2: [desanexar o disco de dados usando o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
* Etapa 3: [anexar o disco de dados à VM do Windows com o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>Criar uma VM com base em discos restaurados

Depois de restaurar os discos, use as etapas a seguir para criar e configurar a máquina virtual do disco.

> [!NOTE]
> Para criar VMs criptografadas a partir de discos restaurados, sua função do Azure deve ter permissão para executar a ação, **Microsoft. keyvault/cofres/implantar/ação**. Se sua função não tiver essa permissão, crie uma função personalizada com essa ação. Para obter mais informações, consulte [funções personalizadas no RBAC do Azure](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Depois de restaurar os discos, agora você pode obter um modelo de implantação que pode ser usado diretamente para criar uma nova VM. Não há mais cmdlets de PS diferentes para criar VMs gerenciadas/não gerenciadas que são criptografadas/descriptografadas.

Os detalhes do trabalho resultante fornecem o URI do modelo que pode ser consultado e implantado.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Basta implantar o modelo para criar uma nova VM, conforme explicado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

A seção a seguir lista as etapas necessárias para criar uma VM usando o arquivo "VMConfig".

> [!NOTE]
> É altamente recomendável usar o modelo de implantação detalhado acima para criar uma VM. Esta seção (pontos 1-6) será preterida em breve.

1. Consulte as propriedades do disco restaurado para obter os detalhes do trabalho.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Defina o contexto de armazenamento do Azure e restaure o arquivo de configuração JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Use o arquivo de configuração JSON para criar a configuração da VM.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Anexe o disco do sistema operacional e os discos de dados. Esta etapa fornece exemplos para várias configurações de VM gerenciadas e criptografadas. Use o exemplo que atenda à sua configuração de VM.

   * **VMs não gerenciadas e não criptografadas** – use o exemplo a seguir para VMs não gerenciadas e não criptografadas.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **VMs não gerenciadas e criptografadas com o Azure AD (somente Bek)** – para VMs criptografadas não gerenciadas com o Azure AD (criptografado usando apenas Bek), você precisa restaurar o segredo para o cofre de chaves antes de poder anexar discos. Para obter mais informações, consulte [restaurar uma máquina virtual criptografada de um ponto de recuperação de backup do Azure](backup-azure-restore-key-secret.md). O exemplo a seguir mostra como anexar o sistema operacional e discos de dados para VMs criptografadas. Ao definir o disco do sistema operacional, certifique-se de mencionar o tipo de sistema operacional relevante.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **VMs não gerenciadas e criptografadas com o Azure AD (Bek e Kek)** – para VMs criptografadas não gerenciadas com o Azure AD (criptografado usando Bek e Kek), restaure a chave e o segredo para o cofre de chaves antes de anexar os discos. Para obter mais informações, consulte [restaurar uma máquina virtual criptografada de um ponto de recuperação do backup do Azure](backup-azure-restore-key-secret.md). O exemplo a seguir mostra como anexar o sistema operacional e discos de dados para VMs criptografadas.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **VMs não gerenciadas e criptografadas sem o AD do Azure (somente Bek)** – para VMs criptografadas não gerenciadas sem o Azure AD (criptografado usando apenas Bek), se o **keyvault/segredo de origem não estiver disponível** , restaure os segredos para o Key Vault usando o procedimento em [ Restaurar uma máquina virtual não criptografada de um ponto de recuperação do backup do Azure](backup-azure-restore-key-secret.md). Em seguida, execute os scripts a seguir para definir detalhes de criptografia no blob restaurado do sistema operacional (essa etapa não é necessária para o blob de dados). O $dekurl pode ser obtido a partir do keyvault restaurado.<br>

   O script abaixo precisa ser executado somente quando o keyvault/segredo de origem não estiver disponível.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Depois que os **segredos estiverem disponíveis** e os detalhes de criptografia também estiverem definidos no blob do sistema operacional, anexe os discos usando o script fornecido abaixo.<br>

    Se o keyvault/segredos de origem já estiverem disponíveis, o script acima não precisará ser executado.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **VMs não gerenciadas e criptografadas sem o Azure AD (Bek e Kek)** – para VMs criptografadas não gerenciadas sem o Azure AD (criptografado usando Bek & Kek), se o **keyvault/chave/segredo de origem não estiver disponível** , restaure a chave e os segredos para o Key Vault usando o procedimento em [restaurar uma máquina virtual não criptografada de um ponto de recuperação do backup do Azure](backup-azure-restore-key-secret.md). Em seguida, execute os scripts a seguir para definir detalhes de criptografia no blob restaurado do sistema operacional (essa etapa não é necessária para o blob de dados). O $dekurl e $kekurl podem ser obtidos do cofre de chaves restaurado.

   O script abaixo precisa ser executado somente quando o keyvault/chave/segredo de origem não estiver disponível.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

   Depois que a **chave/os segredos estiverem disponíveis** e os detalhes de criptografia estiverem definidos no blob do sistema operacional, anexe os discos usando o script fornecido abaixo.

    Se o keyvault/chave/segredos de origem estiverem disponíveis, o script acima não precisará ser executado.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **VMs gerenciadas e não criptografadas** – para VMs não criptografadas gerenciadas, anexe os Managed disks restaurados. Para obter informações detalhadas, consulte [anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **VMs gerenciadas e criptografadas com o Azure AD (somente Bek)** – para VMs criptografadas gerenciadas com o Azure AD (criptografado usando apenas Bek), anexe os Managed disks restaurados. Para obter informações detalhadas, consulte [anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **VMs gerenciadas e criptografadas com o Azure AD (Bek e Kek)** – para VMs criptografadas gerenciadas com o Azure AD (criptografado usando Bek e Kek), anexe os Managed disks restaurados. Para obter informações detalhadas, consulte [anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **VMs gerenciadas e criptografadas sem o AD do Azure (somente Bek)** – para VMs criptografadas e gerenciadas sem o Azure AD (criptografado usando apenas Bek), se o **keyvault/segredo de origem não estiver disponível** , restaure os segredos para o Key Vault usando o procedimento em [restaurar um máquina virtual não criptografada de um ponto de recuperação de backup do Azure](backup-azure-restore-key-secret.md). Em seguida, execute os scripts a seguir para definir detalhes de criptografia no disco do sistema operacional restaurado (essa etapa não é necessária para o disco de dados). O $dekurl pode ser obtido a partir do keyvault restaurado.

     O script abaixo precisa ser executado somente quando o keyvault/segredo de origem não estiver disponível.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     Depois que os segredos estiverem disponíveis e os detalhes de criptografia estiverem definidos no disco do sistema operacional, para anexar os discos gerenciados restaurados, consulte [anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **VMs gerenciadas e criptografadas sem o Azure AD (Bek e Kek)** – para VMs criptografadas e gerenciadas sem o Azure AD (criptografado usando Bek & Kek), se o **keyvault de origem/chave/segredo não estiverem disponíveis** , restaure a chave e os segredos para o Key Vault usando o procedimento em [Restaurar uma máquina virtual não criptografada de um ponto de recuperação do backup do Azure](backup-azure-restore-key-secret.md). Em seguida, execute os scripts a seguir para definir detalhes de criptografia no disco do sistema operacional restaurado (essa etapa não é necessária para o disco de dados). O $dekurl e $kekurl podem ser obtidos do cofre de chaves restaurado.

   O script abaixo precisa ser executado somente quando o keyvault/chave/segredo de origem não estiver disponível.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Depois que a chave/os segredos estiverem disponíveis e os detalhes de criptografia estiverem definidos no disco do sistema operacional, para anexar os discos gerenciados restaurados, consulte [anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Defina as configurações de rede.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Criar a máquina virtual.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Extensão de ADE Push.
   Se as extensões de ADE não forem enviadas por push, os discos de dados serão marcados como não criptografados, portanto, é obrigatório que as etapas abaixo sejam executadas:

   * **Para VM com o Azure ad** – use o seguinte comando para habilitar manualmente a criptografia para os discos de dados  

     **Somente BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Para VM sem Azure ad** -use o seguinte comando para habilitar manualmente a criptografia para os discos de dados.

     Se durante a execução do comando solicitar AADClientID, você precisará atualizar seu Azure PowerShell.

     **Somente BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Certifique-se de excluir manualmente os arquivos JASON criados como parte do processo de disco de restauração de VM criptografado.

## <a name="restore-files-from-an-azure-vm-backup"></a>Restaurar arquivos de um backup de VM do Azure

Além de restaurar discos, você também pode restaurar arquivos individuais de um backup de VM do Azure. A funcionalidade restaurar arquivos fornece acesso a todos os arquivos em um ponto de recuperação. Gerencie os arquivos por meio do explorador de arquivos como você faria para arquivos normais.

As etapas básicas para restaurar um arquivo de um backup de VM do Azure são:

* Selecionar a VM
* Escolher um ponto de recuperação
* Montar os discos do ponto de recuperação
* Copiar os arquivos necessários
* Desmontar o disco

### <a name="select-the-vm"></a>Selecionar a VM

Para obter o objeto do PowerShell que identifica o item de backup correto, inicie no contêiner no cofre e faça o seu sentido para baixo na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e redirecione-o para o cmdlet [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Escolher um ponto de recuperação

Use o cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação do item de backup. Em seguida, escolha o ponto de recuperação a ser restaurado. Se você não tiver certeza de qual ponto de recuperação usar, é uma boa prática escolher o ponto RecoveryPointType = AppConsistent mais recente na lista.

No script a seguir, a variável, **$RP**, é uma matriz de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para escolher o ponto de recuperação. No exemplo, $rp [0] seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

O resultado é semelhante ao seguinte exemplo:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Montar os discos do ponto de recuperação

Use o cmdlet [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) para obter o script para montar todos os discos do ponto de recuperação.

> [!NOTE]
> Os discos são montados como discos conectados iSCSI no computador onde o script é executado. A montagem ocorre imediatamente e você não incorre em encargos.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

O resultado é semelhante ao seguinte exemplo:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Execute o script no computador em que você deseja recuperar os arquivos. Para executar o script, você deve inserir a senha fornecida. Depois que os discos forem anexados, use o explorador de arquivos do Windows para procurar os novos volumes e arquivos. Para obter mais informações, consulte o artigo de backup, [recuperar arquivos do backup de máquina virtual do Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Desmontar os discos

Depois que os arquivos necessários forem copiados, use [Disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) para desmontar os discos. Não se esqueça de desmontar os discos para que o acesso aos arquivos do ponto de recuperação seja removido.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Passos seguintes

Se você preferir usar o PowerShell para se envolver com os recursos do Azure, consulte o artigo do PowerShell, [implantar e gerenciar o backup do Windows Server](backup-client-automation.md). Se você gerenciar backups do DPM, consulte o artigo [implantar e gerenciar backup do DPM](backup-dpm-automation.md).
