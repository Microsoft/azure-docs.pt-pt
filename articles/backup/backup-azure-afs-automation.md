---
title: Criar cópias de segurança e restaurar ficheiros do Azure com cópia de segurança do Azure e o PowerShell
description: Criar cópias de segurança e restaurar ficheiros do Azure com cópia de segurança do Azure e o PowerShell.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 83fe8d17699c19d442fd734d71d828eb9fd9d6ed
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258365"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Criar cópias de segurança e restaurar ficheiros do Azure com o PowerShell

Este artigo descreve como utilizar o Azure PowerShell para criar cópias de segurança e recuperar um arquivo de ficheiros do Azure partilha com um [Azure Backup](backup-overview.md) cofre dos serviços de recuperação. 

Este tutorial explica como:

> [!div class="checklist"]
> * Configurar o PowerShell e registar o fornecedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure cópia de segurança para uma partilha de ficheiros do Azure.
> * Execute uma tarefa de cópia de segurança.
> * Restaure uma cópia de segurança de partilha de ficheiros do Azure ou um arquivo individual a partir de uma partilha.
> * Monitorize a cópia de segurança e restaurar trabalhos.


## <a name="before-you-start"></a>Antes de começar

- [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre cofres dos serviços de recuperação.
- Leia sobre as funcionalidades de pré-visualização para [de segurança das partilhas de ficheiros do Azure](backup-azure-files.md).
- Reveja a hierarquia de objetos do PowerShell para serviços de recuperação.


## <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos de serviços de recuperação

A hierarquia do objeto é resumida no diagrama seguinte.

![Hierarquia de objetos de serviços de recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Reveja os **Az.RecoveryServices** [referência de cmdlet](/powershell/module/az.recoveryservices) referência na biblioteca do Azure.


## <a name="set-up-and-install"></a>Configurar e instalar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte forma:

1. [Baixe a versão mais recente do PowerShell de Az](/powershell/azure/install-az-ps). A versão mínima necessária é 1.0.0.

2. Encontre os cmdlets do PowerShell de cópia de segurança do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Rever os aliases e os cmdlets de cópia de segurança do Azure, Azure Site Recovery e o Cofre dos serviços de recuperação são apresentados. Eis um exemplo de que vê. Não é uma lista completa dos cmdlets.

    ![Lista de cmdlets de serviços de recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Inicie sessão na sua conta do Azure com **Connect-AzAccount**.
4. Na página da web que aparece, lhe for pedido para introduzir as credenciais da conta.

    - Em alternativa, pode incluir as credenciais da conta como um parâmetro no **Connect-AzAccount** cmdlet com **-Credential**.
    - Se estiver trabalhando em nome de um inquilino de parceiro CSP, especifica o cliente como um inquilino, utilizando o respetivo nome de domínio primário tenantID ou o inquilino. Um exemplo é **Connect-AzAccount-inquilino** fabrikam.com.

4. Associe a subscrição que pretende utilizar com a conta, uma vez que uma conta pode ter várias subscrições.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se estiver a utilizar o Azure Backup pela primeira vez, utilize o **Register-AzResourceProvider** cmdlet para registar o fornecedor de serviços de recuperação do Azure com a sua subscrição.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Certifique-se de que os fornecedores registado com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. No resultado do comando, certifique-se de que **RegistrationState** é alterado para **registado**. Se não, é executado o **Register-AzResourceProvider** cmdlet novamente.



## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Siga estes passos para criar um cofre dos serviços de recuperação.

- O Cofre dos serviços de recuperação é um recurso do Resource Manager, para que deve colocá-lo dentro de um grupo de recursos. Pode utilizar um grupo de recursos existente ou pode criar um grupo de recursos com o **New-AzResourceGroup** cmdlet. Quando cria um grupo de recursos, especifique o nome e local para o grupo de recursos. 

1. Um cofre é colocado num grupo de recursos. Se não tiver um recurso existente de grupo, criar um novo com o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Neste exemplo, vamos criar um novo grupo de recursos na região E.U.A. oeste.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Utilize o [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet para criar o cofre. Especifique a mesma localização do cofre que foi utilizado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Especifique o tipo de redundância para utilizar o armazenamento do cofre.

   - Pode usar [armazenamento localmente redundante](../storage/common/storage-redundancy-lrs.md) ou [armazenamento georredundante](../storage/common/storage-redundancy-grs.md).
   - O exemplo seguinte define a **- BackupStorageRedundancy** opção para o[conjunto AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) cmd para **testvault** definido como  **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Ver os cofres numa subscrição

Para ver todos os cofres na subscrição, utilize [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

O resultado é semelhante ao seguinte. Tenha em atenção que o grupo de recursos associados e o local são fornecidos.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do Cofre

Store o objeto do cofre numa variável e defina o contexto do cofre.

- Muitos cmdlets de cópia de segurança do Azure requerem o objeto do cofre dos serviços de recuperação como entrada, por isso é conveniente armazenar o objeto do cofre numa variável.
- O contexto do cofre é o tipo de dados protegidos no cofre. Defini-lo com [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). Depois do contexto é definido, aplica-se a todos os cmdlets subsequentes.


O exemplo seguinte define o contexto do cofre para **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Obter o ID do Cofre

Planejamos descontinuar o contexto do cofre definição em conformidade com as diretrizes do PowerShell do Azure. Em vez disso, pode armazenar ou obter o ID do cofre e passá-lo para comandos relevantes, da seguinte forma:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de cópia de segurança

Uma política de cópia de segurança especifica a agenda para cópias de segurança e o período de tempo de pontos de recuperação de cópia de segurança devem ser mantidas:

- Uma política de cópia de segurança está associada a pelo menos uma política de retenção. Uma política de retenção define o tempo que um ponto de recuperação é mantido antes de ser eliminado.
- A através de política de cópia de segurança de retenção do padrão de vista [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- A através de agendamento de política de cópia de segurança do padrão de vista [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Utilizar o [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet para criar uma nova política de cópia de segurança. Introduza os objetos de política de agendamento e retenção.

O exemplo seguinte armazena a política de agendamento e a política de retenção em variáveis. Em seguida, utiliza essas variável como parâmetros para uma nova política (**NewAFSPolicy**). **NewAFSPolicy** demora um backup diário e os retém durante 30 dias.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

O resultado é semelhante ao seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Ativar cópia de segurança

Depois de definir a política de cópia de segurança, pode ativar a proteção para a partilha de ficheiros do Azure através da política.

### <a name="retrieve-a-backup-policy"></a>Obter uma política de cópia de segurança

Obter o objeto de política relevante com [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Utilize este cmdlet para obter uma política específica ou para ver as políticas associadas um tipo de carga de trabalho.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Obter uma política para um tipo de carga de trabalho

O exemplo seguinte obtém as políticas para o tipo de carga de trabalho **AzureFiles**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

O resultado é semelhante ao seguinte.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> O fuso horário dos **BackupTime** campo no PowerShell é tempo Universal Coordenado (UTC). Quando a hora da cópia é apresentada no portal do Azure, o tempo é ajustado para o fuso horário local.

### <a name="retrieve-a-specific-policy"></a>Obter uma política específica

A política de seguinte obtém a política de cópia de segurança com o nome **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Ativar cópia de segurança e aplicar a política

Ativar a proteção com [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Depois da política associada ao cofre, as cópias de segurança são acionadas em conformidade com o agendamento de política.

O exemplo seguinte ativa a proteção para a partilha de ficheiros do Azure **testAzureFileShare** na conta de armazenamento **testStorageAcct**, com a política **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que a tarefa de proteção de configurar seja concluída e fornece um resultado semelhante, conforme mostrado.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Acionar uma cópia de segurança a pedido

Uso [AzRecoveryServicesBackupItem de cópia de segurança](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) para executar uma cópia de segurança a pedido para uma partilha de ficheiros do Azure protegidos.

1. Obter a conta de armazenamento e o ficheiro partilham a partir do contentor no cofre que contém os seus dados de cópia de segurança com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar uma tarefa de cópia de segurança, é obter informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Executar cópias de segurança a pedido com[AzRecoveryServicesBackupItem de cópia de segurança](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Execute a cópia de segurança a pedido da seguinte forma:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando devolve uma tarefa com o ID ser controlados, como mostrado no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Instantâneos de partilha de ficheiros do Azure são utilizados enquanto são executadas as cópias de segurança, então, normalmente a tarefa é concluída quando o comando devolve esta saída.

### <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política utilizada para criar cópias de segurança da partilha de ficheiros do Azure, utilize [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Especifique o item de cópia de segurança relevante e a nova política de cópia de segurança.

As seguintes alterações de exemplo da **testAzureFS** política de proteção do **dailyafs** para **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Restaurar ficheiros e partilhas de ficheiros do Azure

Pode restaurar uma partilha de ficheiros completa ou ficheiros específicos na partilha. Pode restaurar para a localização original ou para uma localização alternativa. 

### <a name="fetch-recovery-points"></a>Obter pontos de recuperação

Uso [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) para listar todos os pontos de recuperação para o item de cópia de segurança.

No seguinte script:

- A variável **$rp** é uma matriz de pontos de recuperação para o item de cópia de segurança selecionado dos últimos sete dias.
- A matriz é ordenada pela ordem inversa de tempo com o ponto de recuperação mais recente no índice **0**.
- Utilize a indexação da matriz de PowerShell padrão para escolher o ponto de recuperação.
- No exemplo, **$rp [0]** seleciona o ponto de recuperação mais recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

O resultado é semelhante ao seguinte.

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
Depois do ponto de recuperação relevante está selecionado, restaurar o ficheiro ou partilha de ficheiros para a localização original ou para uma localização alternativa.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar uma partilha de ficheiros do Azure para uma localização alternativa

Utilize o [restauro AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especifica estes parâmetros para identificar a localização alternativa: 

- **TargetStorageAccountName**: A conta de armazenamento para o qual é restaurado o conteúdo de uma cópia de segurança. A conta de armazenamento de destino tem de ser na mesma localização que o cofre.
- **TargetFileShareName**: As partilhas de ficheiros no armazenamento de destino da conta para que o conteúdo de uma cópia de segurança é restaurado.
- **TargetFolder**: A pasta na partilha de ficheiros para o qual os dados são restaurados. Se o conteúdo de uma cópia de segurança está a ser restaurada para uma pasta de raiz, atribua valores de pasta de destino como uma cadeia vazia.
- **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

Execute o cmdlet com os parâmetros da seguinte forma:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

O comando devolve uma tarefa com o ID ser controlados, como mostrado no exemplo a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um ficheiro do Azure para uma localização alternativa

Utilize o [restauro AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especificar estes parâmetros para identificar a localização alternativa e, para identificar exclusivamente o ficheiro que pretende restaurar.

* **TargetStorageAccountName**: A conta de armazenamento para o qual é restaurado o conteúdo de uma cópia de segurança. A conta de armazenamento de destino tem de ser na mesma localização que o cofre.
* **TargetFileShareName**: As partilhas de ficheiros no armazenamento de destino da conta para que o conteúdo de uma cópia de segurança é restaurado.
* **TargetFolder**: A pasta na partilha de ficheiros para o qual os dados são restaurados. Se o conteúdo de uma cópia de segurança está a ser restaurada para uma pasta de raiz, atribua valores de pasta de destino como uma cadeia vazia.
* **SourceFilePath**: O caminho absoluto do ficheiro, sejam restaurados em partilha de ficheiros, como uma cadeia de caracteres. Este caminho é o mesmo caminho utilizado na **Get-AzStorageFile** cmdlet do PowerShell.
* **SourceFileType**: Se for selecionado um diretório ou um ficheiro. Aceita **diretório** ou **ficheiro**.
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados apenas para o arquivo individual que pretende restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Este comando devolve uma tarefa com o ID ser controlados, como mostrado na secção anterior.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar ficheiros e partilhas de ficheiros do Azure para a localização original

Ao restaurar para uma localização original, não precisa de especificar parâmetros relacionados de destino e de destino. Apenas **ResolveConflict** tem de ser fornecido.

#### <a name="overwrite-an-azure-file-share"></a>Substituir uma partilha de ficheiros do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Substituir um ficheiro do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Controlar a cópia de segurança e restaurar trabalhos

Operações de cópia de segurança e restauro a pedido devolvam uma tarefa, juntamente com uma ID, conforme mostrado quando [foi executada uma cópia de segurança a pedido](#trigger-an-on-demand-backup). Utilize o [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet para controlar o progresso da tarefa e os detalhes.

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
## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre](backup-azure-files.md) cópia de segurança de ficheiros do Azure no portal do Azure.
