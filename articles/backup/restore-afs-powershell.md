---
title: Restaurar ficheiros Azure com PowerShell
description: Neste artigo, aprenda a restaurar os Ficheiros Azure utilizando o serviço de backup Azure e o PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021389"
---
# <a name="restore-azure-files-with-powershell"></a>Restaurar ficheiros Azure com PowerShell

Este artigo explica como restaurar uma partilha de ficheiros inteira, ou ficheiros específicos, a partir de um ponto de restauração criado pelo serviço [Azure Backup](backup-overview.md) utilizando a Azure PowerShell.

Pode restaurar uma partilha de ficheiros inteira ou ficheiros específicos sobre a partilha. Você pode restaurar para a localização original, ou para um local alternativo.

> [!WARNING]
> Certifique-se de que a versão PowerShell está atualizada para a versão mínima para 'Az.RecoveryServices 2.6.0' para cópias de segurança AFS. Para mais informações, consulte [a secção](backup-azure-afs-automation.md#important-notice-backup-item-identification) que descreve a exigência desta alteração.

>[!NOTE]
>O Azure Backup suporta agora restaurar vários ficheiros ou pastas para a localização original ou alternativa utilizando o PowerShell. Consulte [esta secção](#restore-multiple-files-or-folders-to-original-or-alternate-location) do documento para saber como.

## <a name="fetch-recovery-points"></a>Pegue pontos de recuperação

Utilize [o Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação para o item com apoio.

No seguinte roteiro:

* A variável **$rp** é um conjunto de pontos de recuperação para o item de backup selecionado dos últimos sete dias.
* A matriz é ordenada em ordem inversa do tempo com o último ponto de recuperação no índice **0**.
* Utilize o indexante padrão da matriz PowerShell para escolher o ponto de recuperação.
* No exemplo, **$rp[0]** seleciona o último ponto de recuperação.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
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

Após a seleção do ponto de recuperação relevante, restaure a partilha de ficheiros ou o ficheiro para a localização original ou para uma localização alternativa.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar uma partilha de ficheiros Azure para uma localização alternativa

Utilize o [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar o ponto de recuperação selecionado. Especifique estes parâmetros para identificar a localização alternativa:

* **TargetStorageAccountName**: A conta de armazenamento à qual o conteúdo de back-up é restaurado. A conta de armazenamento do alvo deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O ficheiro partilha dentro da conta de armazenamento-alvo a que o conteúdo de back-up é restaurado.
* **TargetFolder**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de apoio for restaurado para uma pasta de raiz, dê os valores da pasta-alvo como uma corda vazia.
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Executar o cmdlet com os parâmetros da seguinte forma:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

O comando devolve um trabalho com uma identificação a ser rastreada, como mostra o exemplo seguinte.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um ficheiro Azure para uma localização alternativa

Utilize o [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar o ponto de recuperação selecionado. Especifique estes parâmetros para identificar a localização alternativa e identificar exclusivamente o ficheiro que pretende restaurar.

* **TargetStorageAccountName**: A conta de armazenamento à qual o conteúdo de back-up é restaurado. A conta de armazenamento do alvo deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O ficheiro partilha dentro da conta de armazenamento-alvo a que o conteúdo de back-up é restaurado.
* **TargetFolder**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de apoio for restaurado para uma pasta de raiz, dê os valores da pasta-alvo como uma corda vazia.
* **SourceFilePath**: O caminho absoluto do ficheiro, a restaurar dentro da partilha de ficheiros, como uma cadeia. Este caminho é o mesmo caminho usado no **cmdlet Get-AzStorageFile** PowerShell.
* **SourceFileType**: Se um diretório ou um ficheiro é selecionado. Aceita **Diretório** ou **Arquivo.**
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **Overwrite** ou **Skip**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão apenas relacionados com o ficheiro individual que pretende restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Este comando devolve um trabalho com uma identificação a ser rastreada, como mostrado na secção anterior.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar ações e ficheiros de ficheiros Azure para a localização original

Quando restaura para uma localização original, não precisa de especificar os parâmetros relacionados com o destino e o destino. Apenas **resolveconfiação** deve ser fornecida.

### <a name="overwrite-an-azure-file-share"></a>Overuite uma partilha de ficheiros Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Sobrepor um arquivo Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Restaurar vários ficheiros ou pastas para localização original ou alternativa

Utilize o comando [Restore-AzRecoveryServicesBackupItem,](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) passando o caminho de todos os ficheiros ou pastas que pretende restaurar como um valor para o parâmetro **MultipleSourceFilePath.**

### <a name="restore-multiple-files"></a>Restaurar vários ficheiros

No seguinte guião, estamos a tentar restaurar os *ficheirosFileSharePage.png* e *MyTestFile.txt.*

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Restaurar vários diretórios

No seguinte guião, estamos a tentar restaurar os *diretórios de zrs1_restore* e *Restauro.*

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

O resultado será semelhante ao seguinte:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Se pretender restaurar vários ficheiros ou pastas para uma localização alternativa, utilize as scripts acima especificando os valores dos parâmetros relacionados com a localização do alvo, como explicado acima no [Restaurar um ficheiro Azure para uma localização alternativa](#restore-an-azure-file-to-an-alternate-location).

## <a name="next-steps"></a>Passos seguintes

[Saiba como](restore-afs.md) restaurar os Ficheiros Azure no portal Azure.
