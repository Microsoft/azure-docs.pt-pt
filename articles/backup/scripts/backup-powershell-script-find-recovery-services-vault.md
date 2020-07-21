---
title: PowerShell Script - encontre o Cofre para conta de armazenamento
description: Saiba como usar um script Azure PowerShell para encontrar o cofre dos serviços de recuperação onde a sua conta de armazenamento está registada.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: b343b2f93ed439188c5c0238bf108064f6e132c1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513256"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Powershell Script para encontrar o Cofre de Serviços de Recuperação onde uma conta de armazenamento está registada

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

## <a name="output"></a>Saída

A saída mostrará o percurso completo do cofre dos serviços de recuperação onde a conta de armazenamento está registada. Eis uma saída de exemplo:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Próximos passos

Saiba como [fazer backup Azure File Shares a partir do portal Azure](../backup-afs.md)
