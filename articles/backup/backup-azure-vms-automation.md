---
title: Implementar e gerir cópias de segurança de VMs implementadas com o Resource Manager através do PowerShell
description: Utilizar o PowerShell para implementar e gerir cópias de segurança no Azure para VMs implementadas no Resource Manager
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2cdeea546e7153c63cb1edfbc53f3644facc4f2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743906"
---
# <a name="use-powershell-to-back-up-and-restore-virtual-machines"></a>Utilizar o PowerShell para criar cópias de segurança e restaurar máquinas virtuais

Este artigo mostra como utilizar cmdlets do Azure PowerShell para criar cópias de segurança e recuperar uma máquina virtual do Azure (VM) a partir de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é um recurso do Azure Resource Manager utilizado para proteger dados e ativos nos serviços de cópia de segurança do Azure e o Azure Site Recovery.

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo é para utilização com as VMs criadas com o modelo do Resource Manager.
>
>

Este artigo orienta-o através da utilização do PowerShell para proteger uma VM e restaurar dados a partir de um ponto de recuperação.

## <a name="concepts"></a>Conceitos

Se não estiver familiarizado com o serviço de cópia de segurança do Azure, para uma descrição geral do serviço, veja o artigo, [o que é o Azure Backup?](backup-introduction-to-azure-backup.md) Antes de começar, certifique-se de que abrangem os pré-requisitos necessários com cópia de segurança do Azure e as limitações da solução de cópia de segurança de VM atual.

Para utilizar o PowerShell com eficiência, é necessário compreender a hierarquia de objetos e o local começar.

![Hierarquia de objetos de serviços de recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Para ver a referência de cmdlets do PowerShell Recoveryservices, consulte a [cópia de segurança do Azure - Cmdlets de serviços de recuperação](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) na biblioteca do Azure.

## <a name="setup-and-registration"></a>Configuração e registo

Para começar:

1. [Baixe a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (a versão mínima necessária é: 1.4.0)

2. Encontre os cmdlets do PowerShell de cópia de segurança do Azure disponíveis, escrevendo o seguinte comando:

    ```powershell
    Get-Command *azurermrecoveryservices*
    ```    
    Os cmdlets de cópia de segurança do Azure, Azure Site Recovery e o Cofre dos serviços de recuperação e aliases são apresentados. A imagem seguinte é um exemplo de como o que verá. Não é a lista completa de cmdlets.

    ![lista de serviços de recuperação](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Iniciar sessão no seu Azure conta utilizando **Connect-AzureRmAccount**. Este cmdlet é exibida uma página da web pede-lhe as credenciais da conta:

    * Em alternativa, pode incluir as credenciais da conta como um parâmetro no **Connect-AzureRmAccount** cmdlet, utilizando o **-Credential** parâmetro.
    * Se estiver a trabalhar em nome de um inquilino de parceiro CSP, especifica o cliente como um inquilino, utilizando o respetivo nome de domínio primário tenantID ou o inquilino. Por exemplo: **Connect-AzureRmAccount-inquilino "fabrikam.com"**

4. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Se estiver a utilizar o Azure Backup pela primeira vez, tem de utilizar o **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet para registar o fornecedor de serviços de recuperação do Azure com a sua subscrição.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Pode verificar que os fornecedores registado com êxito, com os comandos seguintes:
    ```powershell
    Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
    No resultado do comando, o **RegistrationState** deve ser alterado para **registado**. Se não, basta executar o **[Register-AzureRmResourceProvider](https://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** cmdlet novamente.

As seguintes tarefas podem ser automatizadas com o PowerShell:

* [Criar um cofre dos Serviços de Recuperação](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Fazer cópia de segurança de VMs do Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Acionar uma tarefa de cópia de segurança](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Monitorizar uma tarefa de cópia de segurança](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Restaurar uma VM do Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Os seguintes passos levá-lo através da criação de um cofre dos serviços de recuperação. Um cofre dos serviços de recuperação é diferente de um cofre de cópia de segurança.

1. O Cofre dos serviços de recuperação é um recurso do Resource Manager, por isso terá de colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou crie um grupo de recursos com o **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** cmdlet. Ao criar um grupo de recursos, especifique o nome e local para o grupo de recursos.  

    ```powershell
    New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Utilize o **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** cmdlet para criar o Cofre dos serviços de recuperação. Certifique-se de que especifique a mesma localização do cofre que foi utilizado para o grupo de recursos.

    ```powershell
    New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Especifique o tipo de redundância de armazenamento a utilizar. Pode usar [armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy-lrs.md) ou [armazenamento Georredundante (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo seguinte mostra que a opção - BackupStorageRedundancy para testvault está definida como GeoRedundant.

    ```powershell
    $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Para ver todos os cofres na subscrição, utilize  **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**:

```powershell
Get-AzureRmRecoveryServicesVault
```

O resultado é semelhante ao seguinte exemplo, observe a que localização e ResourceGroupName associados são fornecidos.

```
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Fazer uma cópia de segurança de VMs do Azure

Utilize um cofre dos serviços de recuperação para proteger as suas máquinas virtuais. Antes de aplicar a proteção, defina o contexto do cofre (o tipo de dados protegidos no cofre) e verifique se a política de proteção. A política de proteção é a agenda quando são executadas as tarefas de cópia de segurança e o tempo em que cada instantâneo de cópia de segurança é retido.

### <a name="set-vault-context"></a>Contexto do Cofre de conjunto

Antes de ativar a proteção numa VM, utilize **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** para definir o contexto do cofre. Quando o contexto do cofre estiver definido, é aplicado a todos os cmdlets subsequentes. O exemplo seguinte define o contexto do cofre para o Cofre *testvault*.

```powershell
Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Criar uma política de proteção

Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas. A política de proteção predefinida aciona um trabalho de cópia de segurança todos os dias a uma hora especificada. A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias. Pode utilizar a política predefinida para proteger sua VM rapidamente e editar a política mais tarde com detalhes diferentes.

Uso **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** para ver as políticas de proteção disponíveis no cofre. Pode utilizar este cmdlet para obter uma política específica ou para ver as políticas associadas um tipo de carga de trabalho. O exemplo seguinte obtém as políticas para o tipo de carga de trabalho, AzureVM.

```powershell
Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

O resultado é semelhante ao seguinte exemplo:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando a hora da cópia é apresentada no portal do Azure, o tempo é ajustado para o fuso horário local.
>
>

Uma política de proteção de cópia de segurança está associada a pelo menos uma política de retenção. Política de retenção define o tempo que um ponto de recuperação é mantido antes de ser eliminado. Uso **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** para ver a política de retenção predefinida.  Da mesma forma, pode utilizar **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** para obter a política de agenda predefinida. O **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet cria um objeto do PowerShell que contém as informações de política de cópia de segurança. Os objetos de política de agendamento e retenção são utilizados como entradas para o **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** cmdlet. O exemplo seguinte armazena a política de agendamento e a política de retenção em variáveis. O exemplo utiliza essas variáveis para definir os parâmetros ao criar uma política de proteção *NewPolicy*.

```powershell
$schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

O resultado é semelhante ao seguinte exemplo:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Ativar a proteção

Depois de definir a política de proteção, ainda tem de ativar a política para um item. Uso **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** para ativar a proteção. Ativar a proteção requer dois objetos - o item e a política. Assim que a política foi associada com o cofre, o fluxo de trabalho de cópia de segurança é acionado ao tempo definido no agendamento de política.

Os exemplos seguintes ativar a proteção para o item, V2VM, utilizando a política, NewPolicy. Os exemplos são diferentes com base em se a VM está encriptada e o que o tipo de encriptação. 

Para ativar a proteção num **VMs do Resource Manager não encriptadas**:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para ativar a proteção num **(criptografadas com BEK e KEK) de VMs encriptadas**, tem de dar a permissão de serviço de cópia de segurança do Azure para ler as chaves e segredos do Cofre de chaves.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Para ativar a proteção num **(encriptadas com a BEK apenas) de VMs encriptadas**, tem de dar a permissão de serviço de cópia de segurança do Azure para ler segredos do Cofre de chaves.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Se estiver a utilizar a cloud do Azure Government, em seguida, utilize o ff281ffe-705c-4f53-9f37-a40e6f2c68f3 de valor para o parâmetro **- ServicePrincipalName** na [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) cmdlet.
>
>

Para ativar a proteção numa VM clássica:

```powershell
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Modificar uma política de proteção

Para modificar a política de proteção, utilize [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) para modificar os objetos SchedulePolicy ou RetentionPolicy.

O exemplo seguinte altera o período de retenção do ponto de recuperação a 365 dias.

```powershell
$retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Acionar uma cópia de segurança

Uso **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** para acionar uma tarefa de cópia de segurança. Se estiver a cópia de segurança inicial, é uma cópia de segurança completa. Cópias de segurança subsequentes faça uma cópia incremental. Certifique-se de que usar **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** para definir o contexto do cofre antes de acionar a tarefa de cópia de segurança. O exemplo a seguir supõe que o contexto do cofre já foi definido.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

O resultado é semelhante ao seguinte exemplo:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> O fuso horário dos campos StartTime e EndTime no PowerShell é UTC. No entanto, quando o tempo é apresentado no portal do Azure, o tempo é ajustado para o fuso horário local.
>
>

## <a name="monitoring-a-backup-job"></a>Monitorização de uma tarefa de cópia de segurança

Pode monitorizar as operações de longa execução, como tarefas de cópia de segurança, sem utilizar o portal do Azure. Para obter o estado de uma tarefa em curso, utilize o **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** cmdlet. Este cmdlet obtém as tarefas de cópia de segurança de um cofre específico, e esse cofre é especificado no contexto do cofre. O exemplo seguinte obtém o estado de uma tarefa em curso como uma matriz e armazena o estado na variável $joblist.

```powershell
$joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

O resultado é semelhante ao seguinte exemplo:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Em vez de consultar estas tarefas de conclusão – o que é o código adicional desnecessário - utilizar o **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet. Este cmdlet coloca em pausa a execução até que a tarefa é concluída ou for atingido o valor de tempo limite especificado.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Restaurar uma VM do Azure

Há uma diferença importante entre a restaurar uma VM com o portal do Azure e restaurar uma VM com o PowerShell. Com o PowerShell, a operação de restauro está concluída, uma vez que os discos e as informações de configuração do ponto de recuperação são criados. A operação de restauro não cria a máquina virtual. Para criar uma máquina virtual a partir do disco, consulte a secção [criar a VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se não quer restaurar a VM inteira, mas deseja restaurar ou recuperar alguns arquivos de uma cópia de segurança de VM do Azure, consulte a [secção recuperação de ficheiros](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A operação de restauro não cria a máquina virtual.
>
>

O gráfico seguinte mostra a hierarquia de objetos do RecoveryServicesVault para baixo até o BackupRecoveryPoint.

![Hierarquia de objetos de serviços de recuperação que mostra BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar dados de cópia de segurança, identificar o item de cópia de segurança e o ponto de recuperação que contém os dados de ponto no tempo. Uso **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** para restaurar dados a partir do cofre para a sua conta.

Os passos básicos para restaurar uma VM do Azure são:

* Selecione a VM.
* Escolha um ponto de recuperação.
* Restaure os discos.
* Crie a VM a partir de discos armazenados.

### <a name="select-the-vm"></a>Selecione a VM

Para obter o objeto do PowerShell que identifica o item de cópia de segurança adequados, iniciar a partir do contentor no cofre e for subindo para baixo a hierarquia do objeto. Para selecionar o contentor que representa a VM, utilize o **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet e encaminhar para o **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Escolher um ponto de recuperação

Utilize o **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet para listar todos os pontos de recuperação para o item de cópia de segurança. Em seguida, escolha o ponto de recuperação para restaurar. Se tiver a certeza do ponto de recuperação a utilizar, é uma boa prática para escolher a mais recente RecoveryPointType = AppConsistent ponto na lista.

No seguinte script, a variável **$rp**, é uma matriz de pontos de recuperação para o item de cópia de segurança selecionado, dos últimos sete dias. A matriz é ordenada pela ordem inversa de tempo com o ponto de recuperação mais recente no índice 0. Utilize a indexação da matriz de PowerShell padrão para escolher o ponto de recuperação. No exemplo, $rp [0] seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

O resultado é semelhante ao seguinte exemplo:

```powershell
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

Utilize o **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** cmdlet para restaurar dados de um item de cópia de segurança e configuração para um ponto de recuperação. Depois de identificar um ponto de recuperação, utilizá-lo como o valor para o **- RecoveryPoint** parâmetro. No exemplo acima, **$rp [0]** foi o ponto de recuperação para utilizar. No seguinte código de exemplo, **$rp [0]** é o ponto de recuperação a utilizar para restaurar o disco.

Para restaurar os discos e as informações de configuração:

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Restaurar discos geridos

> [!NOTE]
> Se a cópia de VM tem discos geridos e pretende restaurá-las como discos geridos, Introduzimos o recurso do Azure Powershell v 6.7.0. e superior
>
>

Forneça um parâmetro adicional **TargetResourceGroupName** para especificar o RG ao qual vão ser restaurados discos geridos.

```powershell
$restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

O **VMConfig.JSON** ficheiro será restaurado para a conta de armazenamento e o destino especificado RG serão restaurados os discos geridos.

O resultado é semelhante ao seguinte exemplo:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Utilize o **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** cmdlet para aguardar a conclusão da tarefa de restauro.

```powershell
Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Depois de concluída a tarefa de restauro, utilize o **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** cmdlet para obter os detalhes da operação de restauro. A propriedade de JobDetails tem as informações necessárias para recriar a VM.

```powershell
$restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Depois de restaurar os discos, vá para a secção seguinte para criar a VM.

## <a name="create-a-vm-from-restored-disks"></a>Criar uma VM a partir de discos restaurados

Depois de restaurar os discos, utilize os seguintes passos para criar e configurar a máquina virtual a partir do disco.

> [!NOTE]
> Para criar VMs encriptadas a partir de discos restaurados, sua função do Azure tem de ter permissão para executar a ação **Microsoft.KeyVault/vaults/deploy/action**. Se a sua função não tiver esta permissão, crie uma função personalizada com esta ação. Para obter mais informações, consulte [funções personalizadas no Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> Depois de restaurar discos, agora pode obter um modelo de implementação que pode utilizar diretamente para criar uma nova VM. Cmdlets de PS não mais diferentes para criar VMs kombinace spravovaného a nespravovaného que são encriptados/não encriptada.

Os detalhes da tarefa resultante oferece o modelo de URI que pode ser consultada e implementado.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Basta implementar o modelo para criar uma nova VM, conforme explicado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source).

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

A secção seguinte apresenta uma lista de etapas necessárias para criar uma VM com o ficheiro de "VMConfig".

> [!NOTE]
> É altamente recomendado para utilizar o modelo de implementação detalhado acima para criar uma VM. Nesta secção (pontos de 1 a 6) vai ser preterida em breve.

1. Consulte as propriedades de disco restaurado para os detalhes da tarefa.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Definir o contexto de armazenamento do Azure e restaurar o ficheiro de configuração JSON.

   ```powershell
   Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Utilize o ficheiro de configuração do JSON para criar a configuração de VM.

   ```powershell
   $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Anexe o disco do SO e discos de dados. Este passo fornece exemplos para várias geridos e encriptados configurações de VM. Utilize o exemplo que se adapte às sua configuração de VM.

   * **VMs não geridos e não encriptadas** -utilize o exemplo seguinte para VMs não gerido, não encriptada.

       ```powershell
       Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **VMs encriptadas e não geridos (apenas BEK)** -para VMs não geridos e criptografadas (encriptadas com a BEK apenas), tem de restaurar o segredo ao Cofre de chaves, antes de pode anexar discos. Para obter mais informações, consulte o artigo [restaurar uma máquina virtual encriptada a partir de um ponto de recuperação de cópia de segurança do Azure](backup-azure-restore-key-secret.md). O exemplo a seguir mostra como anexar discos de SO e dados para VMs encriptadas. Ao definir o disco do SO, certifique-se de mencionar o tipo de SO relevante.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```
      Utilize o seguinte comando para ativar manualmente a encriptação para os discos de dados.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
      ```

   * **VMs encriptadas e não geridos (BEK e KEK)** – para VMs não geridos e criptografadas (encriptadas com BEK e KEK), restaurar a chave e segredo ao Cofre de chaves antes de anexar os discos. Para obter mais informações, consulte o artigo [restaurar uma máquina virtual encriptada a partir de um ponto de recuperação de cópia de segurança do Azure](backup-azure-restore-key-secret.md). O exemplo a seguir mostra como anexar discos de SO e dados para VMs encriptadas.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
       {
       $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
      ```

      Utilize o seguinte comando para ativar manualmente a encriptação para os discos de dados.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **VMs geridas e não encriptadas** – para VMs não encriptadas geridas, anexar os discos geridos restaurados. Para obter informações detalhadas, consulte o artigo [anexar um disco de dados a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

   * **Gerido e VMs (apenas BEK) encriptadas** – para VMs encriptadas geridas (encriptadas com BEK apenas), ligue os discos geridos restaurados. Para obter informações detalhadas, consulte o artigo [anexar um disco de dados a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

     Utilize o seguinte comando para ativar manualmente a encriptação para os discos de dados.

       ```powershell
       Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
       ```

   * **Gerido e VMs (BEK e KEK) encriptadas** – para VMs encriptadas geridas (encriptadas com BEK e KEK), ligue os discos geridos restaurados. Para obter informações detalhadas, consulte o artigo [anexar um disco de dados a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

      Utilize o seguinte comando para ativar manualmente a encriptação para os discos de dados.

      ```powershell
      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

5. Configure as definições de rede.

    ```powershell
    $nicName="p1234"
    $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzureRmVirtualNetwork
    $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Criar a máquina virtual.

    ```powershell  
    New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Restaurar ficheiros a partir de uma cópia de segurança de VM do Azure

Além de restauro dos discos, também pode restaurar ficheiros individuais a partir de uma cópia de segurança de VM do Azure. A funcionalidade de ficheiros de restauro fornece acesso a todos os ficheiros num ponto de recuperação. Gerir os ficheiros através do Explorador de ficheiros, tal como faria para ficheiros normais.

Os passos básicos para restaurar um ficheiro a partir de uma cópia de segurança de VM do Azure são:

* Selecione a VM
* Escolher um ponto de recuperação
* Montar os discos de ponto de recuperação
* Copie os ficheiros necessários
* Desmontar o disco

### <a name="select-the-vm"></a>Selecione a VM

Para obter o objeto do PowerShell que identifica o item de cópia de segurança adequados, iniciar a partir do contentor no cofre e for subindo para baixo a hierarquia do objeto. Para selecionar o contentor que representa a VM, utilize o **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** cmdlet e encaminhar para o **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** cmdlet.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Escolher um ponto de recuperação

Utilize o **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** cmdlet para listar todos os pontos de recuperação para o item de cópia de segurança. Em seguida, escolha o ponto de recuperação para restaurar. Se tiver a certeza do ponto de recuperação a utilizar, é uma boa prática para escolher a mais recente RecoveryPointType = AppConsistent ponto na lista.

No seguinte script, a variável **$rp**, é uma matriz de pontos de recuperação para o item de cópia de segurança selecionado, dos últimos sete dias. A matriz é ordenada pela ordem inversa de tempo com o ponto de recuperação mais recente no índice 0. Utilize a indexação da matriz de PowerShell padrão para escolher o ponto de recuperação. No exemplo, $rp [0] seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

O resultado é semelhante ao seguinte exemplo:

```
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

### <a name="mount-the-disks-of-recovery-point"></a>Montar os discos de ponto de recuperação

Utilize o **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** cmdlet para obter o script para montar a todos os discos do ponto de recuperação.

> [!NOTE]
> Os discos estão montados como discos ligados a iSCSI para a máquina em que o script é executado. Montagem ocorre imediatamente e não a incorrer em quaisquer encargos.
>
>

```powershell
Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

O resultado é semelhante ao seguinte exemplo:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Execute o script na máquina em que pretende recuperar os ficheiros. Para executar o script, tem de introduzir a palavra-passe fornecida. Depois dos discos estão anexados, utilize o Explorador de ficheiros do Windows para procurar os ficheiros e os novos volumes. Para obter mais informações, consulte o artigo de cópia de segurança, [recuperar ficheiros a partir de cópia de segurança da máquina virtual do Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Desmontar os discos

Depois dos ficheiros necessários são copiados, utilize **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** desmontar os discos. Certifique-se de que desmontar os discos para que o acesso aos ficheiros do ponto de recuperação é removido.

```powershell
Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>Passos Seguintes

Se preferir utilizar o PowerShell para interagir com wi th sua do Azure recursos, consulte o artigo do PowerShell [implementar e gerir cópias de segurança para o Windows Server](backup-client-automation.md). Se gerir cópias de segurança do DPM, consulte o artigo [implementar e gerir cópias de segurança para o DPM](backup-dpm-automation.md). Estes artigos têm uma versão para implementações do Resource Manager e as implementações clássicas.  
