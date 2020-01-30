---
title: Restaurar ficheiros Azure com PowerShell
description: Neste artigo, aprenda a restaurar os Ficheiros Azure utilizando o serviço de backup Azure e powerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: c1c116033dbf44e6e1f332195a18c7dfdbcd6c71
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776342"
---
# <a name="restore-azure-files-with-powershell"></a>Restaurar ficheiros Azure com PowerShell

Este artigo explica como restaurar uma partilha inteira de ficheiros, ou ficheiros específicos, a partir de um ponto de restauro criado pelo serviço [de backup Azure](backup-overview.md) utilizando o Azure Powershell.

Você pode restaurar um compartilhamento de arquivos inteiro ou arquivos específicos no compartilhamento. Você pode restaurar para o local original ou para um local alternativo.

## <a name="fetch-recovery-points"></a>Buscar pontos de recuperação

Use [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) para listar todos os pontos de recuperação para o item de backup.

No script a seguir:

* A variável **$RP** é uma matriz de pontos de recuperação para o item de backup selecionado dos últimos sete dias.
* A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice **0**.
* Use a indexação de matriz padrão do PowerShell para escolher o ponto de recuperação.
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

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo:

* **TargetStorageAccountName**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: os compartilhamentos de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **TargetFolder**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **ResolveConflict**: instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

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

Use o [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) para restaurar para o ponto de recuperação selecionado. Especifique esses parâmetros para identificar o local alternativo e para identificar exclusivamente o arquivo que você deseja restaurar.

* **TargetStorageAccountName**: a conta de armazenamento para a qual o conteúdo de backup é restaurado. A conta de armazenamento de destino deve estar no mesmo local que o cofre.
* **TargetFileShareName**: os compartilhamentos de arquivos dentro da conta de armazenamento de destino para a qual o conteúdo de backup é restaurado.
* **TargetFolder**: a pasta sob o compartilhamento de arquivos para o qual os dados são restaurados. Se o conteúdo de backup for restaurado para uma pasta raiz, forneça os valores da pasta de destino como uma cadeia de caracteres vazia.
* **SourceFilePath**: o caminho absoluto do arquivo a ser restaurado no compartilhamento de arquivos, como uma cadeia de caracteres. Esse caminho é o mesmo caminho usado no cmdlet **Get-AzStorageFile** do PowerShell.
* **SourceFileType**: se um diretório ou um arquivo está selecionado. Aceita **diretório** ou **arquivo**.
* **ResolveConflict**: instrução se houver um conflito com os dados restaurados. Aceita **substituir** ou **ignorar**.

Os parâmetros adicionais (SourceFilePath e SourceFileType) estão relacionados somente ao arquivo individual que você deseja restaurar.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Esse comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado na seção anterior.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Restaurar arquivos e compartilhamentos de arquivos do Azure para o local original

Ao restaurar para um local original, você não precisa especificar parâmetros relacionados ao destino e ao destino. Somente **ResolveConflict** deve ser fornecido.

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
