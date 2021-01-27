---
title: Recuar e recuperar VMs Azure com PowerShell
description: Descreve como fazer backup e recuperar VMs Azure usando Azure Backup com PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 90bb6f60712fc59aec05ff2e85364fccf00ff1df
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804798"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Recuar e restaurar VMs Azure com PowerShell

Este artigo explica como fazer backup e restaurar um VM Azure num cofre dos Serviços de Recuperação [de Backup Azure](backup-overview.md) usando cmdlets PowerShell.

Neste artigo, saiba como:

> [!div class="checklist"]
>
> * Crie um cofre dos Serviços de Recuperação e desemote o contexto do cofre.
> * Definir uma política de cópias de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Desempate um trabalho de backup a pedido para as máquinas virtuais protegidas Antes de poder fazer backup (ou proteger) uma máquina virtual, tem de completar os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o seu ambiente para proteger os seus VMs.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos Serviços de Recuperação.
* [Reveja](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura para o backup Azure VM, [aprenda sobre](backup-azure-vms-introduction.md) o processo de backup, e [reveja](backup-support-matrix-iaas.md) suporte, limitações e pré-requisitos.
* Reveja a hierarquia de objetos PowerShell para serviços de recuperação.

## <a name="recovery-services-object-hierarchy"></a>Serviços de Recuperação hierárquica de objetos

A hierarquia do objeto é resumida no diagrama seguinte.

![Serviços de Recuperação hierárquica de objetos](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Reveja a referência [de referência cmdlet](/powershell/module/az.recoveryservices/) **Az.RecoveryServices** na biblioteca Azure.

## <a name="set-up-and-register"></a>Configurar e registar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar:

1. [Descarregue a versão mais recente do PowerShell](/powershell/azure/install-az-ps)

2. Encontre os cmdlets PowerShell de backup Azure disponíveis digitando o seguinte comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Aparecem os pseudónimos e cmdlets para a Azure Backup, Azure Site Recovery e o cofre dos Serviços de Recuperação. A imagem a seguir é um exemplo do que vai ver. Não é a lista completa de comandantes.

    ![lista de Serviços de Recuperação](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Inscreva-se na sua conta Azure utilizando **o Connect-AzAccount**. Este cmdlet traz uma página web que lhe solicita as credenciais da sua conta:

    * Alternadamente, pode incluir as suas credenciais de conta como parâmetro no cmdlet **Connect-AzAccount,** utilizando o parâmetro **-Credencial.**
    * Se você é um parceiro da CSP que trabalha em nome de um inquilino, especifique o cliente como inquilino, usando o seu nome de domínio primário de inquilino ou inquilino. Por exemplo: **Connect-AzAccount -Inquilino "fabrikam.com"**

4. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se estiver a utilizar o Azure Backup pela primeira vez, tem de utilizar o **[cmdlet Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** para registar o fornecedor do Serviço de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Pode verificar se os Fornecedores se registaram com sucesso, utilizando os seguintes comandos:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Na saída de comando, o **Estado de Registo** deverá alterar-se para **Registrado**. Caso contrário, basta executar novamente o **[cmdlet Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)**

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Os passos seguintes levam-no através da criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de reserva.

1. O cofre dos Serviços de Recuperação é um recurso do Gestor de Recursos, pelo que é necessário colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **[New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)** Ao criar um grupo de recursos, especifique o nome e a localização do grupo de recursos.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Utilize o [cmdlet New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre dos Serviços de Recuperação. Certifique-se de especificar o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância de armazenamento a utilizar. Pode utilizar [armazenamento localmente redundante (LRS),](../storage/common/storage-redundancy.md#locally-redundant-storage) [armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)ou [armazenamento redundante de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). O exemplo a seguir mostra a opção **-BackupStorageRedundancy** para *testvault* definido para **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por esta razão, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação de Backup numa variável.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Para visualizar todos os cofres da subscrição, utilize [o Get-AzRecoveryServicesVault:](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante ao exemplo seguinte, note que o Nome e Localização do Grupo de Recursos associados são fornecidos.

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

Utilize um cofre dos Serviços de Recuperação para proteger as suas máquinas virtuais. Antes de aplicar a proteção, desa um pouco o contexto do cofre (o tipo de dados protegidos no cofre) e verifique a política de proteção. A política de proteção é o horário em que os postos de trabalho de reserva funcionam, e quanto tempo cada foto de reserva é mantida.

### <a name="set-vault-context"></a>Definir contexto de abóbada

Antes de permitir a proteção num VM, utilize [o Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) para definir o contexto do cofre. Quando o contexto do cofre estiver definido, é aplicado a todos os cmdlets subsequentes. O exemplo a seguir define o contexto do cofre para o cofre, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pegue a iD do cofre

Planeamos depreciar a definição do contexto do cofre de acordo com as diretrizes da Azure PowerShell. Em vez disso, pode armazenar ou buscar a identificação do cofre e passá-la para comandos relevantes. Então, se não definiu o contexto do cofre ou quer especificar o comando para correr para um determinado cofre, passe o ID do cofre como "-vaultID" para todo o comando relevante, da seguinte forma:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Ou

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Modificação das definições de replicação de armazenamento

Utilize o comando [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) para definir a configuração de replicação de armazenamento do cofre para LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Armazenamento A redundância só pode ser modificada se não houver itens de reserva protegidos no cofre.

### <a name="create-a-protection-policy"></a>Criar uma política de proteção

Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas. A política de proteção predefinida aciona um trabalho de cópia de segurança todos os dias a uma hora especificada. A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias. Pode utilizar a política predefinida para proteger rapidamente o seu VM e editar a política mais tarde com diferentes detalhes.

Utilize **[a Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** para ver as políticas de proteção disponíveis no cofre. Você pode usar este cmdlet para obter uma política específica, ou para ver as políticas associadas a um tipo de carga de trabalho. O exemplo a seguir obtém políticas para o tipo de carga de trabalho, AzureVM.

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
> O intervalo de tempo do campo BackupTime em PowerShell é UTC. No entanto, quando o tempo de backup é mostrado no portal Azure, o tempo é ajustado para o seu tempo de tempo local.
>
>

Uma política de proteção de apoio está associada a pelo menos uma política de retenção. Uma política de retenção define quanto tempo um ponto de recuperação é mantido antes de ser eliminado.

* Utilize [o Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) para ver a política de retenção predefinida.
* Da mesma forma, pode utilizar [o Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) para obter a política de agenda padrão.
* O [cmdlet New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cria um objeto PowerShell que contém informações de política de backup.
* Os objetos de política de programação e retenção são utilizados como entradas para o New-AzRecoveryServicesBackupProtectionPolicy cmdlet.

Por predefinição, é definida uma hora de início no Objeto de Política de Agendamento. Utilize o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada também deve estar na UTC. O exemplo a seguir pressupõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Só precisa de fornecer a hora de início em múltiplos de 30 minutos. No exemplo acima, pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00"

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. O exemplo utiliza essas variáveis para definir os parâmetros ao criar uma política de proteção, *a NewPolicy.*

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

O resultado é semelhante ao seguinte exemplo:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Ativar a proteção

Uma vez definida a política de proteção, ainda deve ativar a política para um item. Utilize [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) para permitir a proteção. Permitir a proteção requer dois objetos - o item e a política. Uma vez que a política tenha sido associada ao cofre, o fluxo de trabalho de backup é acionado no momento definido no calendário de política.

> [!IMPORTANT]
> Ao utilizar o PowerShell para ativar a cópia de segurança de vários VMs de uma só vez, certifique-se de que uma única política não tem mais de 100 VMs associados. Esta é uma [boa prática recomendada.](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy) Atualmente, o cliente PowerShell não bloqueia explicitamente se houver mais de 100 VMs, mas o cheque está previsto para ser adicionado no futuro.

Os exemplos a seguir permitem a proteção do artigo V2VM, utilizando a política, a NewPolicy. Os exemplos diferem com base no facto de o VM estar encriptado e de que tipo de encriptação.

Para ativar a proteção em **VMs do Gestor de Recursos não encriptados:**

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para ativar a proteção em VMs encriptados (encriptados usando BEK e KEK), deve dar ao serviço Azure Backup permissão para ler chaves e segredos a partir do cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para ativar a proteção em **VMs encriptados (encriptados apenas com BEK)**, tem de dar permissão ao serviço de Backup Azure para ler segredos a partir do cofre da chave.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Se estiver a utilizar a nuvem do Governo Azure, use o valor `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` para o parâmetro **ServicePrincipalName** em [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet.
>

Se pretender fazer backup seletivo de alguns discos e excluir outros como mencionado [nestes cenários,](selective-disk-backup-restore.md#scenarios)pode configurar a proteção e fazer backup apenas dos discos relevantes, conforme [documentado aqui](selective-disk-backup-restore.md#enable-backup-with-powershell).

## <a name="monitoring-a-backup-job"></a>Monitorização de um trabalho de reserva

Pode monitorizar operações de longa duração, como trabalhos de reserva, sem utilizar o portal Azure. Para obter o estado de um trabalho em curso, use o [cmdlet Get-AzRecoveryservicesBackupJob.](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) Este comandante consegue os trabalhos de reserva para um cofre específico, e o cofre é especificado no contexto do cofre. O exemplo seguinte obtém o estatuto de um trabalho em curso como matriz, e armazena o estatuto na variável $joblist.

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

Em vez de sondar estes postos de trabalho para a conclusão - que é um código adicional desnecessário - use o [cmdlet Wait-AzRecoveryServicesBackupJob.](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) Este cmdlet interrompe a execução até que o trabalho esteja concluído ou o valor de tempo limite especificado seja atingido.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Gerir as cópias de segurança de VMs do Azure

### <a name="modify-a-protection-policy"></a>Modificar uma política de proteção

Para modificar a política de proteção, utilize [o Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) para modificar os objetos AgendaPolicy ou RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Modificação do tempo programado

Quando se cria uma política de proteção, é-lhe atribuída uma hora de início por defeito. Os exemplos que se seguem mostram como modificar a hora de início de uma política de proteção.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Modificação da retenção

O exemplo a seguir altera a retenção do ponto de recuperação para 365 dias.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Configurar retenção instantânea de restauro instantâneo

> [!NOTE]
> A partir da versão 1.6.0 da Azure PowerShell, pode-se atualizar o período de retenção instantânea de retenção instantânea na política utilizando o PowerShell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

O valor predefinido será 2. Pode definir o valor com um mínimo de 1 e máximo de 5. Para as políticas de backup semanal, o período está definido para 5 e não pode ser alterado.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Criar grupo de recursos de backup Azure durante a retenção de instantâneos

> [!NOTE]
> A partir da versão 3.7.0 da Azure PowerShell, pode-se criar e editar o grupo de recursos criado para armazenar instantâneos instantâneos.

Para obter mais informações sobre as regras de criação de grupos de recursos e outros detalhes relevantes, consulte o [grupo de recursos Azure Backup para](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) documentação de Máquinas Virtuais.

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>Excluir discos para um VM protegido

O backup Azure VM fornece uma capacidade de excluir seletivamente ou incluir discos que são úteis [nestes cenários](selective-disk-backup-restore.md#scenarios). Se a máquina virtual já estiver protegida pela cópia de segurança Azure VM e se todos os discos estiverem apoiados, então pode modificar a proteção para incluir ou excluir discos seletivamente, como mencionado [aqui](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell).

### <a name="trigger-a-backup"></a>Desencadeie uma cópia de segurança

Utilize [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) para ativar um trabalho de backup. Se for o reforço inicial, é um reforço completo. As cópias de segurança subsequentes levam uma cópia incremental. O exemplo a seguir requer que uma cópia de segurança VM seja mantida durante 60 dias.

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
> O intervalo de tempo dos campos StartTime e EndTime em PowerShell é UTC. No entanto, quando a hora é mostrada no portal Azure, o tempo é ajustado ao seu tempo de tempo local.
>
>

### <a name="change-policy-for-backup-items"></a>Alterar política para artigos de backup

Pode modificar a política existente ou alterar a política do item apoiado da Política1 para a Policy2. Para mudar as políticas para um item de reserva, pegue na política relevante e faça backup do item e utilize o comando [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) com o item de backup como parâmetro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

O comando aguarda até que a cópia de segurança de configuração esteja concluída e retorne a seguinte saída.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Parar proteção

#### <a name="retain-data"></a>Manter dados

Se desejar parar a proteção, pode utilizar o [cmdlet PowerShell de Disable-AzRecoveryServicesReupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell. Isto irá parar as cópias de segurança programadas, mas os dados até agora são mantidos para sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Para remover completamente os dados de cópia de segurança armazenados no cofre, adicione a bandeira /switch 'RemoveRecoveryPoints' ao [comando de proteção 'desactivar'.](#retain-data)

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Restaurar um VM azul

Há uma diferença importante entre restaurar um VM usando o portal Azure e restaurar um VM usando PowerShell. Com o PowerShell, a operação de restauro fica completa assim que os discos e informações de configuração do ponto de recuperação forem criados. A operação de restauro não cria a máquina virtual. Para criar uma máquina virtual a partir do disco, consulte a secção, [Crie o VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se não quiser restaurar todo o VM, mas quiser restaurar ou recuperar alguns ficheiros de uma cópia de segurança do Azure VM, consulte a [secção de recuperação](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)de ficheiros .

> [!Tip]
> A operação de restauro não cria a máquina virtual.
>
>

O gráfico que se segue mostra a hierarquia do objeto desde o RecoveryServicesVault até ao BackupRecoveryPoint.

![Hierarquia de objetos de serviços de recuperação mostrando BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar os dados de backup, identifique o item de backup e o ponto de recuperação que contém os dados pontuais. Utilize [o Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar os dados do cofre para a sua conta.

Os passos básicos para restaurar um Azure VM são:

* Selecione a VM.
* Escolha um ponto de recuperação.
* Restaurar os discos.
* Crie o VM a partir de discos armazenados.

### <a name="select-the-vm-when-restoring-files"></a>Selecione o VM (ao restaurar ficheiros)

Para obter o objeto PowerShell que identifica o item de reserva certo, comece pelo recipiente no cofre e desça pela hierarquia do objeto. Para selecionar o recipiente que representa o VM, utilize o cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e tubo que vai para o [cmdlet Get-AzRecoveryServicesBackupItem.](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Escolha um ponto de recuperação (ao restaurar ficheiros)

Utilize a [cmdlet Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação do item de backup. Em seguida, escolha o ponto de recuperação para restaurar. Se não tem a certeza de qual ponto de recuperação utilizar, é uma boa prática escolher o mais recente RecoveryPointType = Ponto De AppConsistente na lista.

No seguinte script, a variável, **$rp,** é um conjunto de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz é ordenada em ordem inversa do tempo com o último ponto de recuperação no índice 0. Utilize o indexante padrão da matriz PowerShell para escolher o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

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

Utilize o [cmdlet Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar os dados e configuração de um item de cópia de segurança para um ponto de recuperação. Assim que identificar um ponto de recuperação, use-o como valor para o parâmetro **-RecoveryPoint.** Na amostra acima referida, **$rp[0]** foi o ponto de recuperação a utilizar. No seguinte código de amostra, **$rp[0]** é o ponto de recuperação a utilizar para restaurar o disco.

Para restaurar os discos e informações de configuração:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Restaurar discos geridos

> [!NOTE]
> Se o VM apoiado tiver gerido discos e quiser restaurá-los como discos geridos, introduzimos a capacidade do módulo RM Azure PowerShell v 6.7.0. para a frente.
>
>

Fornecer um parâmetro adicional **TargetResourceGroupName** para especificar o RG para o qual os discos geridos serão restaurados.

> [!IMPORTANT]
> É fortemente recomendado usar o parâmetro **TargetResourceGroupName** para restaurar discos geridos, uma vez que resulta em melhorias significativas no desempenho. Se este parâmetro não for dado, então não poderá beneficiar da funcionalidade de restauro instantâneo e a operação de restauro será mais lenta em comparação. Se o objetivo é restaurar os discos geridos como discos não geridos, então não forneça este parâmetro e torne a intenção clara fornecendo o `-RestoreAsUnmanagedDisks` parâmetro. O `-RestoreAsUnmanagedDisks` parâmetro está disponível a partir de Azure PowerShell 3.7.0 em diante. Em futuras versões, será obrigatório fornecer qualquer um destes parâmetros para a experiência de restauro certa.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

O **ficheiro ONVMConfig.JS** será restaurado na conta de armazenamento e os discos geridos serão restaurados para o RG-alvo especificado.

O resultado é semelhante ao seguinte exemplo:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Utilize o [cmdlet Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) para aguardar a conclusão do trabalho de Restauro.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Uma vez concluída a função Restaurar, utilize o [cmdlet Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) para obter os detalhes da operação de restauro. A propriedade JobDetails tem a informação necessária para reconstruir o VM.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>Restaurar discos seletivos

Um utilizador pode restaurar seletivamente alguns discos em vez de todo o conjunto de back up. Forneça o disco LUNs necessário como parâmetro para apenas restaurá-los em vez de todo o conjunto, conforme documentado [aqui](selective-disk-backup-restore.md#restore-selective-disks-with-powershell).

> [!IMPORTANT]
> É preciso fazer uma redimensionação seletiva dos discos para restaurar seletivamente os discos. Mais detalhes são fornecidos [aqui.](selective-disk-backup-restore.md#selective-disk-restore)

Assim que restaurar os discos, vá à secção seguinte para criar o VM.

## <a name="replace-disks-in-azure-vm"></a>Substitua os discos em Azure VM

Para substituir os discos e as informações de configuração, execute os seguintes passos:

* Passo 1: [Restaurar os discos](backup-azure-vms-automation.md#restore-the-disks)
* Passo 2: [Desprender o disco de dados utilizando o PowerShell](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* Passo 3: [Anexar o disco de dados ao Windows VM com o PowerShell](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Criar um VM a partir de discos restaurados

Depois de restaurar os discos, utilize os seguintes passos para criar e configurar a máquina virtual a partir do disco.

> [!NOTE]
>
> 1. É necessário o módulo AzureAz 3.0.0 ou superior. <br>
> 2. Para criar VMs encriptados a partir de discos restaurados, a sua função Azure deve ter permissão para executar a ação, **Microsoft.KeyVault/vaults/deploy/action**. Se o seu papel não tiver esta permissão, crie um papel personalizado com esta ação. Para mais informações, consulte [as funções personalizadas Azure](../role-based-access-control/custom-roles.md). <br>
> 3. Depois de restaurar os discos, pode agora obter um modelo de implementação que pode utilizar diretamente para criar um novo VM. Não precisa de diferentes cmdlets PowerShell para criar VMs geridos/não geridos que são encriptados/não encriptados.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Criar um VM usando o modelo de implementação

Os detalhes do trabalho resultantes dão ao modelo URI que pode ser consultado e implementado.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

O modelo não é diretamente acessível, uma vez que está sob a conta de armazenamento de um cliente e o recipiente dado. Precisamos do URL completo (juntamente com um token SAS temporário) para aceder a este modelo.

1. Primeiro extrair o nome do modelo do modeloBlobURI. O formato é mencionado abaixo. Pode utilizar a operação dividida no PowerShell para extrair o nome final do modelo deste URL.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Em seguida, o URL completo pode ser gerado como explicado [aqui.](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment)

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. Implemente o modelo para criar um novo VM, como explicado [aqui.](../azure-resource-manager/templates/deploy-powershell.md)

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
    ```

### <a name="create-a-vm-using-the-config-file"></a>Criar um VM utilizando o ficheiro config

A secção seguinte lista as etapas necessárias para criar um VM utilizando o ficheiro "VMConfig".

> [!NOTE]
> É altamente recomendado usar o modelo de implementação detalhado acima para criar um VM. Esta secção (Pontos 1-6) será depreciada em breve.

1. Consultar as propriedades restauradas do disco para os detalhes do trabalho.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Desconfie do contexto de armazenamento Azure e restaure o ficheiro de configuração JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Utilize o ficheiro de configuração JSON para criar a configuração VM.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Anexe o disco de so e os discos de dados. Este passo fornece exemplos para várias configurações de VM geridas e encriptadas. Use o exemplo que se adequa à sua configuração VM.

    * **VMs não geridos e não encriptados** - Utilize a seguinte amostra para VMs não geridos e não encriptados.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs não geridos e encriptados com Azure AD (apenas BEK)** - Para VMs não geridos e encriptados com Azure AD (encriptados apenas usando BEK), você precisa restaurar o segredo para o cofre de chaves antes de poder anexar discos. Para obter mais informações, consulte a [Máquina virtual De Restauro a partir de um ponto de recuperação de Backup Azure](backup-azure-restore-key-secret.md). A amostra que se segue mostra como anexar os discos de SO e de dados para VMs encriptados. Ao configurar o disco OS, certifique-se de mencionar o tipo de SO relevante.

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

    * **VMs não geridos e encriptados com Azure AD (BEK e KEK)** - Para VMs não geridos e encriptados com AZure AD (encriptado usando BEK e KEK), restaurar a chave e o segredo para o cofre antes de anexar os discos. Para obter mais informações, consulte [Restaurar uma máquina virtual encriptada a partir de um ponto de recuperação de Backup Azure](backup-azure-restore-key-secret.md). A amostra que se segue mostra como anexar os discos de SO e de dados para VMs encriptados.

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

    * **VMs não geridos e encriptados sem Azure AD (apenas BEK)** - Para VMs não geridos e encriptados sem Azure AD (encriptados apenas com BEK), se **a chave-fonteVault/secret não estiver disponível** restaurar os segredos para o cofre de chaves usando o procedimento em [Restaurar uma máquina virtual não encriptada a partir de um ponto de recuperação de Backup Azure](backup-azure-restore-key-secret.md). Em seguida, execute os seguintes scripts para definir detalhes de encriptação na bolha de SO restaurada (este passo não é necessário para uma bolha de dados). O $dekurl pode ser recolhido a partir da chave restauradaVault.

    O seguinte script só precisa de ser executado quando a chave de origem/segredo não estiver disponível.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Depois de os **segredos estarem disponíveis** e os detalhes de encriptação também estiverem definidos na Blob OS, anexe os discos utilizando o script abaixo.

    Se a chave de origem/segredos já estiverem disponíveis, então o script acima não precisa de ser executado.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs não geridos e encriptados sem Ad AD (BEK e KEK)** - Para VMs não geridos e encriptados sem AD AZure (encriptado usando BEK & KEK), se **o keyVault/chave/segredo não estiver disponível** restaurar a chave e os segredos para o cofre chave usando o procedimento em [Restaurar uma máquina virtual não encriptada a partir de um ponto de recuperação de Backup Azure](backup-azure-restore-key-secret.md). Em seguida, execute os seguintes scripts para definir detalhes de encriptação na bolha de SO restaurada (este passo não é necessário para uma bolha de dados). O $dekurl e $kekurl podem ser recolhidos a partir da chave restauradaVault.

    O script abaixo só precisa de ser executado quando a chave de origem/chave/segredo não estiver disponível.

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

    Depois de a **chave/segredos estarem disponíveis** e os detalhes de encriptação forem definidos na Blob OS, anexe os discos utilizando o script abaixo.

    Se a chave de origem/chave/segredos estiver disponível, então o script acima não precisa de ser executado.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs geridos e não encriptados** - Para VMs geridos não encriptados, prenda os discos geridos restaurados. Para obter informações aprofundadas, consulte anexar um disco de [dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs geridos e encriptados com Azure AD (apenas BEK)** - Para VMs encriptados geridos com AD AD (encriptados apenas usando BEK), anexe os discos geridos restaurados. Para obter informações aprofundadas, consulte anexar um disco de [dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs geridos e encriptados com Azure AD (BEK e KEK)** - Para VMs encriptados geridos com AD AD (encriptados usando BEK e KEK), prenda os discos geridos restaurados. Para obter informações aprofundadas, consulte anexar um disco de [dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs geridos e encriptados sem Azure AD (apenas BEK)** - Para VMs geridos e encriptados sem AD AZure (encriptado apenas usando BEK), se **a chave de origem/segredo não estiver disponível** restaurar os segredos para o cofre de chaves usando o procedimento em [Restaurar uma máquina virtual não encriptada a partir de um ponto de recuperação de Backup Azure](backup-azure-restore-key-secret.md). Em seguida, execute os seguintes scripts para definir detalhes de encriptação no disco de SISTEMA restaurado (este passo não é necessário para um disco de dados). O $dekurl pode ser recolhido a partir da chave restauradaVault.

    O script abaixo só precisa de ser executado quando a chave de origem/segredo não estiver disponível.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Depois de os segredos estarem disponíveis e os detalhes de encriptação forem definidos no disco OS, para anexar os discos geridos restaurados, consulte [anexar um disco de dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs geridos e encriptados sem Azure AD (BEK e KEK)** - Para VMs geridos e encriptados sem AD AD (encriptado usando BEK & KEK), se **a chave-chave/chave/segredo não estiver disponível** restaurar a chave e os segredos para o cofre chave usando o procedimento em [Restaurar uma máquina virtual não encriptada a partir de um ponto de reserva de recuperação Azure](backup-azure-restore-key-secret.md). Em seguida, execute os seguintes scripts para definir detalhes de encriptação no disco de SISTEMA restaurado (este passo não é necessário para discos de dados). O $dekurl e $kekurl podem ser recolhidos a partir da chave restauradaVault.

    O seguinte script só precisa de ser executado quando a chave de origem/chave/segredo não estiver disponível.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Depois de a chave/segredos estarem disponíveis e os detalhes de encriptação forem definidos no disco OS, para anexar os discos geridos restaurados, ver [Anexar um disco de dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Defina as definições de Rede.

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

7. Empurre a extensão ADE.
   Se as extensões ADE não forem empurradas, os discos de dados serão marcados como não encriptados, pelo que é obrigatório que os passos abaixo sejam executados:

   * **Para VM com Azure AD** - Utilize o seguinte comando para ativar manualmente a encriptação para os discos de dados  

     **APENAS BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Para VM sem Azure AD** - Utilize o seguinte comando para ativar manualmente a encriptação para os discos de dados.

     Se durante a execução do comando pedir a AADClientID, então precisa atualizar o seu Azure PowerShell.

     **APENAS BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Certifique-se de eliminar manualmente os ficheiros JASON criados como parte do processo de restauro de VM encriptado.

## <a name="restore-files-from-an-azure-vm-backup"></a>Restaurar ficheiros a partir de uma cópia de segurança do Azure VM

Além de restaurar os discos, também pode restaurar ficheiros individuais a partir de uma cópia de segurança Azure VM. A funcionalidade de restaurar ficheiros fornece acesso a todos os ficheiros num ponto de recuperação. Gerencie os ficheiros através do File Explorer como faria para ficheiros normais.

Os passos básicos para restaurar um ficheiro de uma cópia de segurança Azure VM são:

* Selecione o VM
* Escolha um ponto de recuperação
* Monte os discos do ponto de recuperação
* Copiar os ficheiros necessários
* Desmonte o disco

### <a name="select-the-vm-when-restoring-the-vm"></a>Selecione o VM (ao restaurar o VM)

Para obter o objeto PowerShell que identifica o item de reserva certo, comece pelo recipiente no cofre e desça pela hierarquia do objeto. Para selecionar o recipiente que representa o VM, utilize o cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e tubo que vai para o [cmdlet Get-AzRecoveryServicesBackupItem.](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Escolha um ponto de recuperação (ao restaurar o VM)

Utilize a [cmdlet Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação do item de backup. Em seguida, escolha o ponto de recuperação para restaurar. Se não tem a certeza de qual ponto de recuperação utilizar, é uma boa prática escolher o mais recente RecoveryPointType = Ponto De AppConsistente na lista.

No seguinte script, a variável, **$rp,** é um conjunto de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz é ordenada em ordem inversa do tempo com o último ponto de recuperação no índice 0. Utilize o indexante padrão da matriz PowerShell para escolher o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

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

### <a name="mount-the-disks-of-recovery-point"></a>Monte os discos do ponto de recuperação

Utilize o [cmdlet Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) para obter o script para montar todos os discos do ponto de recuperação.

> [!NOTE]
> Os discos são montados como discos iSCSI ligados à máquina onde o script é executado. A montagem ocorre imediatamente, e não incorrem em acusações.
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

Execute o script na máquina onde pretende recuperar os ficheiros. Para executar o script, tem de introduzir a palavra-passe fornecida. Depois de os discos serem ligados, utilize o Explorador de Ficheiros do Windows para navegar nos novos volumes e ficheiros. Para obter mais informações, consulte o artigo de Cópia de Segurança, [Recupere os ficheiros da cópia de segurança da máquina virtual Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Desmonte os discos

Depois de copiar os ficheiros [necessários, utilize Disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) para desmontar os discos. Certifique-se de desmontar os discos para que o acesso aos ficheiros do ponto de recuperação seja removido.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Próximos passos

Se preferir utilizar o PowerShell para se envolver com os seus recursos Azure, consulte o artigo PowerShell, [Implementar e Gerir a cópia de segurança para o Servidor do Windows](backup-client-automation.md). Se gerir backups DPM, consulte o artigo, [Implementar e Gerir a Cópia de Segurança para DPM](backup-dpm-automation.md).
