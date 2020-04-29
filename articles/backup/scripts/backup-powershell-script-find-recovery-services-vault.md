---
title: PowerShell Script - encontre Cofre para Conta de Armazenamento
description: Saiba como usar um script Azure PowerShell para encontrar o cofre dos serviços de recuperação onde a sua conta de armazenamento está registada.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76775362"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Powershell Script para encontrar o Cofre de Serviços de Recuperação onde uma conta de armazenamento está registada

Este guião ajuda-o a encontrar o cofre dos serviços de recuperação onde a sua conta de armazenamento está registada.

## <a name="sample-script"></a>Script de exemplo

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Como executar o script

1. Guarde o guião acima na sua máquina com um nome à sua escolha. Neste exemplo, guardámo-lo como *FindRegisteredStorageAccount.ps1*.
2. Executar o script fornecendo os seguintes parâmetros:

    * **-ResourceGroupName** - Grupo de Recursos da conta de armazenamento
    * **-StorageAccountName** - Nome da conta de armazenamento
    * **-Subscrição ID** - O ID de subscrição onde a conta de armazenamento está presente.

O exemplo seguinte tenta encontrar o cofre dos serviços de recuperação onde a conta de armazenamento da *Afsaccount* está registada:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Saída

A saída mostrará o percurso completo do cofre dos serviços de recuperação onde a conta de armazenamento está registada. Eis uma saída de exemplo:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [backup Azure File Shares do portal Azure](https://docs.microsoft.com/azure/backup/backup-afs)
