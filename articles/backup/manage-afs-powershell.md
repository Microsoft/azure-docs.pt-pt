---
title: Gerir cópias de segurança de partilha de ficheiros Azure com a PowerShell
description: Saiba como utilizar o PowerShell para gerir e monitorizar as ações de ficheiros Azure apoiadas pelo serviço Azure Backup.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e2f07e56fb9a8715b1b53165ab5f4b45b4e20ccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89000231"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Gerir cópias de segurança de partilha de ficheiros Azure com a PowerShell

Este artigo descreve como usar a Azure PowerShell para gerir e monitorizar as ações de ficheiros Azure que são apoiadas pelo serviço Azure Backup.

> [!WARNING]
> Certifique-se de que a versão PowerShell está atualizada para a versão mínima para 'Az.RecoveryServices 2.6.0' para cópias de segurança AFS. Para mais detalhes, consulte [a secção](backup-azure-afs-automation.md#important-notice-backup-item-identification) que descreve a exigência desta alteração.

## <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política utilizada para fazer o backup da partilha de ficheiros Azure, utilize [a Proteção de Ficheiros Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Especifique o item de backup relevante e a nova política de backup.

O exemplo a seguir altera a política de proteção **testAzureFS** de **diárias** para **os surdos mensais**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Rastrear backup e restaurar empregos

As operações de backup e restauro a pedido devolvem um emprego juntamente com uma identificação, como mostra quando [se faz um backup a pedido](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Utilize o [cmdlet Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) para acompanhar o progresso do trabalho e detalhes.

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

## <a name="stop-protection-on-a-file-share"></a>Parar a proteção numa partilha de ficheiros

Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

* Pare todos os futuros trabalhos de backup e *elimine* todos os pontos de recuperação
* Parem com todos os futuros trabalhos de apoio, mas *deixem* os pontos de recuperação.

Pode haver um custo associado a deixar os pontos de recuperação armazenados, uma vez que os instantâneos subjacentes criados pela Azure Backup serão mantidos. No entanto, o benefício de deixar os pontos de recuperação é que pode restaurar a partilha do ficheiro mais tarde, se desejar. Para obter informações sobre o custo de sair dos pontos de recuperação, consulte os [detalhes dos preços.](https://azure.microsoft.com/pricing/details/storage/files/) Se optar por eliminar todos os pontos de recuperação, não poderá restaurar a partilha de ficheiros.

## <a name="stop-protection-and-retain-recovery-points"></a>Parar a proteção e reter pontos de recuperação

Para parar a proteção ao reter dados, utilize o cmdlet [de proteção Disable-AzRecoveryServicesBackupProtection.](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)

O exemplo a seguir para a proteção da partilha *de ficheiros afsfileshare,* mas mantém todos os pontos de recuperação:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

O atributo Job ID na saída corresponde ao ID de trabalho do trabalho criado pelo serviço de backup para a sua operação de "stop protection". Para acompanhar o estado do trabalho, utilize o [cmdlet Get-AzRecoveryServicesBackupJob.](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob)

## <a name="stop-protection-without-retaining-recovery-points"></a>Parar a proteção sem reter pontos de recuperação

Para parar a proteção sem reter pontos de recuperação, utilize o cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) e adicione o parâmetro **-RemoveRecoveryPoints.**

O exemplo a seguir impede a proteção da partilha *de ficheiros afsfileshare* sem reter pontos de recuperação:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Passos seguintes

[Saiba como](manage-afs-backup.md) gerir as cópias de segurança do ficheiro Azure no portal Azure.
