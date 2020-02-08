---
title: Restaurar ficheiros Azure com PowerShell
description: Neste artigo, aprenda a restaurar os Ficheiros Azure utilizando o serviço de backup Azure e powerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086823"
---
# <a name="restore-azure-files-with-powershell"></a>Restaurar ficheiros Azure com PowerShell

Este artigo explica como restaurar uma partilha inteira de ficheiros, ou ficheiros específicos, a partir de um ponto de restauro criado pelo serviço [de backup Azure](backup-overview.md) utilizando o Azure Powershell.

Você pode restaurar um compartilhamento de arquivos inteiro ou arquivos específicos no compartilhamento. Você pode restaurar para o local original ou para um local alternativo.

> [!WARNING]
> Certifique-se de que a versão PS é atualizada para a versão mínima para 'Az.RecoveryServices 2.6.0' para backups AFS. Para mais detalhes, consulte [a secção que](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) delineia o requisito para esta alteração.

## <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Utilize [o Get-AzRecoveryRecoveryRecoveryPoint Get-AzRecoveryRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) para listar todos os pontos de recuperação para o item back-up.

No script a seguir:

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

Depois que o ponto de recuperação relevante é selecionado, você restaura o compartilhamento de arquivos ou o arquivo para o local original ou para um local alternativo.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Restaurar um compartilhamento de arquivos do Azure para um local alternativo

Utilize o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo:

* **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo back-up é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O ficheiro partilha dentro da conta de armazenamento-alvo para a qual o conteúdo backed é restaurado.
* **TargetFolder**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **a sobreescrita** ou **o skip**.

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

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Restaurar um arquivo do Azure para um local alternativo

Utilize o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo e para identificar exclusivamente o arquivo que você deseja restaurar.

* **TargetStorageAccountName**: A conta de armazenamento para a qual o conteúdo back-up é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: O ficheiro partilha dentro da conta de armazenamento-alvo para a qual o conteúdo backed é restaurado.
* **TargetFolder**: A pasta sob a partilha de ficheiros para a qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **SourceFilePath**: O caminho absoluto do ficheiro, a ser restaurado dentro da partilha de ficheiros, como uma cadeia. Este caminho é o mesmo caminho utilizado no cmdlet **Get-AzStorageFile** PowerShell.
* **SourceFileType**: Se um diretório ou um ficheiro são selecionados. Aceita **O Diretório** ou **o Arquivo.**
* **ResolveConflict**: Instrução se houver um conflito com os dados restaurados. Aceita **a sobreescrita** ou **o skip**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados somente ao arquivo individual que você deseja restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Esse comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado na seção anterior.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar arquivos e compartilhamentos de arquivos do Azure para o local original

Ao restaurar para um local original, você não precisa especificar parâmetros relacionados ao destino e ao destino. Só o **ResolveConflict** deve ser fornecido.

#### <a name="overwrite-an-azure-file-share"></a>Substituir um compartilhamento de arquivos do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Substituir um arquivo do Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Passos seguintes

[Saiba restaurar](restore-afs.md) os Ficheiros Azure no portal Azure.
