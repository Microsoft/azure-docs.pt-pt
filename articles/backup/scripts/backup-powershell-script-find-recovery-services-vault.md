---
title: PowerShell Script - encontre o Cofre para conta de armazenamento
description: Saiba como usar um script Azure PowerShell para encontrar o cofre dos serviços de recuperação onde a sua conta de armazenamento está registada.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 56e1b1bc1b9d46abecfbdc4b98385a0349711aab
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648722"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>PowerShell Script para encontrar o Cofre de Serviços de Recuperação onde uma conta de armazenamento está registada

Este script ajuda-o a encontrar o cofre dos serviços de recuperação onde a sua conta de armazenamento está registada.

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

1. Guarde o script acima na sua máquina com um nome à sua escolha. Neste exemplo, guardámo-lo como *FindRegisteredStorageAccount.ps1. *
2. Execute o script fornecendo os seguintes parâmetros:

    * **-Grupo de RecursosName** - Grupo de Recursos da conta de armazenamento
    * **-StorageAccountName** - Nome da conta de armazenamento
    * **-SubscriçãoID** - O ID de subscrição onde a conta de armazenamento está presente.

O exemplo a seguir tenta encontrar o cofre dos serviços de recuperação onde está registada a conta de armazenamento *afsaccount:*

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Resultado

A saída mostrará o percurso completo do cofre dos serviços de recuperação onde a conta de armazenamento está registada. Eis uma saída de exemplo:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer backup Azure File Shares a partir do portal Azure](../backup-afs.md)
