---
title: Fazer backup e restaurar arquivos do Azure usando o backup do Azure e o PowerShell
description: Faça backup e restaure os arquivos do Azure usando o backup do Azure e o PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: dacurwin
ms.reviewer: pullabhk
ms.openlocfilehash: f933b926aa0e277976416ae1b3b2eb684d9fcc85
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955091"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Fazer backup e restaurar arquivos do Azure com o PowerShell

Este artigo descreve como usar Azure PowerShell para fazer backup e recuperar um compartilhamento de arquivos de arquivos do Azure usando um cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) . 

Este tutorial explica como:

> [!div class="checklist"]
> * Configure o PowerShell e registre o provedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure o backup para um compartilhamento de arquivos do Azure.
> * Execute um trabalho de backup.
> * Restaure um compartilhamento de arquivos do Azure de backup ou um arquivo individual de um compartilhamento.
> * Monitore os trabalhos de backup e restauração.


## <a name="before-you-start"></a>Antes de começar

- [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos serviços de recuperação.
- Leia sobre os recursos de visualização para fazer backup de compartilhamentos de [arquivos do Azure](backup-azure-files.md).
- Examine a hierarquia de objetos do PowerShell para serviços de recuperação.


## <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos serviços de recuperação

A hierarquia de objetos é resumida no diagrama a seguir.

![Hierarquia de objetos dos serviços de recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Examine a referência de [referência do cmdlet](/powershell/module/az.recoveryservices) **AZ. recoveryservices** na biblioteca do Azure.


## <a name="set-up-and-install"></a>Configurar e instalar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte maneira:

1. [Baixe a versão mais recente do AZ PowerShell](/powershell/azure/install-az-ps). A versão mínima necessária é a 1.0.0.

2. Localize os cmdlets do PowerShell de backup do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Examine os aliases e cmdlets para o backup do Azure, Azure Site Recovery e o cofre dos serviços de recuperação são exibidos. Aqui está um exemplo do que você pode ver. Não é uma lista completa de cmdlets.

    ![Lista de cmdlets dos serviços de recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Entre em sua conta do Azure com **Connect-AzAccount**.
4. Na página da Web que aparece, você será solicitado a inserir suas credenciais de conta.

    - Como alternativa, você pode incluir suas credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount** com **-Credential**.
    - Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Um exemplo é **Connect-AzAccount-Tenant** fabrikam.com.

4. Associe a assinatura que você deseja usar com a conta, pois uma conta pode ter várias assinaturas.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se você estiver usando o backup do Azure pela primeira vez, use o cmdlet **Register-AzResourceProvider** para registrar o provedor de serviços de recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Verifique se os provedores foram registrados com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. Na saída do comando, verifique se **RegistrationState** muda para **registrado**. Caso contrário, execute o cmdlet **Register-AzResourceProvider** novamente.



## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Siga estas etapas para criar um cofre dos serviços de recuperação.

- O cofre dos serviços de recuperação é um recurso do Resource Manager, portanto, você deve colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou pode criar um grupo de recursos com o cmdlet **New-AzResourceGroup** . Ao criar um grupo de recursos, especifique o nome e o local do grupo de recursos. 

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um grupo de recursos existente, crie um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, criamos um novo grupo de recursos na região oeste dos EUA.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Use o cmdlet [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) para criar o cofre. Especifique o mesmo local para o cofre que foi usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Especifique o tipo de redundância a ser usado para o armazenamento do cofre.

   - Você pode usar o [armazenamento com redundância local](../storage/common/storage-redundancy-lrs.md) ou o [armazenamento com redundância geográfica](../storage/common/storage-redundancy-grs.md).
   - O exemplo a seguir define a opção **-BackupStorageRedundancy** para o cmd[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) para **testvault** definidocomo georedundante.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres na assinatura, use [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. Observe que o grupo de recursos e o local associados são fornecidos.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do cofre

Armazene o objeto de cofre em uma variável e defina o contexto do cofre.

- Muitos cmdlets de backup do Azure exigem o objeto de cofre dos serviços de recuperação como uma entrada, portanto, é conveniente armazenar o objeto de cofre em uma variável.
- O contexto do cofre é o tipo de dados protegidos no cofre. Defina-o com [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes.


O exemplo a seguir define o contexto do cofre para **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Buscar a ID do cofre

Planejamos substituir a configuração de contexto do cofre de acordo com as diretrizes de Azure PowerShell. Em vez disso, você pode armazenar ou buscar a ID do cofre e passá-la para comandos relevantes, da seguinte maneira:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o agendamento de backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos:

- Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido antes de ser excluído.
- Exiba a retenção de política de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Exiba o agendamento da política de backup padrão usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Use o cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) para criar uma nova política de backup. Você insere os objetos de política de retenção e agendamento.

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, ele usa essas variáveis como parâmetros para uma nova política (**NewAFSPolicy**). O **NewAFSPolicy** usa um backup diário e o retém por 30 dias.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Habilitar backup

Depois de definir a política de backup, você pode habilitar a proteção para o compartilhamento de arquivos do Azure usando a política.

### <a name="retrieve-a-backup-policy"></a>Recuperar uma política de backup

Você busca o objeto de política relevante com [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Use este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperar uma política para um tipo de carga de trabalho

O exemplo a seguir recupera políticas para o tipo de carga de trabalho **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A saída é semelhante à seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> O fuso horário do campo **backuptime** no PowerShell é UTC (horário coordenado universal). Quando o horário do backup é mostrado na portal do Azure, a hora é ajustada para o fuso horário local.

### <a name="retrieve-a-specific-policy"></a>Recuperar uma política específica

A política a seguir recupera a política de backup chamada **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Habilitar backup e aplicar política

Habilite a proteção com [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Depois que a política estiver associada ao cofre, os backups serão disparados de acordo com o agendamento da política.

O exemplo a seguir habilita a proteção para o compartilhamento de arquivos do Azure **testAzureFileShare** na conta de armazenamento **testStorageAcct**, com a política **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho configurar proteção seja concluído e forneça uma saída semelhante, conforme mostrado.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Disparar um backup sob demanda

Use [backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) para executar um backup sob demanda para um compartilhamento de arquivos do Azure protegido.

1. Recupere a conta de armazenamento e o compartilhamento de arquivos do contêiner no cofre que contém os dados de backup com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar um trabalho de backup, você obtém informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Execute um backup sob demanda com[backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Execute o backup sob demanda da seguinte maneira:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Os instantâneos de compartilhamento de arquivos do Azure são usados enquanto os backups são feitos, geralmente o trabalho é concluído no momento em que o comando retorna essa saída.

### <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política usada para fazer backup do compartilhamento de arquivos do Azure, use [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Especifique o item de backup relevante e a nova política de backup.

O exemplo a seguir altera a política de proteção **testAzureFS** de **dailyafs** para **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Restaurar arquivos e compartilhamentos de arquivos do Azure

Você pode restaurar um compartilhamento de arquivos inteiro ou arquivos específicos no compartilhamento. Você pode restaurar para o local original ou para um local alternativo. 

### <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) para listar todos os pontos de recuperação para o item de backup.

No script a seguir:

- A variável **$RP** é uma matriz de pontos de recuperação para o item de backup selecionado dos últimos sete dias.
- A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice **0**.
- Use a indexação de matriz padrão do PowerShell para escolher o ponto de recuperação.
- No exemplo, **$RP [0]** seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A saída é semelhante à seguinte.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```
Depois que o ponto de recuperação relevante é selecionado, você restaura o compartilhamento de arquivos ou o arquivo para o local original ou para um local alternativo.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar um compartilhamento de arquivos do Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo: 

- **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
- **TargetFileShareName**: Os compartilhamentos de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
- **TargetFolder**: A pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
- **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

Execute o cmdlet com os parâmetros da seguinte maneira:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

O comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um arquivo do Azure para um local alternativo

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo e para identificar exclusivamente o arquivo que você deseja restaurar.

* **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: Os compartilhamentos de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **TargetFolder**: A pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **SourceFilePath**: O caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. Esse caminho é o mesmo caminho usado no cmdlet **Get-AzStorageFile** do PowerShell.
* **SourceFileType**: Se um diretório ou um arquivo está selecionado. Aceita **diretório** ou **arquivo**.
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados somente ao arquivo individual que você deseja restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Esse comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado na seção anterior.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar arquivos e compartilhamentos de arquivos do Azure para o local original

Ao restaurar para um local original, você não precisa especificar parâmetros relacionados ao destino e ao destino. Somente **ResolveConflict** deve ser fornecido.

#### <a name="overwrite-an-azure-file-share"></a>Substituir um compartilhamento de arquivos do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Substituir um arquivo do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Acompanhar trabalhos de backup e restauração

As operações de backup e restauração sob demanda retornam um trabalho junto com uma ID, conforme mostrado quando você [executou um backup sob demanda](#trigger-an-on-demand-backup). Use o cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) para acompanhar o progresso e os detalhes do trabalho.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre como](backup-azure-files.md) fazer backup de arquivos do Azure no portal do Azure.
