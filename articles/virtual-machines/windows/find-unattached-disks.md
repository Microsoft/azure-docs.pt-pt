---
title: Localizar e eliminar discos não ligados do Azure geridos e não geridos
description: Como encontrar e eliminar discos não ligados do Azure geridos e não geridos (VHDs/page blobs) utilizando o Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cac192186c91259a5573dc27442137729816991a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869598"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Localizar e eliminar discos não ligados do Azure geridos e não geridos

Quando elimina uma máquina virtual (VM) em Azure, por padrão, os discos que estejam ligados ao VM não são eliminados. Esta funcionalidade ajuda a prevenir a perda de dados devido à eliminação não intencional de VMs. Depois de um VM ser eliminado, continuará a pagar por discos não ligados. Este artigo mostra-lhe como encontrar e apagar quaisquer discos não ligados e reduzir custos desnecessários.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos geridos: Encontrar e eliminar discos não ligados

O seguinte script procura [discos geridos](managed-disks-overview.md) não ligados examinando o valor da propriedade **ManagedBy.** Quando um disco gerido é ligado a um VM, a propriedade **ManagedBy** contém o ID de recurso do VM. Quando um disco gerido é desligado, a propriedade **ManagedBy** é nula. O script examina todos os discos geridos numa subscrição azure. Quando o script localiza um disco gerido com a propriedade **ManagedBy** definida como nula, o script determina que o disco está desconectado.

>[!IMPORTANT]
>Em primeiro lugar, executar o script definindo a variável **deleteUnattachedDisks** para 0. Esta ação permite-lhe encontrar e ver todos os discos geridos desapegados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e desloque a variável **deleteUnattachedDisks** para 1. Esta ação permite-lhe eliminar todos os discos geridos desapegados.

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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não geridos: Encontrar e eliminar discos não ligados

Os discos não geridos são ficheiros VHD que são [armazenados](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) como bolhas de página nas contas de [armazenamento do Azure](../../storage/common/storage-create-storage-account.md). O seguinte script procura discos não geridos não ligados (bolhas de página) examinando o valor da propriedade **LeaseStatus.** Quando um disco não gerido é ligado a um VM, a propriedade **LeaseStatus** é definida para **Locked**. Quando um disco não gerido é desligado, a propriedade **LeaseStatus** está definida para **Desbloquear**. O script examina todos os discos não geridos em todas as contas de armazenamento azure numa subscrição Azure. Quando o script localiza um disco não gerido com uma propriedade **LeaseStatus** definida para **Desbloquear,** o script determina que o disco está desconectado.

>[!IMPORTANT]
>Em primeiro lugar, executar o script definindo a variável **deleteUnattachedVHDs** para 0. Esta ação permite-lhe encontrar e ver todos os VHDs não ligados.
>
>Depois de rever todos os discos não ligados, volte a executar o script e defina a variável **deleteUnattachedVHDs** para 1. Esta ação permite-lhe eliminar todos os VHDs não ligados não geridos.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
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
                    if($deleteUnattachedVHDs -eq 1){
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

Para mais informações, consulte [Eliminar a conta](../../storage/common/storage-create-storage-account.md) de armazenamento e identificar discos [órfãos usando powershell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
