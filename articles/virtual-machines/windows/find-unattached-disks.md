---
title: Encontre e elimine discos geridos e não geridos do Azure desanexados
description: Como encontrar e eliminar discos Azure não ligados geridos e não geridos (VHDs/blobs de página) utilizando a Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d1e7c90e558a6834a169b528d2e8c2f96af377b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88705702"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Encontre e elimine discos geridos e não geridos do Azure desanexados

Quando elimina uma máquina virtual (VM) em Azure, por padrão, quaisquer discos que estejam ligados ao VM não são eliminados. Esta funcionalidade ajuda a prevenir a perda de dados devido à eliminação não intencional de VMs. Depois de eliminado um VM, continuará a pagar por discos não ligados. Este artigo mostra-lhe como encontrar e eliminar quaisquer discos não ligados e reduzir custos desnecessários.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos geridos: Localizar e apagar discos não ligados

O seguinte script procura [discos geridos](managed-disks-overview.md) não ligados examinando o valor da propriedade **ManagedBy.** Quando um disco gerido é ligado a um VM, a propriedade **ManagedBy** contém o ID de recurso do VM. Quando um disco gerido não está ligado, a propriedade **ManagedBy** é nula. O script examina todos os discos geridos numa subscrição do Azure. Quando o script localiza um disco gerido com a propriedade **ManagedBy** definida para nula, o script determina que o disco está desapegado.

>[!IMPORTANT]
>Em primeiro lugar, executar o script definindo a variável **deleteUnattachedDisks** para 0. Esta ação permite-lhe encontrar e ver todos os discos geridos desvinculados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e desave a variável **deleteUnattachedDisks** para 1. Esta ação permite eliminar todos os discos geridos não ligados.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não geridos: Encontrar e apagar discos não ligados

Os discos não geridos são ficheiros VHD que são armazenados como [bolhas de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas contas de armazenamento [Azure](../../storage/common/storage-account-overview.md). O seguinte script procura discos não geridos (bolhas de página) examinando o valor da propriedade **LeaseStatus.** Quando um disco não gerido é ligado a um VM, a propriedade **LeaseStatus** está definida para **Bloqueado**. Quando um disco não gerido não é ligado, a propriedade **LeaseStatus** está definida para **Desbloqueado**. O script examina todos os discos não geridos em todas as contas de armazenamento Azure numa subscrição do Azure. Quando o script localiza um disco não gerido com uma propriedade **LeaseStatus** definida para **Desbloqueado,** o script determina que o disco está desapegado.

>[!IMPORTANT]
>Em primeiro lugar, execute o script definindo a variável **deleteUnattachedVHDs** para `$false` . Esta ação permite-lhe encontrar e ver todos os VHDs não geridos desapegados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e desloque a variável **deleteUnattachedVHDs** para `$true` . Esta ação permite eliminar todos os VHDs não geridos desapegados.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [Eliminar uma conta de armazenamento](../../storage/common/storage-account-create.md#delete-a-storage-account) e identificar discos [órfãos usando PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)
