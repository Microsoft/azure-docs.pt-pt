---
title: Gerir cópias de partilha de ficheiros Azure com a PowerShell
description: Aprenda a usar o PowerShell para gerir e monitorizar as ações de ficheiros Azure apoiadas pelo serviço de backup Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 6ee5fb92e4a66a9d6db66514f966c3650d3a4f13
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201982"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Gerir cópias de partilha de ficheiros Azure com a PowerShell

Este artigo descreve como usar o Azure PowerShell para gerir e monitorizar as ações de ficheiro supérbio do Azure que são apoiadas pelo serviço de backup Azure.

> [!WARNING]
> Certifique-se de que a versão PS é atualizada para a versão mínima para 'Az.RecoveryServices 2.6.0' para backups AFS. Para mais detalhes, consulte [a secção que](backup-azure-afs-automation.md#important-notice-backup-item-identification) delineia o requisito para esta alteração.

## <a name="modify-the-protection-policy"></a>Modificar a política de proteção

Para alterar a política utilizada para fazer backup da parte do ficheiro Azure, utilize a [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Especifique o item de backup relevante e a nova política de backup.

O exemplo seguinte altera a política de proteção **testAzureFS** de **dailyafs** para **mensals**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Rastrear backup e restaurar empregos

Backup a pedido e restaurar operações devolvem um trabalho juntamente com uma identificação, como mostra quando [executa um backup](backup-azure-afs-automation.md#trigger-an-on-demand-backup)a pedido . Utilize o [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet para acompanhar o progresso do trabalho e detalhes.

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
* Pare todos os futuros trabalhos de backup, mas *deixe* os pontos de recuperação

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, uma vez que os instantâneos subjacentes criados pela Azure Backup serão mantidos. No entanto, o benefício de deixar os pontos de recuperação é que pode restaurar a parte do ficheiro mais tarde, se desejar. Para obter informações sobre o custo de saída dos pontos de recuperação, consulte os detalhes dos [preços.](https://azure.microsoft.com/pricing/details/storage/files/) Se optar por eliminar todos os pontos de recuperação, não pode restaurar a parte do ficheiro.

## <a name="stop-protection-and-retain-recovery-points"></a>Parar a proteção e reter pontos de recuperação

Para parar a proteção enquanto retém dados, utilize o cmdlet [Desactivação-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)

O exemplo que se segue impede a proteção da parte dos *ficheiros afsfileshare,* mas mantém todos os pontos de recuperação:

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

O atributo de ID de trabalho na saída corresponde ao ID de trabalho criado pelo serviço de backup para a sua operação de "stop protection". Para acompanhar o estado do trabalho, utilize o [cmdlet Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Parar a proteção sem reter pontos de recuperação

Para parar a proteção sem reter pontos de recuperação, utilize o cmdlet [Desactivação-AzRecoveryServicesProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) e adicione o parâmetro **-RemoverRecoveryPoints.**

O exemplo que se segue impede a proteção da parte dos *ficheiros afsfileshare* sem reter pontos de recuperação:

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

[Saiba mais sobre](manage-afs-backup.md) a gestão de backups de partilha de ficheiros Azure no portal Azure.
