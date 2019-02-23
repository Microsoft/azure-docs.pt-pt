---
title: Localize e elimine desanexados discos geridos e não geridos do Azure | Documentos da Microsoft
description: Como encontrar e eliminar do Azure geridos e não geridos (blobs de páginas/VHDs) discos desanexados com o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 705706b011464f9e1f437db7b608b3a73e7c3655
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727388"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Localize e elimine desanexados discos geridos e não geridos do Azure

Quando elimina uma máquina virtual (VM) no Azure, por predefinição, não são eliminados todos os discos que estão ligados à VM. Esta funcionalidade ajuda a evitar a perda de dados devido à exclusão involuntária de VMs. Depois de eliminar uma VM, continuará a pagar pelos discos desanexados. Este artigo mostra-lhe como encontrar e eliminar quaisquer discos desanexados e reduzir custos desnecessários.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos geridos: Localize e elimine discos desanexados

O script a seguir procura desanexados [discos geridos](managed-disks-overview.md) examinando o valor da **ManagedBy** propriedade. Quando um disco gerido está ligado a uma VM, o **ManagedBy** propriedade contém o ID de recurso da VM. Quando um disco gerido é desligado, o **ManagedBy** propriedade é nula. O script examina todos os discos geridos numa subscrição do Azure. Quando o script localiza um disco gerido com o **ManagedBy** propriedade definida como null, o script determina que o disco é desligado.

>[!IMPORTANT]
>Em primeiro lugar, execute o script, definindo a **deleteUnattachedDisks** variável como 0. Esta ação permite-lhe localizar e ver todos os discos geridos ligados.
>
>Depois de analisar todos os discos desanexados, execute novamente o script e defina a **deleteUnattachedDisks** variável como 1. Esta ação permite-lhe eliminar todos os discos geridos ligados.
>

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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não geridos: Localize e elimine discos desanexados 

Discos não geridos são arquivos VHD que são armazenados como [blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) na [contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md). O script a seguir procura desanexados discos não geridos (blobs de páginas), examinando o valor do **LeaseStatus** propriedade. Quando um disco não gerido está ligado a uma VM, o **LeaseStatus** estiver definida como **bloqueado**. Quando um disco não gerido é desligado, o **LeaseStatus** estiver definida como **Unlocked**. O script examina todos os discos não geridos em todas as contas de armazenamento do Azure numa subscrição do Azure. Quando o script localiza um disco não gerido com um **LeaseStatus** definida como **Unlocked**, o script determina que o disco é desligado.

>[!IMPORTANT]
>Em primeiro lugar, execute o script, definindo a **deleteUnattachedVHDs** variável como 0. Esta ação permite-lhe localizar e ver todos os VHDs não geridos ligados.
>
>Depois de analisar todos os discos desanexados, execute novamente o script e defina a **deleteUnattachedVHDs** variável como 1. Esta ação permite-lhe eliminar todos os VHDs não geridos ligados.
>

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte [eliminar conta de armazenamento](../../storage/common/storage-create-storage-account.md) e [identificar órfãos discos utilizando o PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)