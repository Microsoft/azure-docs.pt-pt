---
title: Back up e recuperar VMs Azure com PowerShell
description: Descreve como fazer backup e recuperar VMs Azure usando backup Azure com PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 733a06a84aa170f1361ea74d126ec9752586fce2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247985"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Back up e restaurar VMs Azure com PowerShell

Este artigo explica como fazer backup e restaurar um VM Azure num cofre dos Serviços de Recuperação de [Backup Azure](backup-overview.md) usando cmdlets PowerShell.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Crie um cofre de Serviços de Recuperação e detetete o contexto do cofre.
> * Definir uma política de cópia de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Desencadeie um trabalho de backup a pedido para as máquinas virtuais protegidas Antes de poder fazer backup (ou proteger) uma máquina virtual, deve completar os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o seu ambiente para proteger os seus VMs.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos Serviços de Recuperação.
* [Reveja](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura para backup Azure VM, [conheça](backup-azure-vms-introduction.md) o processo de backup e [reveja](backup-support-matrix-iaas.md) o suporte, limitações e pré-requisitos.
* Reveja a hierarquia de objetos PowerShell para Serviços de Recuperação.

## <a name="recovery-services-object-hierarchy"></a>Serviços de Recuperação objetam hierarquia

A hierarquia do objeto é resumida no diagrama seguinte.

![Serviços de Recuperação objetam hierarquia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Consulte a referência de referência **Az.RecoveryServices** [cmdlet](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) na biblioteca Azure.

## <a name="set-up-and-register"></a>Configurar e registar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar:

1. [Descarregue a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Encontre os cmdlets PowerShell de backup Azure disponíveis digitando o seguinte comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Aparecem os pseudónimos e cmdlets para o Azure Backup, a Recuperação do Local Azure e o cofre dos Serviços de Recuperação. A imagem que se segue é um exemplo do que verá. Não é a lista completa de cmdlets.

    ![lista de Serviços de Recuperação](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Inscreva-se na sua conta Azure utilizando o **Connect-AzAccount**. Este cmdlet traz uma página web que lhe pede credenciais de conta:

    * Alternadamente, pode incluir as credenciais da sua conta como parâmetro no cmdlet **Connect-AzAccount,** utilizando o parâmetro **-Credencial.**
    * Se for sócio da CSP a trabalhar em nome de um inquilino, especifique o cliente como inquilino, utilizando o seu nome de domínio primário de inquilino ou inquilino. Por exemplo: **Connect-AzAccount -Inquilino "fabrikam.com"**

4. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se estiver a utilizar o Azure Backup pela primeira vez, deve utilizar o **[cmdlet Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** para registar o prestador do Serviço de Recuperação Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Pode verificar se os Fornecedores se registaram com sucesso, utilizando os seguintes comandos:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Na saída de comando, o **Estado de Registo** deve alterar-se para **Registro**. Caso contrário, basta executar o **[cmdlet Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** novamente.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Os seguintes passos levam-no através da criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de reserva.

1. O cofre dos Serviços de Recuperação é um recurso do Gestor de Recursos, por isso é necessário colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente, ou criar um grupo de recursos com o cmdlet **[New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** Ao criar um grupo de recursos, especifique o nome e a localização do grupo de recursos.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Utilize o [cmdlet New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) para criar o cofre dos Serviços de Recuperação. Certifique-se de especificar a mesma localização para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especificar o tipo de redundância de armazenamento a utilizar; pode utilizar [armazenamento redundante localmente (LRS)](../storage/common/storage-redundancy-lrs.md) ou [Armazenamento Geo Redundante (GRS)](../storage/common/storage-redundancy-grs.md). O exemplo seguinte mostra que a opção -BackupStorageRedundancy para testvault está definida para GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Veja os cofres em uma subscrição

Para ver todos os cofres da subscrição, utilize [o Get-AzRecoveryServicesVault:](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante ao seguinte exemplo, note que o Nome e Localização do Grupo de Recursos associados são fornecidos.

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

Utilize um cofre dos Serviços de Recuperação para proteger as suas máquinas virtuais. Antes de aplicar a proteção, detete o contexto do cofre (o tipo de dados protegidos no cofre) e verifique a política de proteção. A política de proteção é o calendário quando os trabalhos de backup funcionam, e quanto tempo cada foto de backup é mantida.

### <a name="set-vault-context"></a>Definir o contexto do cofre

Antes de permitir a proteção num VM, utilize o [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) para definir o contexto do cofre. Quando o contexto do cofre estiver definido, é aplicado a todos os cmdlets subsequentes. O exemplo que se segue define o contexto do cofre para o cofre, o cofre de *teste.*

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Pegue a identificação do cofre

Planeamos depreciar a definição do contexto do cofre de acordo com as diretrizes da Azure PowerShell. Em vez disso, pode armazenar ou obter a identificação do cofre, e passá-la para comandos relevantes. Então, se não definiu o contexto do cofre ou quer especificar o comando para correr para um determinado cofre, passe o ID do cofre como "abóbada" para todos os comandos relevantes, da seguinte forma:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Ou

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Modificação das definições de replicação de armazenamento

Utilize o comando [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) para definir a configuração de replicação de armazenamento do cofre para LRS/GRS

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Armazenamento A redundância só pode ser modificada se não houver itens de reserva protegidos para o cofre.

### <a name="create-a-protection-policy"></a>Criar uma política de proteção

Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas. A política de proteção predefinida aciona um trabalho de cópia de segurança todos os dias a uma hora especificada. A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias. Pode utilizar a política de predefinição para proteger rapidamente o seu VM e editar a política mais tarde com diferentes detalhes.

Utilize a Política de Proteção de **[BackupS Get-AzRecoveryServicesBackup](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** para ver as políticas de proteção disponíveis no cofre. Pode utilizar este cmdlet para obter uma política específica, ou para ver as políticas associadas a um tipo de carga de trabalho. O exemplo seguinte obtém políticas para o tipo de carga de trabalho, AzureVM.

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
> O fuso horário do campo BackupTime em PowerShell é UTC. No entanto, quando o tempo de backup é mostrado no portal Azure, o tempo é ajustado ao seu fuso horário local.
>
>

Uma política de proteção de apoio está associada a pelo menos uma política de retenção. Uma política de retenção define quanto tempo um ponto de recuperação é mantido antes de ser eliminado.

* Utilize [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) para visualizar a política de retenção por defeito.
* Da mesma forma, pode utilizar [o Get-AzRecoveryServicesBackupAgendaPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) para obter a política de horário sinuosa.
* O [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cmdlet cria um objeto PowerShell que detém informações sobre política de backup.
* Os objetos de política de agenda e retenção são usados como inputs para o cmdlet New-AzRecoveryServicesBackupProtectionPolicy.

Por predefinição, um tempo de início é definido no Objeto de Política de Agenda. Utilize o seguinte exemplo para alterar o tempo de início para a hora de início desejada. A hora de início desejada também deve ser na UTC. O exemplo abaixo pressupõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" -VaultId $targetVault.ID
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Precisa fornecer a hora de início em apenas 30 minutos múltiplos. No exemplo acima, só pode ser "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00"

O exemplo seguinte armazena a política de horários e a política de retenção em variáveis. O exemplo utiliza essas variáveis para definir os parâmetros na criação de uma política de proteção, *a NewPolicy*.

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

Depois de definir a política de proteção, ainda deve ativar a política de um item. Utilize [o Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) para permitir a proteção. A proteção de habilitação requer dois objetos - o item e a política. Uma vez que a política tenha sido associada ao cofre, o fluxo de trabalho de reserva é desencadeado no momento definido no calendário de apólices.

> [!IMPORTANT]
> Ao utilizar o PS para permitir a cópia de segurança de vários VMs ao mesmo tempo, certifique-se de que uma única política não tem mais de 100 VMs associados a ela. Esta é uma [melhor prática recomendada.](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy) Atualmente, o cliente do PS não bloqueia explicitamente se existem mais de 100 VMs, mas o cheque está previsto para ser adicionado no futuro.

Os seguintes exemplos permitem a proteção do item, V2VM, utilizando a política NewPolicy. Os exemplos diferem com base no facto de o VM estar encriptado e de que tipo de encriptação.

Para permitir a proteção em **VMs gestor de recursos não encriptados:**

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para permitir a proteção em VMs encriptados (encriptados utilizando BEK e KEK), deve dar ao serviço de backup Azure permissão para ler chaves e segredos do cofre da chave.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para permitir a proteção em **VMs encriptados (encriptados apenas utilizando BEK)** , deve dar ao serviço de backup Azure permissão para ler segredos do cofre da chave.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Se estiver a utilizar a nuvem do Governo Azure, utilize o valor ff281ffe-705c-4f53-9f37-a40e6f2c68f3 para o parâmetro ServicePrincipalName em [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet.
>

## <a name="monitoring-a-backup-job"></a>Monitorização de um trabalho de reserva

Pode monitorizar operações de longo prazo, como trabalhos de backup, sem utilizar o portal Azure. Para obter o estatuto de um trabalho em curso, use o [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet. Este cmdlet recebe os trabalhos de reserva para um cofre específico, e o cofre é especificado no contexto do cofre. O exemplo seguinte obtém o estatuto de um trabalho em curso como uma matriz, e armazena o estado na variável $joblist.

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

Em vez de votar estes postos de trabalho para conclusão - que é um código adicional desnecessário - utilize o cmdlet [Wait-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) Este cmdlet interrompe a execução até que o trabalho esteja concluído ou o valor limite especificado seja atingido.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Gerir as cópias de segurança de VMs do Azure

### <a name="modify-a-protection-policy"></a>Modificar uma política de proteção

Para modificar a política de proteção, utilize a [Definição-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) para modificar os objetos SchedulePolicy ou RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Modificação da hora programada

Quando se cria uma política de proteção, é-lhe atribuído um início de tempo por defeito. Os exemplos que se seguem mostram como modificar o tempo de início de uma política de proteção.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Modificação da retenção

O exemplo seguinte altera a retenção do ponto de recuperação para 365 dias.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Configurar a retenção instantânea de instantâneos

> [!NOTE]
> A partir da versão 1.6.0 do Az PS, pode-se atualizar o período instantâneo de retenção instantânea na política utilizando powershell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

O valor predefinido será 2, o utilizador pode definir o valor com um min de 1 e máx. Para as políticas semanais de backup, o período está definido para 5 e não pode ser alterado.

### <a name="trigger-a-backup"></a>Desencadear uma cópia de segurança

Utilize [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) para desencadear um trabalho de backup. Se for o reforço inicial, é um reforço completo. As cópias posteriores têm uma cópia incremental. O exemplo seguinte requer um backup VM para ser retido por 60 dias.

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
> O fuso horário dos campos StartTime e EndTime na PowerShell é UTC. No entanto, quando a hora é mostrada no portal Azure, o tempo é ajustado ao seu fuso horário local.
>
>

### <a name="change-policy-for-backup-items"></a>Alterar a política de itens de backup

O utilizador pode modificar a política existente ou alterar a política do item back-up da Policy1 para a Policy2. Para mudar as políticas para um item de reserva, pegue a política relevante e faça backup e utilize o comando [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) com o item de backup como parâmetro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

O comando aguarda até que a cópia de segurança configurada esteja concluída e desemque a seguinte saída.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Parar a proteção

#### <a name="retain-data"></a>Reter dados

Se o utilizador quiser parar a proteção, pode utilizar o cmdlet [DESactivação-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS. Isto irá parar as cópias de segurança programadas, mas os dados apoiados até agora são mantidos para sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Para remover completamente os dados de backup armazenados no cofre, basta adicionar a bandeira/interruptor 'RemoveRecoveryPoints' ao comando de [proteção 'desactivar'.](#retain-data)

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Restaurar um VM Azure

Existe uma diferença importante entre restaurar um VM usando o portal Azure e restaurar um VM usando powerShell. Com o PowerShell, a operação de restauro é completa assim que os discos e as informações de configuração do ponto de recuperação forem criadas. A operação de restauro não cria a máquina virtual. Para criar uma máquina virtual a partir do disco, consulte a secção, [Crie o VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se não quiser restaurar todo o VM, mas quer restaurar ou recuperar alguns ficheiros de uma cópia de segurança Azure VM, consulte a secção de recuperação de [ficheiros](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A operação de restauro não cria a máquina virtual.
>
>

O gráfico seguinte mostra a hierarquia do objeto desde o RecoveryServicesVault até ao BackupRecoveryPoint.

![Serviços de recuperação objetam hierarquia mostrando BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar os dados de backup, identifique o item de reserva e o ponto de recuperação que detém os dados ponto-a-tempo. Utilize [restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar os dados do cofre para a sua conta.

Os passos básicos para restaurar um VM Azure são:

* Selecione a VM.
* Escolha um ponto de recuperação.
* Restaurar os discos.
* Crie o VM a partir de discos armazenados.

### <a name="select-the-vm"></a>Selecione o VM

Para obter o objeto PowerShell que identifica o item de reserva certo, comece pelo recipiente no cofre, e desça a hierarquia do objeto. Para selecionar o recipiente que representa o VM, utilize o cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e pipe-o para o cmdlet [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Escolha um ponto de recuperação

Utilize o [cmdlet Get-AzRecoveryRecoveryRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) Para listar todos os pontos de recuperação para o item de backup. Em seguida, escolha o ponto de recuperação para restaurar. Se não tiver a certeza qual o ponto de recuperação a utilizar, é uma boa prática escolher o ponto de RecoveryPointType = AppConsistent mais recente na lista.

No seguinte guião, a variável, **$rp,** é uma variedade de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz está classificada em ordem inversa com o último ponto de recuperação no índice 0. Utilize a indexação padrão da matriz PowerShell para escolher o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

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

Utilize o cmdlet [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar os dados e configuração de um item de reserva para um ponto de recuperação. Assim que identificar um ponto de recuperação, use-o como valor para o parâmetro **-RecoveryPoint.** Na amostra acima, **$rp[0]** foi o ponto de recuperação a utilizar. No seguinte código de amostra, **$rp[0]** é o ponto de recuperação a utilizar para restaurar o disco.

Para restaurar os discos e informações de configuração:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Restaurar discos geridos

> [!NOTE]
> Se o VM apoiado tiver gerido discos e quiser restaurá-los como discos geridos, introduzimos a capacidade do módulo RM Azure PowerShell v 6.7.0. para a frente
>
>

Forneça um parâmetro adicional **TargetResourceGroupName** para especificar o RG ao qual serão restaurados os discos geridos.

> [!NOTE]
> Recomenda-se vivamente utilizar o parâmetro **TargetResourceGroupName** para restaurar os discos geridos, uma vez que resulta em melhorias significativas de desempenho. Além disso, a partir do módulo Azure Powershell Az 1.0 em diante, este parâmetro é obrigatório em caso de restauro com discos geridos
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

O ficheiro **VMConfig.JSON** será restaurado na conta de armazenamento e os discos geridos serão restaurados ao alvo ESPECIFICAdo RG.

O resultado é semelhante ao seguinte exemplo:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Utilize o [cmdlet Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) para aguardar que o trabalho de Restauro esteja concluído.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Uma vez concluída a função de Restauro, utilize o [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet para obter os detalhes da operação de restauro. A propriedade JobDetails tem a informação necessária para reconstruir o VM.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Assim que restaurar os discos, vá para a secção seguinte para criar o VM.

## <a name="replace-disks-in-azure-vm"></a>Substitua discos em Azure VM

Para substituir os discos e as informações de configuração, execute os passos abaixo:

* Passo 1: [Restaurar os discos](backup-azure-vms-automation.md#restore-the-disks)
* Passo 2: [Desmontar o disco](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell) de dados utilizando o PowerShell
* Passo 3: [Fixe o disco de dados ao Windows VM com a PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>Criar um VM a partir de discos restaurados

Depois de restaurar os discos, utilize os seguintes passos para criar e configurar a máquina virtual a partir do disco.

> [!NOTE]
>
> 1. É necessário um módulo AzureAz 3.0.0 ou superior. <br>
> 2. Para criar VMs encriptados a partir de discos restaurados, a sua função Azure deve ter permissão para realizar a ação, **Microsoft.KeyVault/vaults/deploy/action**. Se o seu papel não tiver essa permissão, crie um papel personalizado com esta ação. Para mais informações, consulte [Papéis Personalizados no Azure RBAC](../role-based-access-control/custom-roles.md). <br>
> 3. Depois de restaurar os discos, pode agora obter um modelo de implementação que pode utilizar diretamente para criar um novo VM. Não há mais cmdlets PS diferentes para criar VMs geridos/não geridos que sejam encriptados/não encriptados.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Criar um VM usando o modelo de implementação

Os detalhes de trabalho resultantes dão o modelo URI que pode ser consultado e implantado.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

O modelo não é diretamente acessível, uma vez que se encontra sob a conta de armazenamento de um cliente e o recipiente dado. Precisamos do URL completo (juntamente com um token SAS temporário) para aceder a este modelo.

1. Primeiro extrai o nome do modelo do modeloBlobURI. O formato é mencionado abaixo. Pode utilizar a operação de divisão em Powershell para extrair o nome final do modelo deste URL.

```http
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

2. Em seguida, o URL completo pode ser gerado como explicado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-powershell#provide-sas-token-during-deployment).

```powershell
Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
$templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
```

3. Implemente o modelo para criar um novo VM, como explicado [aqui](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
```

### <a name="create-a-vm-using-the-config-file"></a>Criar um VM utilizando o ficheiro config

A secção seguinte enumera as etapas necessárias para criar um VM utilizando o ficheiro "VMConfig".

> [!NOTE]
> É altamente recomendado usar o modelo de implementação descrito acima para criar um VM. Esta secção (Pontos 1-6) será depreciada em breve.

1. Consultar as propriedades restauradas do disco para os detalhes do trabalho.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Delineie o contexto de armazenamento Azure e restaure o ficheiro de configuração JSON.

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

4. Fixe o disco de OS e os discos de dados. Este passo fornece exemplos para várias configurações vm geridas e encriptadas. Use o exemplo que se adequa à sua configuração VM.

* **VMs não geridos e não encriptados** - Utilize a seguinte amostra para VMs não geridos e não encriptados.

```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
```

* **VMs não geridos e encriptados com AD Azure (apenas BEK)** - Para VMs não geridos e encriptados com AD Azure (encriptado apenas usando BEK), você precisa restaurar o segredo no cofre da chave antes de poder anexar discos. Para mais informações, consulte a [Restauração de uma máquina virtual encriptada a partir de um ponto de recuperação](backup-azure-restore-key-secret.md)de Backup Azure . A amostra que se segue mostra como anexar OS e discos de dados para VMs encriptados. Ao definir o disco OS, certifique-se de mencionar o tipo de OS relevante.

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

* **VMs não geridos e encriptados com AD Azure (BEK e KEK)** - Para VMs não geridos e encriptados com AD Azure (encriptado usando BEK e KEK), restaure a chave e o segredo no cofre da chave antes de prender os discos. Para mais informações, consulte [Restaurar uma máquina virtual encriptada a partir de um ponto de recuperação](backup-azure-restore-key-secret.md)de Backup Azure . A amostra que se segue mostra como anexar OS e discos de dados para VMs encriptados.

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

* **VMs não geridos e encriptados sem AD Azure (apenas BEK)** - Para VMs não geridos e encriptados sem AD Azure (encriptado apenas usando BEK), se a chave de **origemVault/secret não estiver disponível,** restaurar os segredos do cofre chave usando o procedimento em [Restaurar uma máquina virtual não encriptada a partir de um ponto de recuperação](backup-azure-restore-key-secret.md)de backup Azure . Em seguida, execute os seguintes scripts para definir detalhes de encriptação na bolha osso restaurada (este passo não é necessário para a bolha de dados). O $dekurl pode ser recolhido a partir do cofre restaurado.

O script abaixo só precisa de ser executado quando a chave de origemVault/secret não estiver disponível.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

Depois de **os segredos estarem disponíveis** e os detalhes de encriptação também serem definidos no OS Blob, prenda os discos utilizando o script abaixo indicado.

Se a chave de origemVault/secrets já estiver disponível, então o script acima não precisa ser executado.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **VMs não geridos e encriptados sem AD Azure (BEK e KEK)** - Para VMs não geridos e encriptados sem AD Azure (encriptado usando BEK & KEK), se a chave de **origemVault/key/secret não estiver disponível** restaurar a chave e segredos para o cofre chave usando o procedimento em [Restaurar uma máquina virtual não encriptada de um ponto de recuperação](backup-azure-restore-key-secret.md)de backup Azure . Em seguida, execute os seguintes scripts para definir detalhes de encriptação na bolha osso restaurada (este passo não é necessário para a bolha de dados). A $dekurl e $kekurl podem ser recolhidas a partir do cofre de chaves restaurado.

O script abaixo só precisa de ser executado quando a chave de origemVault/key/secret não estiver disponível.

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

Depois de disponíveis os **segredos/chave** e os detalhes de encriptação no OS Blob, prenda os discos utilizando o script abaixo indicado.

Se a chave de origemVault/key/secrets estiver disponível, então o script acima não precisa de ser executado.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **VMs geridos e não encriptados** - Para VMs geridos não encriptados, prenda os discos geridos restaurados. Para obter informações aprofundadas, consulte A anexação de um disco de [dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **VMs geridos e encriptados com AD Azure (apenas BEK)** - Para VMs encriptados geridos com AD Azure (encriptado apenas usando BEK), prenda os discos geridos restaurados. Para obter informações aprofundadas, consulte A anexação de um disco de [dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **VMs geridos e encriptados com AD Azure (BEK e KEK)** - Para VMs encriptados geridos com AD Azure (encriptados usando BEK e KEK), prenda os discos geridos restaurados. Para obter informações aprofundadas, consulte A anexação de um disco de [dados a um VM do Windows utilizando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

* **VMs geridos e encriptados sem AD Azure (apenas BEK)** -Para VMs geridos e encriptados sem AD Azure (encriptado apenas usando BEK), se a chave de **origemVault/secret não estiver disponível** restaurar os segredos para o cofre chave usando o procedimento em [Restaurar uma máquina virtual não encriptada a partir de um ponto de recuperação](backup-azure-restore-key-secret.md)de Backup Azure . Em seguida, execute os seguintes scripts para definir detalhes de encriptação no disco OS restaurado (este passo não é necessário para o disco de dados). O $dekurl pode ser recolhido a partir do cofre restaurado.

O script abaixo só precisa de ser executado quando a chave de origemVault/secret não estiver disponível.  

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

Depois de os segredos estarem disponíveis e os detalhes de encriptação serem definidos no disco OS, para anexar os discos geridos restaurados, consulte a anexação de um disco de [dados a um VM do Windows utilizando](../virtual-machines/windows/attach-disk-ps.md)o PowerShell .

* **VMs geridos e encriptados sem AD Azure (BEK e KEK)** - Para VMs geridos e encriptados sem AD Azure (encriptados usando BEK & KEK), se a chave de **origemVault/key/secret não estiver disponível** restaurar a chave e segredos para o cofre chave usando o procedimento em [Restaurar uma máquina virtual não encriptada a partir de um ponto de recuperação](backup-azure-restore-key-secret.md)de backup Azure . Em seguida, execute os seguintes scripts para definir detalhes de encriptação no disco OS restaurado (este passo não é necessário para discos de dados). A $dekurl e $kekurl podem ser recolhidas a partir do cofre de chaves restaurado.

O script abaixo só precisa de ser executado quando a chave de origemVault/key/secret não estiver disponível.

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

Depois de disponíveis os segredos/chave e os detalhes de encriptação no disco OS, para fixar os discos geridos restaurados, consulte a anexação de um disco de [dados a um VM do Windows utilizando](../virtual-machines/windows/attach-disk-ps.md)o PowerShell .

5. Definir as definições da Rede.

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

   * **Para VM com AD Azure** - Utilize o seguinte comando para ativar manualmente a encriptação dos discos de dados  

     **Só BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Para VM sem AD Azure** - Utilize o seguinte comando para ativar manualmente a encriptação dos discos de dados.

     Se durante a execução do comando pedir a AADClientID, então precisa de atualizar o seu PowerShell Azure.

     **Só BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Certifique-se de eliminar manualmente os ficheiros JASON criados como parte do processo de restauração de VM encriptado.

## <a name="restore-files-from-an-azure-vm-backup"></a>Restaurar ficheiros de uma cópia de segurança Azure VM

Além de restaurar discos, também pode restaurar ficheiros individuais a partir de uma cópia de segurança Azure VM. A funcionalidade de restauro de ficheiros fornece acesso a todos os ficheiros num ponto de recuperação. Gerencie os ficheiros através do File Explorer como faria para ficheiros normais.

Os passos básicos para restaurar um ficheiro de uma cópia de segurança Azure VM são:

* Selecione o VM
* Escolha um ponto de recuperação
* Monte os discos do ponto de recuperação
* Copiar os ficheiros necessários
* Desmonte o disco

### <a name="select-the-vm"></a>Selecione o VM

Para obter o objeto PowerShell que identifica o item de reserva certo, comece pelo recipiente no cofre, e desça a hierarquia do objeto. Para selecionar o recipiente que representa o VM, utilize o cmdlet [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e pipe-o para o cmdlet [Get-AzRecoveryServicesBackupItem.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Escolha um ponto de recuperação

Utilize o [cmdlet Get-AzRecoveryRecoveryRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) Para listar todos os pontos de recuperação para o item de backup. Em seguida, escolha o ponto de recuperação para restaurar. Se não tiver a certeza qual o ponto de recuperação a utilizar, é uma boa prática escolher o ponto de RecoveryPointType = AppConsistent mais recente na lista.

No seguinte guião, a variável, **$rp,** é uma variedade de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz está classificada em ordem inversa com o último ponto de recuperação no índice 0. Utilize a indexação padrão da matriz PowerShell para escolher o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

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

Utilize o cmdlet [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) para obter o script para montar todos os discos do ponto de recuperação.

> [!NOTE]
> Os discos são montados como discos ligados iSCSI à máquina onde o script é executado. A montagem ocorre imediatamente, e você não incorre em nenhuma acusação.
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

Faça o guião na máquina onde quer recuperar os ficheiros. Para executar o script, tem de introduzir a palavra-passe fornecida. Depois de os discos serem anexados, utilize o Windows File Explorer para navegar nos novos volumes e ficheiros. Para mais informações, consulte o artigo de backup, [Recuperar ficheiros da cópia de segurança da máquina virtual Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Desmontar os discos

Depois de copiados os ficheiros necessários, utilize o [Disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) para desmontar os discos. Certifique-se de desmontar os discos para que o acesso aos ficheiros do ponto de recuperação seja removido.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Passos seguintes

Se preferir utilizar o PowerShell para se envolver com os seus recursos Azure, consulte o artigo PowerShell, [Implemente e gere a cópia de segurança para o Windows Server](backup-client-automation.md). Se gerir cópias de segurança do DPM, consulte o artigo, [implante e gere de reserva para DPM](backup-dpm-automation.md).
