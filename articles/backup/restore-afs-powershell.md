---
title: Restaurar ficheiros Azure com PowerShell
description: Neste artigo, aprenda a restaurar os Ficheiros Azure utilizando o serviço de backup Azure e powerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 12bff49bc249b23542534d218b13b517411f461b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756188"
---
# <a name="restore-azure-files-with-powershell"></a>Restaurar ficheiros Azure com PowerShell

Este artigo explica como restaurar uma partilha inteira de ficheiros, ou ficheiros específicos, a partir de um ponto de restauro criado pelo serviço [de backup Azure](backup-overview.md) utilizando o Azure PowerShell.

Pode restaurar uma partilha de ficheiros ou ficheiros específicos da parte. Pode restaurar a localização original, ou para um local alternativo.

> [!WARNING]
> Certifique-se de que a versão PS é atualizada para a versão mínima para 'Az.RecoveryServices 2.6.0' para backups AFS. Para mais informações, consulte [a secção](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) que delineia o requisito para esta alteração.

>[!NOTE]
>O Azure Backup suporta agora a restauração de vários ficheiros ou pastas para o Local original ou alternativo utilizando o PowerShell. Consulte [esta secção](#restore-multiple-files-or-folders-to-original-or-alternate-location) do documento para saber como.

## <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Utilize [o Get-AzRecoveryRecoveryRecoveryPoint Get-AzRecoveryRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) para listar todos os pontos de recuperação para o item back-up.

No seguinte guião:

* A **variável $rp** é uma variedade de pontos de recuperação para o item de backup selecionado dos últimos sete dias.
* A matriz está classificada em ordem inversa com o último ponto de recuperação no índice **0**.
* Utilize a indexação padrão da matriz PowerShell para escolher o ponto de recuperação.
* No exemplo, **$rp[0]** seleciona o último ponto de recuperação.

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

Após a separação do ponto de recuperação relevante, restaure a parte do ficheiro ou o ficheiro para a localização original ou para um local alternativo.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar uma partilha de ficheiros Azure para um local alternativo

Utilize o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar o ponto de recuperação selecionado. Especifique estes parâmetros para identificar a localização alternativa:

* **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo back-up é restaurado. A conta de armazenamento alvo deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O ficheiro partilha dentro da conta de armazenamento-alvo para a qual o conteúdo backed é restaurado.
* **TargetFolder**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de back-up for restaurado para uma pasta de raiz, dê os valores da pasta-alvo como uma cadeia vazia.
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **a sobreescrita** ou **o skip**.

Executar o cmdlet com os parâmetros seguintes:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

O comando devolve um trabalho com uma identificação a ser rastreada, como mostra o exemplo seguinte.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um ficheiro Azure para um local alternativo

Utilize o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar o ponto de recuperação selecionado. Especifique estes parâmetros para identificar a localização alternativa e identificar de forma única o ficheiro que pretende restaurar.

* **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo back-up é restaurado. A conta de armazenamento alvo deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O ficheiro partilha dentro da conta de armazenamento-alvo para a qual o conteúdo backed é restaurado.
* **TargetFolder**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de back-up for restaurado para uma pasta de raiz, dê os valores da pasta-alvo como uma cadeia vazia.
* **SourceFilePath**: O caminho absoluto do ficheiro, a ser restaurado dentro da partilha de ficheiros, como uma cadeia. Este caminho é o mesmo caminho utilizado no cmdlet **Get-AzStorageFile** PowerShell.
* **SourceFileType**: Se um diretório ou um ficheiro são selecionados. Aceita **O Diretório** ou **o Arquivo.**
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **a sobreescrita** ou **o skip**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados apenas com o ficheiro individual que pretende restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Este comando devolve um trabalho com uma identificação a ser rastreada, como mostrado na secção anterior.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar as partilhas e ficheiros de ficheiros azure para a localização original

Quando restaura para um local original, não precisa especificar parâmetros relacionados com o destino e o alvo. Só o **ResolveConflict** deve ser fornecido.

### <a name="overwrite-an-azure-file-share"></a>Sobrepor uma parte de ficheiro Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Sobrepor um ficheiro Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Restaurar vários ficheiros ou pastas para localização original ou alternativa

Utilize o comando [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) passando o caminho de todos os ficheiros ou pastas que pretende restaurar como um valor para o parâmetro **MultipleSourceFilePath.**

### <a name="restore-multiple-files"></a>Restaurar vários ficheiros

No seguinte script, estamos a tentar restaurar os ficheiros *FileSharePage.png* e *MyTestFile.txt.*

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Restaurar vários diretórios

No seguinte guião, estamos a tentar restaurar os *diretórios zrs1_restore* e *restaurar.*

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

Se pretender restaurar vários ficheiros ou pastas para uma localização alternativa, utilize os scripts acima especificando os valores dos parâmetros de localização-alvo, como explicado acima em [Restaurar um ficheiro Azure para uma localização alternativa](#restore-an-azure-file-to-an-alternate-location).

## <a name="next-steps"></a>Passos seguintes

[Saiba restaurar](restore-afs.md) os Ficheiros Azure no portal Azure.
